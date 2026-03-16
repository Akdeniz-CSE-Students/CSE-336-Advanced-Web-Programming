


📹 VIDEO TOPIC: Building a Personal AI Avatar Chatbot (Lab 3)
🕐 COVERAGE: Context Injection, System vs. User Prompts, PyPDF2 Text Extraction, and Gradio Chat Interfaces

**🔹 Lab 3 Overview & Directory Structure**
→ The video covers the creation of a personal AI avatar—a chatbot that acts as the user's professional alter ego. The project is structured within a `foundations` folder, containing a sub-directory named `me`. This `me` directory holds personal data files that will serve as the knowledge base for the AI:
1. `LinkedIn.pdf`: A PDF export of the user's LinkedIn profile (obtainable by going to a LinkedIn profile, clicking the ellipsis/more menu, and selecting "Save to PDF").
2. `summary.txt`: A simple text file containing a short, two-sentence personal summary or "fun fact" about the user.
The core task of this lab is to replace the instructor's default documents with the student's own documents to personalize the resulting chatbot.

**🔹 The Role of Tools (Looking Ahead)**
→ The instructor explicitly notes that this lab *does not* use AI Tools or function calling yet. This lab is about laying the groundwork and establishing foundational techniques (like context injection) before introducing the actual Tool framework in the subsequent lessons.

**🔹 Core Python Packages for AI UIs**
→ To build this application, several Python packages are imported. If a developer doesn't know what a package does, the instructor recommends simply asking ChatGPT or Claude for a quick guide. 
*   `dotenv` (`load_dotenv`): Used to load environment variables (like the OpenAI API key) from a `.env` file.
*   `openai` (`OpenAI`): The official OpenAI client library used to communicate with the LLM.
*   `PyPDF2` (`PdfReader`): A popular open-source Python library specifically used for parsing, splitting, merging, and extracting text from PDF files.
*   `gradio` (`gr`): A framework for rapidly building user interfaces for data science and AI applications. The instructor notes that even as a "horrible frontend engineer," Gradio allows developers to create beautiful, functional frontends incredibly easily.

**🔹 PyPI (Python Package Index)**
→ The instructor highlights `pypi.org` as the central repository where open-source Python packages live. If a developer wants to find the canonical source, read documentation, or locate the GitHub repository for packages like `PyPDF2` or `gradio`, PyPI is the standard starting point.

**🔹 Extracting Text from a PDF using PyPDF2**
→ To feed the PDF data to the LLM, the raw text must first be extracted. The instructor demonstrates a simple programmatic pipeline to achieve this using `PyPDF2`.
```python
# Initialize the OpenAI client and load environment variables
from dotenv import load_dotenv
from openai import OpenAI
from PyPDF2 import PdfReader

load_dotenv(override=True)
openai = OpenAI()

# 1. Point the PdfReader at the specific file
reader = PdfReader("me/LinkedIn.pdf")
linkedin = ""

# 2. Iterate through every page in the PDF document
for page in reader.pages:
    # 3. Extract the text from the current page
    text = page.extract_text()
    if text:
        # 4. Append the extracted text to the main string variable
        linkedin += text

# Print to verify the output (results in a large string of resume data)
print(linkedin)
```

**🔹 Loading Supplementary Text Data**
→ Alongside the PDF, the script reads a standard text file containing the personal summary. This is done using standard Python file handling. A variable is also created to hold the user's name, which will be dynamically injected into the prompts.
```python
name = "Ed Donner"

with open("me/summary.txt", "r", encoding="utf-8") as f:
    summary = f.read()
```

**⭐ 🔹 System Prompts vs. User Prompts**
→ Historically, interacting with an LLM often involved passing a single, massive prompt. In modern Agentic systems and APIs, prompts are strictly separated into different roles—specifically the System Prompt and the User Prompt. 
*   **System Prompt:** Provides the overall instructions. It sets the overarching context, the task at hand, the desired format, and the persona or tone the AI should adopt.
*   **User Prompt:** The actual, specific question or input coming directly from the end-user in real-time.
Separating these two allows developers to establish hard boundaries and guidelines (System) that dictate how the AI handles the unpredictable input (User).

> ⭐ **EXAM NOTE:** Understanding the architectural separation between System and User prompts is critical. Exam questions frequently test your ability to recognize that persona, behavior constraints, and static context belong in the System prompt, while dynamic queries belong in the User prompt.

**⭐ 🔹 Context Injection (Building the System Prompt)**
→ To make the LLM act as a specific person, the instructor uses a technique that injects the previously extracted text variables (`name`, `summary`, `linkedin`) directly into a massive Python f-string that serves as the System Prompt.
1.  **Define the Persona:** `"You are acting as {name}. You are answering questions on {name}'s website..."`
2.  **Set the Rules/Tone:** `"Be professional and engaging, as if talking to a potential client... If you don't know the answer, say so."`
3.  **Inject the Data:** The instructor appends the exact contents of the `summary` and `linkedin` variables at the bottom of the prompt, using Markdown tags (like `## Summary` and `## LinkedIn Profile`) to help the LLM structure and understand the raw data.
4.  **Final Instruction:** Reinforce the behavior at the very end: `"With this context, please chat with the user, always staying in character as {name}."`

> ⭐ **EXAM NOTE:** This pattern—dynamically compiling unstructured data (PDFs, text) into a System Prompt to arm an LLM with specific knowledge it wasn't trained on—is the foundational mechanism of context injection/RAG-lite. You must know how context is passed to the LLM.

**⭐ 🔹 Gradio Chat Callback Function Architecture**
→ To connect the LLM to the Gradio chat interface, Gradio requires a specific callback function. This function is triggered every time the user submits a message in the UI. The function signature strictly requires two parameters: `message` (the new text the user just typed) and `history` (an array of prior messages to maintain conversational memory).
```python
def chat(message, history):
    # 1. Construct the messages array for the OpenAI API
    # Start with the System Prompt, add the prior conversation history, and append the latest User message.
    messages =[{"role": "system", "content": system_prompt}] + history + [{"role": "user", "content": message}]
    
    # 2. Call the OpenAI API (using gpt-4o-mini in this example)
    response = openai.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages
    )
    
    # 3. Return the string content generated by the LLM so Gradio can display it
    return response.choices[0].message.content
```

> ⭐ **EXAM NOTE:** You must memorize the message array construction pattern: `[System Message] + [History] +[Current User Message]`. This exact data structure is required by the OpenAI API to maintain both the system instructions and the conversational memory correctly.

**🔹 Launching the Gradio Interface**
→ Once the callback function (`chat`) is defined, bringing up the visual interface requires only a single line of code using Gradio's built-in `ChatInterface` class. The `type="messages"` argument ensures Gradio formats the history to be compatible with modern LLM message arrays.
```python
# Initialize the UI and launch it
gr.ChatInterface(chat, type="messages").launch()
```

**🔹 Testing the AI Avatar**
→ Upon running the `.launch()` command, a visual chat window appears. The instructor tests the system with three progressive questions:
1.  **"Hi there"**: The AI responds with a professional, in-character greeting based on the System Prompt instructions.
2.  **"What is your greatest accomplishment?"**: The AI accurately parses the injected `LinkedIn.pdf` data to discuss co-founding Nebula.io, perfectly maintaining the first-person perspective.
3.  **"What is a challenge that you encountered and needed to overcome?"**: The AI successfully synthesizes a professional narrative based on the text data (transitioning from JPMorgan to a startup), proving that the context injection successfully armed the LLM to act as a highly accurate, professional alter ego.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. System Prompts vs. User Prompts
2. Context Injection (Building the System Prompt)
3. Gradio Chat Callback Function Architecture
