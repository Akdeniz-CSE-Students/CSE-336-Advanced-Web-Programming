```text
📹 VIDEO TOPIC: AutoGen AgentChat Basics & Tool Calling
🕐 COVERAGE: Introduction to AutoGen AgentChat, Models, Messages, Agents, and Tool execution

**⭐ 🔹 AutoGen AgentChat Overview**
→ AutoGen AgentChat is the main component of the AutoGen framework, comparable to frameworks like CrewAI and the OpenAI Agents SDK. It provides a lightweight abstraction layer around calling Large Language Models (LLMs) to build agentic systems. The instructor notes that experiencing AutoGen yields a similar lightweight "aha" moment to using the OpenAI Agents SDK.

> ⭐ **EXAM NOTE:** Understanding where AutoGen AgentChat sits in the ecosystem (comparable to CrewAI/LangGraph) and its lightweight nature is fundamental to architectural choices.

**🔹 Environment Setup**
→ As with most LLM applications, the environment is initialized by loading a `.env` file, typically containing API keys.
```python
from dotenv import load_dotenv
load_dotenv(override=True)
```

**⭐ 🔹 The Model Client (`OpenAIChatCompletionClient`)**
→ The first core concept is the Model, which acts as a wrapper around the underlying LLM. In AutoGen AgentChat, you import a specific client class based on the model provider. For OpenAI models, this is the `OpenAIChatCompletionClient`.
- Instantiating the model simply requires passing the model name.
- **Comparison (Local vs Cloud):** You can easily swap cloud models for local models. By using `OllamaClient` instead of `OpenAIChatCompletionClient`, you can run local models like `llama3.2` using exactly the same execution pattern.
```python
from autogen_ext.models.openai import OpenAIChatCompletionClient

# Cloud model using OpenAI
model_client = OpenAIChatCompletionClient(model="gpt-4o-mini")

# Local model alternative (Ollama)
# from autogen_ext.models.ollama import OllamaClient
# ollama_client = OllamaClient(model="llama3.2")
```

> ⭐ **EXAM NOTE:** You must know how AutoGen abstracts LLM providers using specific client classes like `OpenAIChatCompletionClient` and how easy it is to swap to local execution using `OllamaClient`.

**⭐ 🔹 The Message (`TextMessage`)**
→ The second core concept is how information is passed. Instead of raw strings, AutoGen uses specific object types to represent messages. The most basic form is the `TextMessage`.
- It takes the `content` of the message (the actual text).
- It takes a `source` to identify who sent the message (e.g., "user").
```python
from autogen_agentchat.messages import TextMessage

message = TextMessage(
    content="I'd like to go to London",
    source="user"
)
print(message)
# Outputs: TextMessage(source='user', models_usage=None, ... content="I'd like to go to London")
```

> ⭐ **EXAM NOTE:** Recognizing that AutoGen wraps communication in specific classes like `TextMessage` (rather than raw dictionaries or strings) is crucial for understanding its communication protocol.

**⭐ 🔹 The Agent (`AssistantAgent`)**
→ The third and most fundamental concept is the agent itself. The primary class used in AutoGen AgentChat is `AssistantAgent`.
- You instantiate it by providing a `name` to identify the agent.
- You pass the `model_client` (the LLM wrapper created earlier).
- You provide a `system_message`, which contains the system instructions/prompt for the agent's behavior (e.g., "You give short, humorous answers").
- You can optionally set `model_client_stream=True` to stream the responses back.
```python
from autogen_agentchat.agents import AssistantAgent

agent = AssistantAgent(
    name="airline_agent",
    model_client=model_client,
    system_message="You are a helpful assistant for an airline. You give short, humorous answers.",
    model_client_stream=True
)
```

> ⭐ **EXAM NOTE:** `AssistantAgent` is the core building block of AutoGen. You must know its primary parameters: `name`, `model_client`, and `system_message`.

**⭐ 🔹 Executing the Agent (`on_messages` and `CancellationToken`)**
→ To actually trigger the agent to process a message, you use the `on_messages` method.
- **Asynchronous Execution:** The method is a coroutine (async), so it must be awaited (`await agent.on_messages(...)`).
- **Input:** It expects a list of message objects.
- **CancellationToken:** It requires a `CancellationToken` object. This is described as a "fiddly" framework requirement that AutoGen uses under the hood to know when message processing has finished or if it needs to be interrupted.
```python
from autogen_core import CancellationToken

# Awaiting the async function, passing the message in a list, and including the cancellation token
response = await agent.on_messages(
    [message], 
    cancellation_token=CancellationToken()
)

# Accessing the final text response
print(response.chat_message.content)
```

> ⭐ **EXAM NOTE:** The `on_messages` method is the primary execution entry point. You must remember that it is async (`await`), takes a list of messages, and strictly requires a `CancellationToken`.

**🔹 Building a Local Tool (SQLite Database Example)**
→ To demonstrate tool use, a local SQLite database (`tickets.db`) is created to look up ticket prices.
- **Steps to create the DB tool:**
  1. Delete the existing database file if it exists to ensure a clean slate.
  2. Connect to SQLite and create a `cities` table with `city_name` and `round_trip_price`.
  3. Populate the database with sample cities (London, Paris, Rome, Madrid, Barcelona, Berlin) and prices.
  4. Write a standard Python function `get_city_price(city_name: str) -> float | None` that queries the database and returns the price.
- **Note:** The instructor explicitly mentions that security validation (like checking the city name string to prevent SQL injection) is skipped here because it is just a "toy example."
```python
import sqlite3
import os

# Delete existing DB
if os.path.exists("tickets.db"):
    os.remove("tickets.db")

# Create DB and table
conn = sqlite3.connect("tickets.db")
c = conn.cursor()
c.execute("CREATE TABLE cities (city_name TEXT PRIMARY KEY, round_trip_price REAL)")

# ... DB population code omitted for brevity ...

def get_city_price(city_name: str) -> float | None:
    """Get the roundtrip ticket price to travel to the city."""
    conn = sqlite3.connect("tickets.db")
    c = conn.cursor()
    c.execute("SELECT round_trip_price FROM cities WHERE city_name = ?", (city_name.lower(),))
    result = c.fetchone()
    conn.close()
    return result[0] if result else None

# Testing the function locally
print(get_city_price("London")) # Outputs 299.0
```

**⭐ 🔹 Equipping the Agent with Tools (Raw Python Functions)**
→ Giving an AutoGen agent access to tools is uniquely straightforward compared to other frameworks.
- You pass a list of tools to the `tools` parameter of the `AssistantAgent`.
- **Framework Comparison (AutoGen vs LangGraph/OpenAI SDK):** In other frameworks, you often have to use specific decorators (like `@tool`) or wrap functions in special Tool classes. In AutoGen AgentChat, you simply pass the **raw Python function directly**. AutoGen automatically inspects the Python function signature and its docstring (e.g., `"""Get the roundtrip ticket price..."""`) to figure out the tool description and parameters to pass to the LLM. The instructor highlights this as a specific way AutoGen is superior and more lightweight.
```python
smart_agent = AssistantAgent(
    name="smart_airline_agent",
    model_client=model_client,
    system_message="You are a helpful assistant...",
    tools=[get_city_price], # Passing the raw Python function directly!
    # ...
)
```

> ⭐ **EXAM NOTE:** This is a major differentiator for AutoGen. You must know that AutoGen accepts raw, undecorated Python functions as tools and automatically uses the function signature and docstrings to generate the tool schema for the LLM.

**⭐ 🔹 `reflect_on_tool_use` Parameter**
→ When equipping an agent with a tool, you add `reflect_on_tool_use=True` to the `AssistantAgent`.
- **Explanation:** If this attribute is absent or False, the agent might simply return the raw output of the tool (e.g., returning just the number "299.0") back to the user. By setting it to `True`, you instruct the agent to take the tool's result, reflect on it, and continue processing to generate a natural language conversational response (e.g., "A ticket to London will cost you $299! Just remember your umbrella!").
- The instructor advises to "always assume that will be your default" unless you specifically want raw tool data outputs.
```python
smart_agent = AssistantAgent(
    # ... other parameters ...
    tools=[get_city_price],
    reflect_on_tool_use=True 
)
```

> ⭐ **EXAM NOTE:** Understanding `reflect_on_tool_use` is critical. It controls the agent's routing behavior after a tool is executed—whether it stops and returns raw data, or continues the LLM loop to synthesize a human-readable response.

**⭐ 🔹 Inner Messages (`response.inner_messages`)**
→ When an agent executes tools, it generates "inner messages" that represent the internal steps taken before arriving at the final answer.
- The message construct isn't just for human-to-agent communication; AutoGen uses specific message types for internal agent actions.
- By looping through and inspecting `response.inner_messages`, you can see the exact sequence of events, such as:
  1. **Function Call:** The LLM requesting to use a tool (e.g., `Call(name='get_city_price', arguments={'city_name': 'London'})`).
  2. **Function Execution Result:** The system returning the tool's output back to the LLM (e.g., `FunctionExecutionResult(content='299.0')`).
- Inspecting these is crucial for debugging and understanding the agent's internal thought process.
```python
# Running the tool-equipped agent
response = await smart_agent.on_messages([message], cancellation_token=CancellationToken())

# Inspecting the internal thought process/tool calls
for inner_message in response.inner_messages:
    print(inner_message.content)

# Printing the final synthesized output
print(response.chat_message.content)
```

> ⭐ **EXAM NOTE:** You must understand the concept of `inner_messages`. It is how AutoGen exposes the internal tool-calling loop (the function call requests and the execution results) prior to the final chat message generation.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. AutoGen AgentChat Overview
2. The Model Client (`OpenAIChatCompletionClient`)
3. The Message (`TextMessage`)
4. The Agent (`AssistantAgent`)
5. Executing the Agent (`on_messages` and `CancellationToken`)
6. Equipping the Agent with Tools (Raw Python Functions)
7. `reflect_on_tool_use` Parameter
8. Inner Messages (`response.inner_messages`)
```