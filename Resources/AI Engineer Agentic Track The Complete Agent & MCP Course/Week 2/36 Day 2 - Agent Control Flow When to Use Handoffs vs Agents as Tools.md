


📹 VIDEO TOPIC: Building Agentic Systems
🕐 COVERAGE: Recap of Agent Workflows, Introduction to Handoffs vs. Tools, and Creating a Delegation Pipeline

***

**🔹 Recap: Agent Workflows & Parallel Execution**
→ The instructor begins by summarizing the architecture built in previous steps. The system utilized agents to write Python code and executed multiple agents in parallel using the `asyncio.gather` function (async gather). Once the parallel agents completed their tasks, a subsequent agent was used to evaluate and pick the best resulting email. The system wrapped regular Python functions as tools for the agents to call, and also used an "agent-as-tool" construct (via the `as_tool()` method). This allowed a "Sales Manager" agent to utilize three distinct email-producing agents as tools, eventually sending out the finalized email. 

**⭐ 🔹 Agents as Tools vs. Handoffs (Conceptual Difference)**
→ The instructor introduces **Handoffs**, a construct that is fundamentally similar to "agents as tools" but carries a crucial conceptual and philosophical distinction. 
- **Agent as a Tool:** When an agent uses another agent as a tool, it is utilizing a small feature or helper to complete a sub-task as part of its own broader job.
- **Handoff:** A handoff is when an agent delegates completely, giving total responsibility and ownership of a specialist task to another agent. Instead of just asking for a piece of information to continue its own work, the agent passes the entire job over to a specialist.

> ⭐ **EXAM NOTE:** The conceptual difference between these two patterns is a core architectural concept. You must know that tools are for assisting an agent's current job, while handoffs are for transferring ownership and responsibility of an entire job to a specialized agent.

**⭐ 🔹 Agents as Tools vs. Handoffs (Technical Difference)**
→ Beyond the conceptual mindset, there is a fundamental technical execution difference between how tools and handoffs operate within the control flow of the application.
- **Tools (Request-Response):** Using a tool operates like a standard request-response cycle. The main agent calls the tool, the tool executes, and **control passes back** to the main agent, allowing it to continue its execution sequence.
- **Handoffs (Passing Control):** A handoff involves passing control entirely. The primary agent delegates control to the target agent, and the execution flow **does not come back** to the original caller. The new agent takes over the execution completely.

> ⭐ **EXAM NOTE:** This technical distinction is highly testable. Remember: Tools = control returns to the caller. Handoffs = one-way delegation of control where the flow does not return to the caller.

**🔹 Creating Specialist Agents (`subject_writer` and `html_converter`)**
→ To demonstrate handoffs, a new scenario is constructed requiring an agent to receive an email body, format it, and send it. To support this, two specialist agents are created and immediately wrapped as tools using the `.as_tool()` construct.
1. **Subject Writer Agent:** Given a text message, it generates an email subject line likely to get a response. 
2. **HTML Converter Agent:** Receives a text email body (which may contain markdown, typical of LLM outputs) and converts it into a formatted HTML email with a simple, clear, and compelling layout.

```python
# Creating the Subject Writer Tool
subject_instructions = "You can write a subject for a cold sales email. \n You are given a message and you need to write a subject for an email that is likely to get a response."
subject_writer = Agent(name="Email subject writer", instructions=subject_instructions, model="gpt-4o-mini")
subject_tool = subject_writer.as_tool(tool_name="subject_writer", tool_description="Write a subject for a cold sales email")

# Creating the HTML Converter Tool
html_instructions = "You can convert a text email body to an HTML email body. \n You are given a text email body which might have some markdown \n and you need to convert it to an HTML email body with simple, clear, compelling layout and design."
html_converter = Agent(name="HTML email body converter", instructions=html_instructions, model="gpt-4o-mini")
html_tool = html_converter.as_tool(tool_name="html_converter", tool_description="Convert a text email body to an HTML email body")
```

**🔹 Creating a Python Function Tool (`send_html_email`)**
→ The instructor creates a third tool, this time wrapping a standard Python function rather than an agent. Using a `@function_tool` decorator, a function is defined to take a `subject` and `html_body` as arguments. This function uses the SendGrid API (`sg.client.mail.send.post()`) to physically send the formatted HTML email out to prospects. The instructor notes the practical requirement of ensuring the `from_email` is a verified sender address in SendGrid.

```python
@function_tool
def send_html_email(subject: str, html_body: str) -> dict[str, str]:
    """ Send out an email with the given subject and HTML body to all sales prospects """
    # Code utilizes SendGrid environment variables and client API to send the email
    # ...
```

**⭐ 🔹 The Handoff Agent (`emailer_agent`)**
→ With the three sub-tools created, the instructor builds the actual agent that will serve as the target for the handoff. This agent, named "Email Manager", is given explicit instructions outlining a multi-step pipeline:
1. Receive the body of an email to be sent.
2. Use the `subject_writer` tool to create a subject.
3. Use the `html_converter` tool to convert the body to HTML.
4. Use the `send_html_email` tool to send the finalized payload.
This agent is equipped with the `tools` array containing the three previously defined tools (`[subject_tool, html_tool, send_html_email]`).

> ⭐ **EXAM NOTE:** Be prepared to identify architectures where a "Handoff Agent" acts as an orchestrator for its own isolated set of tools. When handed control, it operates autonomously using its own toolset without returning to the main agent.

**⭐ 🔹 The `handoff_description` Parameter**
→ When defining the `emailer_agent`, a new and specific parameter is introduced: `handoff_description`. The instructor sets this to `"Convert an email to HTML and send it"`. 
- **Purpose:** This property is how the agent announces itself to the rest of the agentic system. 
- **Mechanism:** Just like a tool has a `tool_description` so an LLM knows when to trigger it, an agent provided as a handoff uses the `handoff_description` to inform the primary agent of its capabilities. If the primary agent decides it needs to delegate the formatting and sending of an email, it reads this description to know that the `emailer_agent` is the correct target for delegation.

> ⭐ **EXAM NOTE:** Knowing the exact purpose of `handoff_description` is critical. It is the semantic bridge that allows an LLM to autonomously decide to route a workflow and hand over control to a specific specialist agent.

```python
instructions = "You are an email formatter and sender. You receive the body of an email to be sent. \n You first use the subject_writer tool to write a subject for the email, then use the html_converter tool to convert the body to HTML. \n Finally, you use the send_html_email tool to send the email with the subject and HTML body."

emailer_agent = Agent(
    name="Email Manager",
    instructions=instructions,
    tools=[subject_tool, html_tool, send_html_email],
    model="gpt-4o-mini",
    handoff_description="Convert an email to HTML and send it"
)
```

**🔹 Reviewing System Architecture Arrays**
→ Finally, the instructor clarifies the current state of the global variables holding the system's capabilities before running the application.
- The `tools` array contains `[tool1, tool2, tool3]`, which are the initial 3 sales copywriter agents wrapped purely as request-response tools.
- The `handoffs` array contains `[emailer_agent]`, strictly defining it as an entity to which control will be permanently passed at the end of the primary workflow. 
The instructor prints these arrays to debug and visually verify that the tools are instantiated as function tool wrappers, while the handoff is recognized as a full Agent object equipped with its own internal tools.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Agents as Tools vs. Handoffs (Conceptual Difference)
2. Agents as Tools vs. Handoffs (Technical Difference)
3. The Handoff Agent (`emailer_agent`)
4. The `handoff_description` Parameter
