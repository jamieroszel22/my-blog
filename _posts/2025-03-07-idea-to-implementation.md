---

layout: post
title: "From Idea to Implementation: Building a Recipe Creator with BeeAI - A Development Journey"
date: 2025-03-07
categories: [Python, AI, Tutorial]
comments: true

---

In this tutorial, I'll share the iterative process of developing a Recipe Creator application using the <a href="https://github.com/i-am-bee" target="_blank" rel="noopener noreferrer">BeeAI framework</a>. Rather than presenting a polished, final product, I want to walk through the actual development journey that <a href="https://claude.ai" target="_blank" rel="noopener noreferrer">Claude</a> (my AI coding partner) and I embarked on together. We practiced what I like to call "vibe coding" - a collaborative process where I guided the conceptual direction while Claude helped implement and troubleshoot the technical details. This post highlights the challenges we faced, the solutions we discovered, and the insights we gained along the way as a human-AI coding team.

## The Initial Vision

After discovering BeeAI, a Python framework for building multi-agent systems, I conceptualized a Recipe Creator that would demonstrate the power of specialized agents working together. I shared this vision with Claude, and together we mapped out an ambitious initial concept:

1. A RecipeFinder agent to discover recipes using provided ingredients
2. A NutritionAnalyst agent to check nutrition information
3. A RecipeFormatter agent to create detailed cooking instructions
4. A FinalCompiler agent to present everything in a user-friendly format

However, as with most software projects, the path from concept to working implementation wasn't straightforward. Let me share the development journey that Claude and I navigated together, where I directed the vision and Claude handled the technical implementation details.

## Step 1: Setting Up the Environment

Our first challenge was setting up the Python environment correctly. I encountered several issues with package management, and Claude helped me diagnose and solve these technical hurdles. The BeeAI framework requires Python 3.11 or higher, which added another layer of complexity.

### Environment Setup Challenges

I ran into my first roadblock immediately when trying to run the initial code:

```bash
jamieroszel@jamies-mbp BeeAI % python recipe_creator.py
zsh: command not found: python
```

Claude explained that I needed to specify the correct Python interpreter on macOS:

```bash
jamieroszel@jamies-mbp BeeAI % python3 recipe_creator.py
```

Next, we encountered package installation issues:

```bash
ModuleNotFoundError: No module named 'pydantic'
```

When I tried to install packages directly based on Claude's guidance, I hit an "externally-managed-environment" error:

```bash
error: externally-managed-environment
× This environment is externally managed
```

### Solution: Creating a Virtual Environment

Claude suggested creating a dedicated virtual environment for our project, which solved the issue:

```bash
/opt/homebrew/bin/python3 -m venv beeai_env
source beeai_env/bin/activate
pip install pydantic beeai-framework
```

I shared the terminal outputs with Claude at each step, and together we worked through the errors. After activating the environment, we could properly install dependencies and begin development. This back-and-forth troubleshooting process showcased how effective human-AI pair programming can be - I provided the real-world context and error messages while Claude offered technical solutions.

## Step 2: Understanding the BeeAI Framework

Our next challenge was understanding how the BeeAI framework was actually structured. Claude had provided initial code based on documentation, but when I tried to implement it, we discovered the framework structure differed from what we had expected.

### Exploring the Framework Structure

Claude suggested I write a simple inspection script to explore the framework's structure:

```python
import beeai_framework
import os

# Print the package paths
print(beeai_framework.__path__)

# Print available submodules
print([name for name in dir(beeai_framework) if not name.startswith('_')])

# Check if tools exists as a module
if hasattr(beeai_framework, 'tools'):
    print("Tools module exists")
    # Print tools submodules
    print([name for name in dir(beeai_framework.tools) if not name.startswith('_')])
else:
    print("Tools module doesn't exist directly")
```

I ran this script and shared the output with Claude:

```bash
['/Users/jamieroszel/Documents/BeeAI/beeai_env/lib/python3.13/site-packages/beeai_framework']
['AssistantMessage', 'BaseAgent', 'BaseMemory', 'CustomMessage', 'LoggerError', 'Message', 'OpenMeteoTool', 'Prompt', 'PromptTemplateError', 'ReActAgent', 'ReadOnlyMemory', 'Role', 'Serializable', 'SystemMessage', 'TokenMemory', 'Tool', 'ToolMessage', 'UnconstrainedMemory', 'UserMessage', 'agents', 'backend', 'cancellation', 'context', 'emitter', 'errors', 'logger', 'memory', 'parsers', 'retryable', 'template', 'tool', 'tools', 'utils']
Tools module exists
['StringToolOutput', 'Tool', 'ToolError', 'ToolInputValidationError', 'ToolOutput', 'errors', 'tool', 'weather']
```

This collaborative debugging revealed that the framework had a different structure than we initially assumed - the `Tool` class was directly in the `beeai_framework` package, not in a nested module as Claude had expected based on the documentation. By sharing real runtime information, I helped Claude understand the actual framework structure so we could update our implementation accordingly.

## Step 3: Creating a Custom Tool - Challenges

One of our key goals was to create a custom nutrition tool. Claude had written the code for this feature, but when I tried to implement it, we faced multiple unexpected challenges.

### First Attempt: Import Path Issues

I tried Claude's first implementation of the custom nutrition tool:

```python
from beeai_framework.tools.base import BaseTool

class NutritionTool(BaseTool):
    # Implementation
```

When I ran the code and shared the error with Claude, we discovered an import issue:

```bash
ModuleNotFoundError: No module named 'beeai_framework.tools.base'
```

### Second Attempt: Missing Abstract Methods

Claude revised the approach based on our framework inspection:

```python
from beeai_framework import Tool

class NutritionTool(Tool):
    # Implementation
```

I implemented this change and ran the code again, only to encounter another error:

```bash
TypeError: Can't instantiate abstract class NutritionTool without an implementation for abstract methods '_create_emitter', 'input_schema'
```

This back-and-forth debugging process revealed that custom tools in BeeAI require implementing abstract methods that weren't mentioned in the documentation Claude had referenced.

### Third Attempt: Missing EventEmitter

Claude provided implementations for the required methods:

```python
def _create_emitter(self) -> EventEmitter:
    return EventEmitter()
    
def input_schema(self):
    return Annotated[str, Field(
        description="The name of a food or ingredient"
    )]
```

When I implemented this and shared the new error, we hit another roadblock:

```bash
ImportError: cannot import name 'EventEmitter' from 'beeai_framework.emitter'
```

Each attempt involved me implementing Claude's suggestions, running the code in my environment, and sharing the results. This collaborative debugging process showcased how a human-AI coding team can tackle unexpected framework issues through iterative problem-solving.

## Step 4: Simplifying the Approach

After several iterations and continued challenges with implementing custom tools, I suggested to Claude that we simplify our approach and focus on getting a working MVP before adding more complex features.

### Simplified Workflow Design

Claude proposed, and I approved, reducing our four-agent system to two key agents:

```python
async def create_recipe_workflow(llm: ChatModel) -> AgentWorkflow:
    workflow = AgentWorkflow(name="Recipe Creator")
    
    # 1. Recipe Finder Agent
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="RecipeFinder",
            instructions="""You are a Recipe Finder assistant...""",
            tools=[DuckDuckGoSearchTool()],
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=3),
        )
    )
    
    # 2. Final compiler
    workflow.add_agent(
        agent=AgentFactoryInput(
            name="Compiler",
            instructions="""You are a Recipe Compiler...""",
            llm=llm,
            execution=AgentExecutionConfig(max_iterations=1),
        )
    )
    
    return workflow
```

This was a strategic decision on my part - directing our "vibe coding" session toward a working solution rather than getting bogged down in framework-specific implementation details. The simplified approach focused on using the built-in DuckDuckGoSearchTool, eliminating the need for our custom tool implementation that was causing issues. Claude implemented this simplified version, which I then tested in my environment.

## Step 5: Improving User Experience

Even with this simplified approach, we found that the recipe generation process took a long time. To improve the user experience, we added progress updates:

```python
async def print_progress():
    minute = 1
    while True:
        await asyncio.sleep(60)  # Wait for 1 minute
        print(f"Still working... ({minute} minute{'s' if minute > 1 else ''} elapsed)")
        minute += 1

# Start progress task
progress_task = asyncio.create_task(print_progress())
```

We also added a timeout to prevent excessive waiting:

```python
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
    print("\nThe operation took too long and was terminated.")
finally:
    # Cancel the progress task
    progress_task.cancel()
    try:
        await progress_task
    except asyncio.CancelledError:
        pass
```

## Step 6: Refining Agent Instructions

When I tested the system, I found it was working but the output wasn't as useful as I had hoped - providing general tips but not complete recipes. I shared this feedback with Claude, and together we refined the agent instructions:

```python
# 1. Recipe Finder Agent - more specific instructions
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
```

I implemented this change and found it made a significant difference in the quality of the output. This iterative refinement process highlighted how my domain knowledge about what makes a useful recipe output combined with Claude's ability to implement the technical changes led to a better end result than either of us could have created alone.

## Final Result: Our Working MVP

After multiple iterations and collaborative problem-solving between Claude and me, we arrived at a working MVP that successfully:

1. Takes user-provided ingredients
2. Searches for recipes using those ingredients
3. Formats the recipes with detailed information
4. Presents everything in a user-friendly format

When I ran the final version, I was delighted with the results:

```bash
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

## Key Lessons Learned

Our human-AI development journey taught me several valuable lessons:

1. **Framework Understanding**: The actual structure of a framework may differ from what is initially expected, even by an AI assistant. Taking time to explore and understand it through collaborative debugging is essential.

2. **MVP Approach**: Starting with a simplified version and iteratively adding complexity is often more effective than trying to build everything at once - especially when "vibe coding" with an AI partner.

3. **User Experience**: Even if your backend processing takes time, keeping users informed with progress updates significantly improves the experience - a human insight that improved our technical implementation.

4. **Clear Instructions**: With LLM-powered agents, the quality and specificity of your instructions make a huge difference in the output. My guidance on what information was most important helped Claude craft better agent instructions.

5. **Adaptability**: Being willing to adjust your approach based on what you learn is crucial for successfully implementing new technologies - particularly in human-AI collaboration.

## Next Steps

While our MVP is functional, there are several improvements I'd like to explore with Claude in future coding sessions:

1. **Custom Tool Integration**: Revisit adding a nutrition tool with our better understanding of the framework's requirements
2. **Performance Optimization**: Investigate ways to make the recipe generation process faster
3. **UI Development**: Create a web interface for a more user-friendly experience
4. **Enhanced Features**: Add support for dietary restrictions, cuisine preferences, and portion scaling

## Conclusion

Building the Recipe Creator with BeeAI through collaborative <a href="https://youtu.be/5k2-NOh2tk0?si=4SiV3n37bmFwPOtK" target="_blank" rel="noopener noreferrer">"vibe coding"</a> with Claude was a journey of discovery, problem-solving, and iteration. The process of going from idea to implementation wasn't straightforward, but the challenges we encountered helped me gain a deeper understanding of both the framework and the process of building multi-agent systems.

This type of iterative development between human and AI is a new paradigm in software engineering. As the human, I provided direction, contextual knowledge, and real-world testing, while Claude handled the technical implementation details and suggested solutions.
