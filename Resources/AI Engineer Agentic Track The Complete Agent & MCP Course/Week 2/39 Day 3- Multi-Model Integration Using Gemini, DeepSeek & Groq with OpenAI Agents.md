```text
📹 VIDEO TOPIC: Master AI Agentic Engineering - Week 2 Day 3
🕐 COVERAGE: OpenAI Agents SDK, Non-OpenAI Models, Recaps on Tools and Handoffs

**🔹 Recap: Function Tools**
→ A quick recap of a previous concept: Using a simple Python decorator (`@function_tool` or similar depending on the specific SDK implementation) to wrap a standard Python function into a tool. This allows an AI agent to call the function natively without the developer having to manually write boilerplate JSON schemas to describe the tool to the LLM. 

**🔹 Recap: Agents as Tools**
→ Agents themselves can be converted into tools so that other agents can interact with them. By using an `as_tool()` method (or an equivalent function), an agent's complete set of capabilities and prompt instructions are encapsulated into a callable tool format. This makes agent-to-agent collaboration straightforward, treating another intelligent entity just like a standard programmatic function.

**⭐ 🔹 Handoffs vs. Tool Calling**
→ The instructor highlights a critical architectural distinction regarding how agents collaborate in a multi-agent system. 
- **Tool Calling:** When an agent uses a tool (even if that tool is another wrapped agent), it is essentially making a subroutine call. The calling agent halts its execution, waits for the tool to execute, and expects a **return value** so it can continue its own workflow. 
- **Handoffs:** By contrast, a handoff represents a **permanent transfer of control**. It moves the workflow to the next step by giving over complete execution state and control to the receiving agent. The original calling agent steps back and does not expect the process to return to it.
> ⭐ **EXAM NOTE:** This is a fundamental architectural concept in agentic engineering. Understanding the distinct difference between a nested tool call (expecting a return) and a handoff (transferring control/execution state entirely) is strictly required for designing and answering questions about multi-agent workflows.

**🔹 Upcoming Concepts (Week 2 Day 3 Agenda)**
→ The instructor outlines three extensions to the framework that will be explored:
1. **Models other than OpenAI**: Using the OpenAI Agents SDK to drive and manage alternative models like Gemini, DeepSeek, and Groq.
2. **Structured Outputs**: Forcing an agent to return a predictable, structured data object (like a JSON object populated with specific fields) rather than raw, conversational text.
3. **Guardrails**: Safety and control mechanisms implemented to monitor and filter the information flowing into the agent (inputs) and the information coming out of the agent (outputs).

**🔹 Environment Setup for Multi-Provider Inference**
→ To use models outside the OpenAI ecosystem, you must load their specific API keys into your environment variables. The lab demonstrates loading `GOOGLE_API_KEY`, `DEEPSEEK_API_KEY`, and `GROQ_API_KEY` via a `.env` file using `dotenv`. The instructor also explicitly mentions that a unified routing service like **OpenRouter** could be used as an alternative way to connect to various distinct models using a single service API.

**⭐ 🔹 Using OpenAI-Compatible Endpoints**
→ A major feature of modern LLM inference is that many external AI providers have adopted the standard OpenAI API structure. Because of this architectural alignment, you can use the official OpenAI Agents SDK to communicate with models from other companies (like Google Gemini, DeepSeek, and Groq) simply by pointing the SDK at different URLs. You define custom Base URLs for each respective provider.
```python
GEMINI_BASE_URL = "https://generativelanguage.googleapis.com/v1beta/openai/"
DEEPSEEK_BASE_URL = "https://api.deepseek.com/v1"
GROQ_BASE_URL = "https://api.groq.com/openai/v1"
```
> ⭐ **EXAM NOTE:** Knowing that the OpenAI SDK can act as a universal client for any provider that exposes an "OpenAI-compatible endpoint" is a highly testable, practical concept. It allows developers to seamlessly swap underlying models without rewriting the core SDK integration logic.

**⭐ 🔹 Creating Custom Clients and Model Objects**
→ To route requests to these compatible endpoints, you must execute a two-step process in the SDK. First, you instantiate explicit asynchronous clients (`AsyncOpenAI`) for each provider, passing the custom Base URL and the specific API key. Second, you wrap these clients inside an `OpenAIChatCompletionModel` object, binding the client with the specific string name of the model you intend to invoke.
```python
# Step 1: Create the Custom Async Client
deepseek_client = AsyncOpenAI(
    base_url=DEEPSEEK_BASE_URL, 
    api_key=deepseek_api_key
)

# Step 2: Wrap it in a Model Object
deepseek_model = OpenAIChatCompletionModel(
    name="deepseek-chat", 
    openai_client=deepseek_client
)
```
> ⭐ **EXAM NOTE:** You must understand this precise two-step process to utilize custom endpoints in the OpenAI SDK. Creating an `AsyncOpenAI` client sets the network path (`base_url` and `api_key`), while the `OpenAIChatCompletionModel` binds the specific model identifier string to that network path for the agent to use.

**⭐ 🔹 Model Parameter Assignment: String vs. Object**
→ When instantiating an `Agent` in the SDK, the `model` parameter behaves dynamically depending on the data type passed to it:
- **String input:** If you pass a plain text string (e.g., `model="gpt-4o-mini"`), the SDK assumes you are connecting directly to OpenAI using standard default environment variables (e.g., `OPENAI_API_KEY`).
- **Object input:** If you pass a custom model object (e.g., `model=deepseek_model`), the SDK will ignore defaults and utilize the custom Base URL and API key defined within that specific object's client.
> ⭐ **EXAM NOTE:** This behavioral distinction is critical for configuration and debugging. Expect questions on how the SDK infers the API connection target based strictly on whether a string literal or an `OpenAIChatCompletionModel` object is passed into the agent's constructor.

**🔹 Building a Multi-Model Multi-Agent Workflow**
→ The instructor demonstrates a complex, collaborative system tying everything together, leveraging different LLM providers for different personas within a single application. The workflow consists of multiple steps:
1. **Create Persona Agents:** Three separate sales agents are created with different system instructions.
   - `sales_agent1`: Uses `deepseek_model` (Professional persona).
   - `sales_agent2`: Uses `gemini_model` (Witty persona).
   - `sales_agent3`: Uses `llama3_model` hosted on Groq for fast inference (Concise persona).
2. **Wrap Personas as Tools:** Each of these three agents is converted into a callable tool (`tool1`, `tool2`, `tool3`) using the `as_tool()` concept, allowing a higher-level agent to invoke them.
3. **Create Utility Tools:** The lab reuses utility tools from previous lessons:
   - A standard Python function tool (`send_html_email`).
   - A standard OpenAI `gpt-4o-mini` agent converted into a `subject_tool`.
   - A standard OpenAI `gpt-4o-mini` agent converted into an `html_tool`.
4. **Create Handoff Agent:** An `emailer_agent` is created and equipped with the utility tools. It acts as a specialized worker tasked with formatting and sending the final output.
5. **Create Manager Agent:** A master `sales_manager` agent is created to orchestrate the entire process. It is given access to the three persona tools to generate varying email copies. It is also given a **handoff** to the `emailer_agent`. Once the manager requests drafts and selects the best email copy from the three underlying personas, it permanently transfers control (using the handoff mechanism) to the `emailer_agent` to format to HTML and send the final result.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Handoffs vs. Tool Calling
2. Using OpenAI-Compatible Endpoints
3. Creating Custom Clients and Model Objects
4. Model Parameter Assignment: String vs. Object
```