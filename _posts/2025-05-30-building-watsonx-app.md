---

layout: post
title: "Complete Tutorial: Building a Watsonx Copy Editor with Streamlit"
date: 2025-05-30
categories: [AI, Tutorial]
comments: true

---

## How I built my first AI-powered tool using Cursor and watsonx.ai (without being a full-stack developer)

As a project leader for IBM Redbooks, I spend countless hours reviewing and editing technical content to meet IBM's writing standards. I've always wished for a tool that could automatically apply our style guidelines—active voice, contractions, removing first-person pronouns, and more.

<iframe width="560" height="315" src="https://www.youtube.com/embed/PDHeJe2-YFU" title="Building a Watsonx Copy Editor with Streamlit - Live Demo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Recently, I decided to build exactly that tool using IBM's watsonx.ai platform. Here's the surprising part: I'm not a full-stack developer, I'd never used watsonx.ai before, and honestly, I had no real idea what I was doing as an AI developer or cloud architect. But with Cursor as my coding companion, I was able to build a professional copy editing tool in just a few hours.

The results exceeded my expectations. Not only does the tool solve a real problem I face daily, but the process taught me how approachable AI development has become. I'm excited to share this tutorial and continue iterating on the tool.

## 🎯 What You'll Build

A professional copy editing tool that uses IBM Watsonx AI to automatically improve technical content according to IBM writing guidelines. The tool features a clean web interface where you can paste text and get back professionally edited content with a summary of changes made.

## 📋 Prerequisites

- Python 3.8+ installed
- Basic familiarity with command line
- An IBM Cloud account (free tier works fine)

## 🚀 Step 1: Set Up Your Watsonx Project

### 1.1 Create Your Watsonx Project

1. Navigate to [watsonx.ai](https://watsonx.ai)
2. Click **"Create project"**
3. Name it "Copy Editor" (or whatever you prefer)
4. **Critical:** Make sure to associate a **Watson Machine Learning** service during creation—this is essential for the tool to work

### 1.2 Get Your Project ID

1. Open your project in Watsonx
2. Look at the URL in your browser—it will look like:

   ```txt
   https://dataplatform.cloud.ibm.com/projects/YOUR-PROJECT-ID-HERE/overview?context=wx
   ```

3. Copy the Project ID from the URL (the long string after `/projects/`)
4. Save this—you'll need it later

### 1.3 Generate API Key

1. In your project, go to **Settings** or **Manage**
2. Look for **API Key** or **Access tokens**
3. Generate a new API key
4. **Save this securely**—you'll need it for the app

## 🛠️ Step 2: Set Up Your Development Environment

### 2.1 Create Project Folder

```bash
mkdir copy-editor-tool
cd copy-editor-tool
```

### 2.2 Create API Key File

Create `apikey.json`:

```json
{
    "name": "Copy Editor",
    "description": "",
    "createdAt": "2025-05-28T20:24+0000",
    "apikey": "YOUR-API-KEY-HERE"
}
```

Replace `YOUR-API-KEY-HERE` with the API key you generated in Step 1.3.

### 2.3 Create Requirements File

Create `requirements.txt`:

```txt
streamlit>=1.28.0
ibm-watsonx-ai>=1.0.0
requests>=2.31.0
```

### 2.4 Install Dependencies

```bash
pip install -r requirements.txt
```

## 💻 Step 3: Create the Application

Create `app.py` with the following code. Don't worry if this looks complex—most of it is error handling and user interface setup:

```python
import streamlit as st
import json
import requests
from ibm_watsonx_ai.foundation_models import ModelInference
from ibm_watsonx_ai.credentials import Credentials

# Load API credentials
@st.cache_data
def load_credentials():
    try:
        with open('apikey.json', 'r') as f:
            creds = json.load(f)
        return creds['apikey']
    except Exception as e:
        st.error(f"Error loading API key: {e}")
        return None

# Initialize Watsonx Model
@st.cache_resource
def init_watsonx_model():
    api_key = load_credentials()
    if not api_key:
        return None, "No API key found"
    
    # REPLACE WITH YOUR PROJECT ID
    project_id = "YOUR-PROJECT-ID-HERE"
    
    try:
        credentials = Credentials(
            url="https://us-south.ml.cloud.ibm.com",
            api_key=api_key
        )
        
        model = ModelInference(
            model_id="meta-llama/llama-3-3-70b-instruct",
            params={
                "decoding_method": "greedy",
                "max_new_tokens": 1000,
                "temperature": 0.0,
                "repetition_penalty": 1.1
            },
            credentials=credentials,
            project_id=project_id
        )
        
        return model, "success"
        
    except Exception as e:
        error_msg = str(e)
        if "not associated with a WML instance" in error_msg:
            return None, "wml_missing"
        else:
            return None, f"connection_error: {error_msg}"

# Copy editing prompt - this is where the magic happens
EDITING_PROMPT = """You are an expert editor for IBM technical content. Apply these IBM writing guidelines:

1. Use active voice (not passive voice)
2. Add contractions: "don't", "won't", "can't", "you'll", etc.
3. Remove "we", "our", "us" - use "you" instead
4. Use present tense when possible
5. Use "although" not "while" for contrast
6. Use "because" not "since" for cause
7. Make language more direct and engaging

TEXT TO EDIT:
{text}

Please provide:
1. The revised text
2. A brief summary of the main changes you made"""

def edit_text(model, text):
    """Send text to Watsonx for editing"""
    if not model:
        return "Error: Watsonx model not initialized"
    
    try:
        prompt = EDITING_PROMPT.format(text=text)
        response = model.generate_text(prompt=prompt)
        return response
    except Exception as e:
        return f"Error processing text: {e}"

# Streamlit UI
def main():
    st.set_page_config(page_title="Copy Editor Tool", page_icon="✍️", layout="wide")
    
    st.title("✍️ IBM Copy Editor Tool")
    st.subheader("Technical Content Editor powered by Watsonx")
    
    # Initialize model
    model, status = init_watsonx_model()
    
    # Show setup instructions if WML service is missing
    if status == "wml_missing":
        st.error("🔧 **Setup Required:** Your Watsonx project needs a Watson Machine Learning service")
        
        with st.expander("📋 **How to Fix This (Click to Expand)**", expanded=True):
            st.markdown("""
            ### Step 1: Add Watson Machine Learning Service
            1. Go to your Watsonx project
            2. Click **"Settings"** or **"Manage"** tab
            3. Look for **"Associate services"** or **"Services"** section
            4. Click **"Add service"** → **"Watson Machine Learning"**
            5. Create or select an existing WML instance
            6. Associate it with your project
            
            ### Step 2: Refresh This App
            Once you've added the service, refresh this page and the app should work!
            """)
        
        st.warning("⚠️ **The app will not work until you complete the setup above.**")
        return
    
    elif status.startswith("connection_error"):
        st.error(f"❌ **Connection Error:** {status}")
        st.info("Check your API key and internet connection, then refresh the page.")
        return
    
    elif status != "success":
        st.error(f"❌ **Initialization Error:** {status}")
        return

    # Instructions
    with st.expander("📋 How to Use", expanded=False):
        st.markdown("""
        1. **Paste your text** in the input area below (recommended: 2-3 paragraphs at a time)
        2. **Click 'Edit Text'** to process with IBM's writing guidelines
        3. **Review the results** including summary of changes and full revised text
        4. **Copy the edited text** and repeat with your next chunk
        
        **Pro tip:** For best results, edit 2-3 paragraphs at a time rather than entire documents.
        """)
    
    # Main interface
    col1, col2 = st.columns([1, 1])
    
    with col1:
        st.subheader("📝 Original Text")
        user_text = st.text_area(
            "Paste your technical content here:",
            height=300,
            placeholder="Enter the text you want to edit according to IBM writing guidelines..."
        )
        
        edit_button = st.button("✍️ Edit Text", type="primary", disabled=not model or not user_text)
    
    with col2:
        st.subheader("📋 Edited Results")
        
        if edit_button and user_text and model:
            with st.spinner("Editing your text..."):
                edited_result = edit_text(model, user_text)
            
            if edited_result.startswith("Error"):
                st.error(edited_result)
            else:
                # Display the full response in a text area for easy copying
                st.text_area(
                    "Edited Text and Summary (copy this):",
                    value=edited_result,
                    height=300
                )
                
                st.success("✅ Text edited successfully! Copy the text above.")
        
        elif not model:
            st.info("👆 Complete the setup above to start editing")
        elif not user_text:
            st.info("👆 Enter some text in the left panel to get started")

    # Status info
    st.markdown("---")
    if model:
        st.success("🟢 Connected to Watsonx (Llama 3.3 70B model)")
    else:
        st.error("🔴 Waiting for Watsonx setup completion")

if __name__ == "__main__":
    main()
```

## ⚙️ Step 4: Configure Your App

### 4.1 Add Your Project ID

In `app.py`, find this line:

```python
project_id = "YOUR-PROJECT-ID-HERE"
```

Replace it with your actual Project ID from Step 1.2.

### 4.2 Verify WML Service Association

If you get a "WML instance" error when running the app:

1. Go to your Watsonx project
2. Click **"Manage"** → **"Services & integrations"**
3. If no Watson Machine Learning service is listed:
   - Click **"Associate service"**
   - Add **"Watson Machine Learning"**
   - Create or select an existing instance

## 🚀 Step 5: Run Your App

```bash
streamlit run app.py
```

The app will open automatically in your browser at `http://localhost:8501`

## 🧪 Step 6: Test Your App

Try this sample text that contains common issues the tool fixes:

```txt
The data will be processed by the system once the user has submitted their request. We have designed this feature to ensure that all information is properly validated before it is stored in the database. While this process may take some time, users should not worry about the delay since we are continuously working to improve performance.
```

**Expected improvements:**

- ✅ Passive → Active voice ("The system processes data" instead of "data will be processed")
- ✅ First person removed ("we" → "you" or eliminated entirely)
- ✅ Contractions added ("don't worry" instead of "do not worry")
- ✅ Better word choices ("although" instead of "while", "because" instead of "since")

## 🔧 Troubleshooting

**Problem:** "Model not supported"  
**Solution:** The model ID might have changed. Check the Watsonx documentation for current supported models.

**Problem:** "No WML instance"  
**Solution:** Associate Watson Machine Learning service with your project (see Step 4.2).

**Problem:** "API key error"  
**Solution:** Verify your `apikey.json` file contains the correct API key from your Watsonx project.

**Problem:** No output from editing  
**Solution:** Try shorter text chunks (2-3 paragraphs) or check your internet connection.

## 💡 What I Learned Building This

**On using Cursor:** As someone who doesn't write code regularly, Cursor was a game-changer. It helped me understand complex concepts, suggested improvements, and caught errors I would have missed. It felt like having an experienced developer pair-programming with me.

**On watsonx.ai:** I was surprised by how straightforward it was to get started, even with no prior AI development experience. The hardest part was understanding the project setup requirements, but once configured, the API was intuitive to use.

**On building without expertise:** This project proved that you don't need to be an expert to build useful AI tools. The combination of good documentation, AI-assisted coding, and willingness to experiment can take you far.

## 📚 Key Learning Points

- **Watsonx Project Setup:** Always include Watson Machine Learning service—this caught me initially
- **Model Selection:** Different models excel at different tasks; Llama 3.3 70B works well for editing
- **Prompt Engineering:** Simple, clear prompts often work better than complex ones
- **Error Handling:** Always plan for API and connection issues in production tools
- **User Experience:** Clear instructions and structured output help users understand and trust the tool

## 🎉 Conclusion

Building this copy editor has been  rewarding. It solves a real problem I face daily, and the development process taught me that AI development is more accessible than I initially thought.

The tool isn't perfect—I'm already thinking about improvements and refinements—but it's functional and genuinely useful.

It's interesting how much I can get done without really knowing what I'm doing. I'm not any kind of expert on AI, coding, or cloud computing, but I can spin up a useful app on my own in just a couple hours. Could I make money on this, nope! Does this scale to an enterprise, not likely! But it is pretty great to see what I can do with a vision and a little work.

*Happy coding, and remember: you don't need to be an expert to start building with AI!*
