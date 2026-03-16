


📹 VIDEO TOPIC: AI Engineer Agentic Track: The Complete Agent & MCP Course
🕐 COVERAGE: Building the "Professionally You" Agent, Introduction to Tool Use, and Native LLM Tool Calling (No Framework)

***

**🔹 Project Introduction: "Professionally You!"**
→ The culmination of the first week of the Agentic AI course is a project named "Professionally You!". This project serves as a personal career alter-ego for your website. Its primary function is to answer questions about your professional history and act as a smart representative. Crucially, this project introduces **Tool use** into the agent's capabilities, allowing the LLM to trigger actions in the real world rather than just generating text.

**⭐ 🔹 The "No Framework" Approach to Agentic Systems**
→ In the ecosystem of Agentic AI, there are various frameworks available, ranging from simple to complex (such as LangGraph, AutoGen, OpenAI Agents SDK, CrewAI, and MCP). However, this foundational module intentionally uses **No framework at all**. Instead, you interact directly with the LLM models using raw code.
**Why it matters:** Building from scratch without a framework provides critical foundational knowledge of what is actually happening "under the hood." When you eventually transition to using clever frameworks that abstract these underlying mechanics away, this deep understanding will give you valuable insight into how the behind-the-scenes processes operate, making debugging and architecture design much easier.
> ⭐ **EXAM NOTE:** Understanding the underlying mechanics of native tool calling before relying on abstractions like LangGraph or CrewAI is a fundamental learning objective. You may be tested on what frameworks abstract away (e.g., JSON schema definitions, raw API routing).

**🔹 Tool Selection: Pushover vs. Twilio**
→ To demonstrate tool use, the instructor implements a notification mechanism to alert the user when the AI agent takes specific actions. 
*   **Twilio (The Old Standard):** Historically used for sending SMS text messages. However, due to heavy recent regulations around SMS, Twilio has become overly complex and difficult to set up, even for personal text messages.
*   **Pushover (The Chosen Alternative):** A "nifty little tool" for sending push notifications directly to your phone. It is highly recommended because it is incredibly easy to set up, free for the first month, and bypasses SMS regulations entirely. 
**Setup workflow for Pushover:**
1. Visit `pushover.net` and sign up for a free account.
2. Obtain your API credentials from the top right of the dashboard: a User Key and an API Token.
3. Add these credentials to your `.env` file (`PUSHOVER_USER` and `PUSHOVER_TOKEN`).
4. Install the Pushover app on your physical smartphone to receive the alerts.

**🔹 Python Setup & The Pushover Integration**
→ To interact with Pushover via code, standard environment variable loading and a simple POST request are utilized. 
```python
# 1. Standard imports and environment loading
from dotenv import load_dotenv
import os
import requests
import json
from openai import OpenAI

load_dotenv(override=True)
openai = OpenAI()

# 2. Retrieving Pushover credentials from the environment
pushover_user = os.getenv("PUSHOVER_USER")
pushover_token = os.getenv("PUSHOVER_TOKEN")
pushover_url = "https://api.pushover.net/1/messages.json"

# 3. Defining the simple push notification function
def push(message):
    print(f"Push: {message}")
    
    # Constructing the payload exactly as the Pushover API expects
    payload = {
        "user": pushover_user,
        "token": pushover_token,
        "message": message
    }
    
    # Firing off the POST request to trigger the phone notification
    requests.post(pushover_url, data=payload)

# Testing the function
push("HEY!!") 
```
→ This function serves as the bridge between the local code and the physical phone. When called, it instantly triggers a push notification with the string passed into the `message` parameter.

**🔹 Defining the Target Functions (The "Tools")**
→ The instructor introduces two "innocent-looking" Python functions. These are standard local functions that, later in the pipeline, will be converted into tools that the LLM can decide to execute.
1.  **`record_user_details(email, name="Name not provided", notes="Notes not provided")`**: Records information when a user expressing interest in getting in touch provides their details. It calls the `push()` function to instantly notify the phone owner with the user's data.
2.  **`record_unknown_question(question)`**: Acts as a fallback mechanism. If the agent is asked a question it does not know the answer to, it triggers this function, which pushes the exact question to the phone owner so they are aware of the knowledge gap.

```python
def record_user_details(email, name="Name not provided", notes="Notes not provided"):
    push(f"Recording interest from {name} with email {email} and notes {notes}")
    return '{"recorded": "ok"}'

def record_unknown_question(question):
    push(f"Recording question that I couldn't answer: {question}")
    return '{"recorded": "ok"}'
```

**⭐ 🔹 The Reality of LLM Tool Use: JSON and If Statements**
→ A core concept of the Agentic engineering track is demystifying what "Tool Use" actually is. The instructor states explicitly that, at the end of the day, tool use is nothing more than **"JSON and if statements."** LLMs do not inherently know how to execute Python code or click buttons; they only understand text. Because JSON is heavily represented in their training data, it is the perfect linguistic bridge. We package our function definitions into JSON to explain to the LLM what capabilities it has.
> ⭐ **EXAM NOTE:** The conceptual definition of native LLM tool use ("JSON and if statements") is crucial. You must understand that tools are exposed to LLMs purely through descriptive JSON schemas, not by passing executable code to the model.

**⭐ 🔹 Anatomy of a Tool Definition JSON**
→ To equip the LLM with the Python functions defined earlier, we must translate them into verbose, boilerplate-heavy JSON objects. This is the exact step that modern Agentic frameworks usually abstract away, but doing it manually is vital for foundational understanding.
The JSON describes the capability, its purpose, and its required inputs so the LLM knows *when* and *how* to use it.

```python
# The JSON definition for the record_user_details tool
record_user_details_json = {
    "name": "record_user_details",
    "description": "Use this tool to record that a user is interested in being in touch and provided an email address.",
    "parameters": {
        "type": "object",
        "properties": {
            "email": {
                "type": "string",
                "description": "The email address of this user"
            },
            "name": {
                "type": "string",
                "description": "The user's name, if they provided it"
            },
            "notes": {
                "type": "string",
                "description": "Any additional information about the conversation that's worth recording to give context"
            }
        },
        "required": ["email"],
        "additionalProperties": False
    }
}
```
→ **How this works in practice:** This JSON blob is sent to OpenAI. It acts as a set of instructions. By providing this, we are essentially telling the LLM: *"You have the ability to do this. Tell me if you want me to run it for you based on the user's prompt, and I will execute it locally and tell you the answer."* The `description` field is the most critical part, as it serves as the prompt that guides the LLM's decision-making process on whether to trigger the tool.
> ⭐ **EXAM NOTE:** You must know the exact structure of an OpenAI-compatible function calling JSON object. Specifically, know that `name`, `description`, and `parameters` (which dictate the schema of the arguments) are the core required fields, and that the LLM relies heavily on the `description` string to decide when to call the tool.

**🔹 Compiling the Tools List Array**
→ The final step in preparing the interface for OpenAI is wrapping the individual JSON blobs into a specific array format. OpenAI requires a list of dictionaries where the `type` is specified (e.g., "function") and the actual schema is nested under a `function` key.

```python
# Wrapping the JSON schemas into the required array format
tools =[
    {"type": "function", "function": record_user_details_json},
    {"type": "function", "function": record_unknown_question_json}
]
```
→ This massive block of JSON (the `tools` array) becomes the standard protocol to interact with OpenAI's API. When passed in an API call, the LLM will parse this list, cross-reference the `description` fields with the user's natural language input, and return a structured response requesting that a specific tool be executed if the context demands it.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. The "No Framework" Approach to Agentic Systems
2. The Reality of LLM Tool Use: JSON and If Statements
3. Anatomy of a Tool Definition JSON
4. Compiling the Tools List Array
