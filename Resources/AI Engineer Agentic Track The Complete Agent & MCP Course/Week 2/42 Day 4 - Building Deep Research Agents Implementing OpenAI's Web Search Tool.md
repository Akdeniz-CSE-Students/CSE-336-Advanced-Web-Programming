📹 VIDEO TOPIC: Master AI Agentic Engineering - build Autonomous AI Agents
🕐 COVERAGE: Essential Use-Case: Deep Research, OpenAI Hosted Tools, and Building a Search Agent

**⭐ 🔹 Deep Research Use-Case**
→ Deep Research is presented as one of the absolute classic, cornerstone use cases of agentic AI. It involves deploying an autonomous agent to search the internet, navigate through various links, read content, and synthesize research on a given topic. This workflow is so foundational that major frontier AI labs natively offer it (for example, the "Deep Research" button in OpenAI's ChatGPT interface). When a user clicks that button, the model enters a "deep research mode," which is fundamentally running an agentic workflow behind the scenes.
> ⭐ **EXAM NOTE:** Deep Research is a heavily emphasized foundational design pattern for agents. You must understand it as an archetypal agentic workflow where an LLM leverages tools (like web search) iteratively to gather, synthesize, and summarize external information.

**🔹 Commercial Implications of Deep Research**
→ A Deep Research agent is highly versatile and broadly applicable to almost any business area. It can be specialized to focus exclusively on your specific industry domain, or it can be utilized as a generalized personal "sidekick" for day-to-day activities. Building this gives developers immediate, practical value that mirrors enterprise-grade tools built by frontier AI labs.

**🔹 Jupyter Notebooks vs. Python Modules for Agent Development**
→ In this lab phase, development is done using Jupyter Notebooks (`.ipynb` files) within the Cursor IDE. While production environments typically rely on standard Python modules (`.py` files)—which the course will transition to later—notebooks are utilized here because their interactive format is unparalleled for experimenting, talking through code step-by-step, and rapidly tweaking parameters while observing immediate outputs.

**⭐ 🔹 Hosted Tools (OpenAI)**
→ Hosted Tools represent a major paradigm shift in agent development introduced in this module. Unlike custom-built, locally executed Python functions, Hosted Tools are built, maintained, and executed remotely by the AI provider (in this case, OpenAI) on behalf of the developer.
> ⭐ **EXAM NOTE:** Understanding the distinction between local tools (functions you write and run locally) and Hosted Tools (tools executed natively by the provider's infrastructure) is critical for architectural decision-making and tool configuration questions.

**🔹 The Three OpenAI Hosted Tools**
→ Currently, the OpenAI Agent SDK provides three specific hosted tools:
1. **`WebSearchTool`**: Instructs OpenAI to execute a live web search on behalf of the agent to retrieve current internet data. (This is the focus of this specific lab).
2. **`FileSearchTool`**: Allows the agent to retrieve information from Vector Stores that have been uploaded and hosted remotely on OpenAI's infrastructure.
3. **`ComputerTool`**: Automates computer use tasks, allowing the model to take screenshots and autonomously click around a screen. (The instructor notes that while OpenAI offers this natively, the course will later build a custom, locally-run version of a computer tool rather than using the hosted one).

**🔹 API Pricing and Cost Management for WebSearchTool**
→ OpenAI's `WebSearchTool` is notably expensive. At the time of the recording, calling this tool costs 2.5 cents ($0.025) per call, even when using the lowest-end, cheapest model. In a typical Deep Research loop, an agent might run 10 or more searches, bringing the cost of a single run to 25 cents, and easily accumulating up to $2 to $3 for a single lab session. Developers are advised to monitor costs via the OpenAI pricing page (`platform.openai.com/docs/pricing#web-search`) and can optionally limit their searches or wait for future lessons demonstrating much cheaper, alternative search methods.

**🔹 System Prompt (Instructions) Authority**
→ For the Search Agent, the system prompt explicitly instructs the agent: *"You are a research assistant. Given a search term, you search the web for that term and produce a concise summary of the results..."* The instructor reveals that rather than custom-writing this prompt, it was taken *verbatim* from OpenAI's official documentation for utilizing web search tools. This demonstrates a best practice: utilizing prompt templates officially designed and optimized by the model creators. Grammar is noted as less important in this prompt because the output is meant to be consumed and synthesized by another AI, not directly by an end-user.

**⭐ 🔹 Forcing Tool Execution (`tool_choice="required"`)**
→ When configuring an agent, developers can strip the LLM of its discretion regarding whether or not to use a tool. By explicitly setting the parameter to required, you mandate that the agent must invoke the tool rather than simply responding with its pre-trained knowledge.
> ⭐ **EXAM NOTE:** `tool_choice="required"` is a critical configuration parameter. You will likely be tested on how to guarantee an LLM executes a specific tool rather than relying on its internal weights; this parameter is the exact mechanism to achieve that.

**🔹 Building the Search Agent (Code Implementation)**
→ The Search Agent is instantiated using the `Agent` class, passing in the instructions, the hosted tool, and model settings. 
```python
search_agent = Agent(
    name="Search agent",
    instructions=INSTRUCTIONS,                 // Uses the verbatim OpenAI prompt
    tools=[WebSearchTool(search_context_size="low")], // The OpenAI Hosted Tool
    model="gpt-4o-mini",                       // Using the cheaper model to save costs
    model_settings=ModelSettings(tool_choice="required"), // Mandates the tool MUST be run
)
```
*Code Annotations & Parameters:*
*   `search_context_size="low"`: This is an optional parameter specific to the `WebSearchTool`. It accepts `"low"`, `"medium"` (default), or `"high"`. It directly impacts how much context is retrieved and consequently dictates the price of the call (low being the absolute cheapest at 2.5 cents).
*   `model="gpt-4o-mini"`: Chosen specifically because it is significantly cheaper than the flagship `gpt-4o` model, helping mitigate the high baseline cost of the search tool.

**🔹 Running the Agent and Analyzing Results**
→ To run the agent, the message *"Latest AI Agent frameworks in 2025"* is passed into the Runner:
```python
message = "Latest AI Agent frameworks in 2025"
result = await Runner.run(search_agent, message)
display(Markdown(result.final_output))
```
The output successfully synthesizes web results in markdown format. However, the instructor notes "mixed accuracy" in the results due to AI hallucinations/misinterpretations. For example, while it accurately listed LangGraph, CrewAI, and AutoGen as agent frameworks, it also incorrectly listed *OpenAI Gym* (which is actually a Reinforcement Learning framework, not an agent SDK) and older frameworks like *Rasa*. 

**🔹 OpenAI Traces Dashboard**
→ After execution, developers can view the exact underlying API behavior via the OpenAI Traces dashboard (`platform.openai.com/traces`). Opening the trace for the "Search agent" reveals the exact workflow:
*   **Workflow:** Search agent
*   **Tool Executed:** WebSearch
*   **Metrics:** Execution time and Token usage (Total tokens).
*   **Payloads:** Shows the exact Input (System prompt and User prompt) and Output (the synthesized markdown text) parsed during the run.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Deep Research Use-Case
2. Hosted Tools (OpenAI) vs Local Tools
3. Forcing Tool Execution (`tool_choice="required"`)