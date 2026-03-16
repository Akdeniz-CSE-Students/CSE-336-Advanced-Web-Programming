


📹 VIDEO TOPIC: AI Engineer Agentic Track: The Complete Agent & MCP Course
🕐 COVERAGE: Manual Tool Call Handling & Dynamic Function Routing in Python

***

**⭐ 🔹 The Purpose of Handling Tool Calls**
→ When building agentic systems, giving the Large Language Model (LLM) the ability to use tools is a two-step process. First, you send a JSON schema describing the available tools (e.g., `record_user_details` or `record_unknown_question`) to the LLM. The LLM generates a response where it can opt to run one or more of these tools. However, the LLM cannot execute the tools itself; it merely outputs text (structured JSON) expressing its *intent* to call a tool. The second critical step—often considered the most complex part of the foundation—is writing a handler function in your code to intercept that intent, execute the actual underlying Python function, and return the output back to the LLM. 

> ⭐ **EXAM NOTE:** Understanding that LLMs do not execute code but only return structured intent is a foundational concept in agentic architecture. You must know that the application layer is responsible for parsing this intent and performing the actual execution.

**⭐ 🔹 Anatomy of an LLM Tool Call Response (Structured Output)**
→ When the LLM decides to call a tool, it returns a structured JSON object. This response is highly analogous to standard structured outputs. The object contains a list of tool calls. Each individual tool call within this list has specific properties that must be parsed:
1. **Tool Call ID:** A unique identifier for the specific tool invocation (e.g., `tool_call.id`).
2. **Function Name:** A string representing the name of the tool the LLM wants to execute (e.g., `tool_call.function.name`).
3. **Arguments:** A stringified JSON object containing the parameters the LLM generated to pass into the function (e.g., `tool_call.function.arguments`). 

> ⭐ **EXAM NOTE:** You are likely to be tested on the exact components of an LLM tool call object. Knowing that arguments are returned as a stringified JSON object that must be explicitly parsed (using `json.loads`) is a common practical exam question.

**⭐ 🔹 Handling Multiple Tool Calls (Parallel Tool Calling)**
→ The LLM's response format explicitly returns a *list* of tool calls rather than a single tool call. While an LLM might often choose to use only one tool at a time, the construct is designed to support multiple tools being called simultaneously (parallel tool calling). Therefore, the handler function must always iterate over a list of tool calls using a loop (e.g., `for tool_call in tool_calls:`), ensuring that all requested executions are processed and their results collected.

> ⭐ **EXAM NOTE:** The requirement to iterate over a list of tool calls supports "Parallel Tool Calling." This is a key capability of modern LLMs, and understanding why the handler uses a loop instead of just processing a single object is critical.

**🔹 Extracting Tool Execution Data**
→ Within the loop processing the tool calls, the code must extract the required execution data. The instructor demonstrates the following Python code pattern:
```python
# Extract the name of the function the LLM wants to call
tool_name = tool_call.function.name

# Parse the stringified JSON arguments into a Python dictionary
arguments = json.loads(tool_call.function.arguments)

# Optional: Print for debugging/logging purposes
print(f"Tool called: {tool_name}", flush=True)
```
This step translates the raw LLM output into usable Python variables.

**🔹 The Naive Approach: IF/ELSE Routing**
→ The most direct and explicit way to route the LLM's requested tool name to an actual Python function is by using an `if/elif` statement. The instructor refers to this as the "dumb" or manual way, though it is functionally correct. The code checks the string `tool_name` and manually calls the corresponding function, passing in the unpacked arguments using the `**` kwargs operator:

```python
# The BIG IF STATEMENT
if tool_name == "record_user_details":
    # Call the actual Python function and unpack the arguments dictionary
    result = record_user_details(**arguments)
elif tool_name == "record_unknown_question":
    result = record_unknown_question(**arguments)
```

**⭐ 🔹 Formatting the Tool Response Message**
→ Once the Python function executes and generates a result, that result must be formatted into a specific message structure before it can be appended to the conversation history and sent back to the LLM. The LLM expects tool responses to follow a strict schema so it knows which execution result belongs to which tool call. The message must contain:
- `role`: Must be set to `"tool"`.
- `content`: The output of the function, typically stringified using `json.dumps(result)`.
- `tool_call_id`: The exact ID of the original tool call that triggered this execution (`tool_call.id`).

```python
results.append({
    "role": "tool",
    "content": json.dumps(result),
    "tool_call_id": tool_call.id
})
```

> ⭐ **EXAM NOTE:** The structure of a tool response message is strictly defined. You must know the three required keys: `role` (set to "tool"), `content` (the stringified result), and `tool_call_id` (matching the original call).

**⭐ 🔹 The Dynamic Approach: Python Reflection via `globals()`**
→ To avoid maintaining a massive, clunky `if/elif` block as the number of tools grows, the instructor introduces a more elegant, "sneaky" approach using Python reflection. Because the LLM returns the function name as a string, and the actual Python function shares that exact same name, we can dynamically look up the function reference.
In Python, `globals()` returns a dictionary representing the current global symbol table. This dictionary maps string names to their actual objects (variables, functions, classes). 

**Workflow for Dynamic Routing:**
1. Look up the string `tool_name` in the `globals()` dictionary to get the function reference.
2. Check if the function actually exists.
3. Call the function dynamically, unpacking the parsed arguments.

```python
# Look up the function reference dynamically by its string name
tool = globals().get(tool_name)

# If the function exists in the global scope, execute it
if tool:
    result = tool(**arguments)
```
The instructor demonstrates this by running `globals()["record_unknown_question"]("this is a really hard question")`, which successfully triggers the function and sends a push notification to his phone. He notes that while this is a more elegant codebase, architecturally it is just a "glorified IF statement"—it simply maps text to an executable function.

> ⭐ **EXAM NOTE:** Understanding how to map a string representation of a tool name to actual executable code (whether via `if/else` or dynamic dictionaries like `globals()`) is a fundamental pattern for building tool-calling agents from scratch.

**🔹 Framework Abstraction for Tool Handling**
→ The instructor emphasizes that while writing this manual parsing, routing, and formatting code is required when building foundational implementations from scratch, developers rarely write this code in production after learning the basics. Modern AI frameworks handle this boilerplate automatically. The frameworks wrap this exact logic—extracting the name, parsing JSON arguments, finding the linked function, and formatting the response. The primary reason for teaching this manual implementation is so students understand exactly what is happening under the hood when they eventually use higher-level frameworks.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. The Purpose of Handling Tool Calls
2. Anatomy of an LLM Tool Call Response (Structured Output)
3. Handling Multiple Tool Calls (Parallel Tool Calling)
4. Formatting the Tool Response Message
5. The Dynamic Approach: Python Reflection via `globals()`
