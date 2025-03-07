---

layout: post
title: "Building a Recipe Creator with BeeAI Framework: A Comprehensive Tutorial"
date: 2025-03-07
categories: [Python, AI, Tutorial]
comments: true

---

## Building a Recipe Creator with BeeAI Framework: A Comprehensive Tutorial

In this tutorial, we'll build a practical multi-agent system using the <a href="https://github.com/i-am-bee" target="_blank" rel="noopener noreferrer">BeeAI framework</a>  that can create recipes based on user-provided ingredients. Our Recipe Creator will demonstrate how specialized agents can work together to accomplish a complex task.

## What We'll Build

Our Recipe Creator will:

1. Accept a list of ingredients from the user
2. Search for possible recipes using those ingredients
3. Format the recipes with detailed ingredients and instructions
4. Present everything in a clean, user-friendly format

## Prerequisites

Before we begin, make sure you have:

- Python 3.11 or higher
- A virtual environment set up
- BeeAI framework installed (`pip install beeai-framework`)
- An LLM provider (we'll use <a href="https://ollama.ai/" target="_blank" rel="noopener noreferrer">Ollama</a> with granite3.1-dense:8b in this tutorial)

## Step 1: Setting Up the Project Structure

Let's start by creating a new Python file named `recipe_creator.py` and importing the necessary modules:

```python
import asyncio
import traceback
from typing import List, Optional, Annotated

from pydantic import BaseModel, ValidationError

from beeai_framework.agents.react.agent import AgentExecutionConfig
from beeai_framework.backend.chat import ChatModel
from beeai_framework.backend.message import UserMessage
from beeai_framework.memory import UnconstrainedMemory
from beeai_framework.tools.search.duckduckgo import DuckDuckGoSearchTool
from beeai_framework.workflows.agent import AgentFactoryInput, AgentWorkflow
from beeai_framework.workflows.workflow import WorkflowError
```

## Step 2: Defining Our Workflow Agents

Now, let's define the specialized agents that will work together in our workflow:

```python
async def create_recipe_workflow(llm: ChatModel) -> AgentWorkflow:
    # Create the workflow
    workflow = AgentWorkflow(name="Recipe Creator")
    
    # 1. Recipe Finder Agent - Searches for recipes and formats them
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="RecipeFinder",
            instructions="""You are a Recipe Finder assistant. 
            Your task is to search for and then format recipes based on the ingredients provided by the user.
            
            First, search for 1-2 specific recipes that can be made with the provided ingredients.
            
            Then, for each recipe you find, format it with:
            1. The recipe name as a heading
            2. A list of all ingredients with measurements
            3. Step-by-step cooking instructions
            4. Estimated cooking time
            
            Do not provide general suggestions - return actual, specific recipes with complete details.""",
            tools=[DuckDuckGoSearchTool()],
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=3),
        )
    )
    
    # 2. Final compiler - just does some cleanup and formatting
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="Compiler",
            instructions="""You are a Recipe Compiler.
            Take the recipes found by the RecipeFinder and present them in a clean, readable format.
            Add a brief introduction and any helpful tips, but maintain all the recipe details.""",
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=1),
        )
    )
    
    return workflow
```

In this design, we're using two agents:
- The **RecipeFinder** agent searches for recipes using the provided ingredients and formats them with complete details
- The **Compiler** agent takes the recipes found and presents them in a clean, user-friendly format

## Step 3: Building the Main Function with Progress Updates

Now, let's put everything together in a main function that will run our workflow:

```python
async def main() -> None:
    # Initialize the LLM
    print("Initializing LLM...")
    llm = ChatModel.from_name("ollama:granite3.1-dense:8b")
    
    try:
        # Create the workflow
        print("Creating workflow...")
        workflow = await create_recipe_workflow(llm)
        
        # Get user input
        ingredients = input("Enter ingredients (separated by commas): ")
        prompt = f"Create recipes using these ingredients: {ingredients}"
        
        # Initialize memory and add the user message
        print("Setting up memory...")
        memory = UnconstrainedMemory()
        await memory.add(UserMessage(content=prompt))
        
        # Run the workflow with progress updates
        print("\nGenerating recipes... This may take several minutes.")
        print("The RecipeFinder agent is searching for recipes...")
        
        # Set a timer to provide updates
        start_time = asyncio.get_event_loop().time()
        
        async def print_progress():
            minute = 1
            while True:
                await asyncio.sleep(60)  # Wait for 1 minute
                print(f"Still working... ({minute} minute{'s' if minute > 1 else ''} elapsed)")
                minute += 1
        
        # Start progress task
        progress_task = asyncio.create_task(print_progress())
        
        try:
            # Run the workflow with a timeout
            response = await asyncio.wait_for(
                workflow.run(messages=memory.messages),
                timeout=600  # 10 minute timeout
            )
            
            # Display the final result
            print("\n===== Your Recipe Ideas =====\n")
            print(response.state.final_answer)
            
        except asyncio.TimeoutError:
            print("\nThe operation took too long and was terminated. Please try again with fewer ingredients or a simpler request.")
        finally:
            # Cancel the progress task
            progress_task.cancel()
            try:
                await progress_task
            except asyncio.CancelledError:
                pass
        
    except WorkflowError:
        print("An error occurred in the workflow:")
        traceback.print_exc()
    except ValidationError:
        print("A validation error occurred:")
        traceback.print_exc()
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        traceback.print_exc()


if __name__ == "__main__":
    asyncio.run(main())
```

This main function includes several user experience enhancements:

- Progress updates every minute so users know the system is still working
- A 10-minute timeout to prevent excessively long runs
- Clear status messages throughout the process

## Complete Code

Here's the complete code for our Recipe Creator:

```python
import asyncio
import traceback
from typing import List, Optional, Annotated

from pydantic import BaseModel, ValidationError

from beeai_framework.agents.react.agent import AgentExecutionConfig
from beeai_framework.backend.chat import ChatModel
from beeai_framework.backend.message import UserMessage
from beeai_framework.memory import UnconstrainedMemory
from beeai_framework.tools.search.duckduckgo import DuckDuckGoSearchTool
from beeai_framework.workflows.agent import AgentFactoryInput, AgentWorkflow
from beeai_framework.workflows.workflow import WorkflowError


class NutritionInfo(BaseModel):
    calories: Optional[int] = None
    protein: Optional[float] = None
    carbs: Optional[float] = None
    fat: Optional[float] = None


async def create_recipe_workflow(llm: ChatModel) -> AgentWorkflow:
    # Create the workflow
    workflow = AgentWorkflow(name="Recipe Creator")
    
    # 1. Recipe Finder Agent - Searches for recipes and formats them
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="RecipeFinder",
            instructions="""You are a Recipe Finder assistant. 
            Your task is to search for and then format recipes based on the ingredients provided by the user.
            
            First, search for 1-2 specific recipes that can be made with the provided ingredients.
            
            Then, for each recipe you find, format it with:
            1. The recipe name as a heading
            2. A list of all ingredients with measurements
            3. Step-by-step cooking instructions
            4. Estimated cooking time
            
            Do not provide general suggestions - return actual, specific recipes with complete details.""",
            tools=[DuckDuckGoSearchTool()],
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=3),
        )
    )
    
    # 2. Final compiler - just does some cleanup and formatting
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="Compiler",
            instructions="""You are a Recipe Compiler.
            Take the recipes found by the RecipeFinder and present them in a clean, readable format.
            Add a brief introduction and any helpful tips, but maintain all the recipe details.""",
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=1),
        )
    )
    
    return workflow


async def main() -> None:
    # Initialize the LLM
    print("Initializing LLM...")
    llm = ChatModel.from_name("ollama:granite3.1-dense:8b")
    
    try:
        # Create the workflow
        print("Creating workflow...")
        workflow = await create_recipe_workflow(llm)
        
        # Get user input
        ingredients = input("Enter ingredients (separated by commas): ")
        prompt = f"Create recipes using these ingredients: {ingredients}"
        
        # Initialize memory and add the user message
        print("Setting up memory...")
        memory = UnconstrainedMemory()
        await memory.add(UserMessage(content=prompt))
        
        # Run the workflow with progress updates
        print("\nGenerating recipes... This may take several minutes.")
        print("The RecipeFinder agent is searching for recipes...")
        
        # Set a timer to provide updates
        start_time = asyncio.get_event_loop().time()
        
        async def print_progress():
            minute = 1
            while True:
                await asyncio.sleep(60)  # Wait for 1 minute
                print(f"Still working... ({minute} minute{'s' if minute > 1 else ''} elapsed)")
                minute += 1
        
        # Start progress task
        progress_task = asyncio.create_task(print_progress())
        
        try:
            # Run the workflow with a timeout
            response = await asyncio.wait_for(
                workflow.run(messages=memory.messages),
                timeout=600  # 10 minute timeout
            )
            
            # Display the final result
            print("\n===== Your Recipe Ideas =====\n")
            print(response.state.final_answer)
            
        except asyncio.TimeoutError:
            print("\nThe operation took too long and was terminated. Please try again with fewer ingredients or a simpler request.")
        finally:
            # Cancel the progress task
            progress_task.cancel()
            try:
                await progress_task
            except asyncio.CancelledError:
                pass
        
    except WorkflowError:
        print("An error occurred in the workflow:")
        traceback.print_exc()
    except ValidationError:
        print("A validation error occurred:")
        traceback.print_exc()
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        traceback.print_exc()


if __name__ == "__main__":
    asyncio.run(main())
```

## Running and Testing

Now that we have our complete `recipe_creator.py` file, let's run it and test our multi-agent system:

```bash
$ python recipe_creator.py
Initializing LLM...
Creating workflow...
Enter ingredients (separated by commas): ground beef, tomatoes, yellow onion, avocado
Setting up memory...

Generating recipes... This may take several minutes.
The RecipeFinder agent is searching for recipes...
Still working... (1 minute elapsed)

===== Your Recipe Ideas =====

Here are three recipes using ground beef, tomatoes, and yellow onion with optional avocado topping:
1. **Ground Beef and Tomato Stew**
   - Ingredients: 1 lb lean ground beef, 2 large tomatoes (diced), 1 yellow onion (finely chopped), 1 tbsp olive oil, salt, pepper, garlic powder, and fresh cilantro.
   - Instructions: Heat the oil in a pan over medium heat. Add the onions and cook until softened. Add ground beef, breaking it up as it browns. Stir in tomatoes, season with salt, pepper, and garlic powder. Simmer for 20 minutes. Serve garnished with cilantro.
2. **Keto-Friendly Ground Beef Fajitas**
   - Ingredients: 1 lb ground beef, 2 large tomatoes (diced), 1 yellow onion (sliced), 1 avocado (sliced), salt, pepper, chili powder, cumin, and lime juice.
   - Instructions: Cook the ground beef in a pan over medium heat until browned. Add sliced onions and tomatoes, cooking until softened. Season with salt, pepper, chili powder, and cumin. Serve in lettuce wraps or tortillas, topped with avocado slices and lime juice.
3. **Ground Beef Chile Verde**
   - Ingredients: 1 lb ground beef, 2 large tomatoes (diced), 1 yellow onion (finely chopped), 1 chipotle pepper in adobo sauce, 1 cup beef broth, salt, and fresh cilantro.
   - Instructions: Cook the ground beef in a pan over medium heat until browned. Add onions and cook until softened. Stir in tomatoes, chipotle pepper with its sauce, and beef broth. Simmer for 20 minutes. Season with salt and garnish with cilantro. Serve with warm chile broth or crema.
For an optional avocado topping, simply slice the avocado and sprinkle it with lime juice before serving on top of any of these dishes. Enjoy!
```

## Understanding How It Works

Our Recipe Creator demonstrates several key concepts of the BeeAI framework:

### 1. Agent Specialization

Each agent in our workflow has a specific role:

- **RecipeFinder**: Searches for and formats recipes based on provided ingredients
- **Compiler**: Presents the recipes in a clean, user-friendly format

### 2. Tool Integration

We've integrated the DuckDuckGoSearchTool, which allows our RecipeFinder agent to search the web for recipes.

### 3. Workflow Orchestration

The BeeAI framework handles the complex orchestration:

- Passing information between agents
- Managing memory and context
- Handling errors and retries
- Compiling the final response

### 4. User Experience

We've added several features to improve the user experience:

- Status messages to keep users informed
- Progress updates every minute
- A timeout to prevent excessively long processing times
- Comprehensive error handling

## Extending the Project

Here are some ways you could extend this project:

1. **API Integration**: Add a nutritional information API to provide calorie and nutrient data
2. **User Preferences**: Add support for dietary restrictions or preferences
3. **More Agents**: Add specialized agents for cuisine styles, wine pairing, etc.
4. **Feedback Loop**: Implement a system for users to rate recipes and improve suggestions
5. **Image Generation**: Integrate with an image generation API to create visuals of the dishes

## Conclusion

In this tutorial, we've built a practical Recipe Creator using the BeeAI framework. We've seen how multiple specialized agents can work together to create a system that's greater than the sum of its parts.

The BeeAI framework makes it easy to build complex multi-agent systems by providing a robust infrastructure for agent orchestration, tool integration, and memory management. By defining clear roles for each agent and providing appropriate tools, we can create powerful AI assistants for a wide range of tasks.

This example shows just one application of multi-agent systems. The same principles can be applied to create assistants for research, content creation, data analysis, and many other domains.

Happy coding and happy cooking!