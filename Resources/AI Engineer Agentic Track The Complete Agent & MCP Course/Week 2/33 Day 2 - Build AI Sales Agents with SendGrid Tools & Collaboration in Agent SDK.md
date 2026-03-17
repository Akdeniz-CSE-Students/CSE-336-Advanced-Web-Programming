📹 VIDEO TOPIC: OpenAI Agents SDK - Building an Automated Sales Outreach Workflow
🕐 COVERAGE: Agentic Architecture Layers, SendGrid Tool Setup, System Prompts, Agent Instantiation, and Asynchronous Streaming Execution

**⭐ 🔹 Architectural Layers of Agentic Systems**
→ The project involves building an automated Sales Development Representative (SDR) and introduces three distinct layers of agentic architecture. When building agentic systems, developers typically progress through these levels of complexity:
1. **A simple workflow of Agent calls:** Basic sequential or orchestrated calls to agents without external integrations.
2. **An agent that can use a tool:** Enhancing an agent's capabilities by allowing it to call external functions (e.g., sending an email via an API).
3. **Agents that can call on other agents (Agent Collaboration):** Orchestrating multiple agents to work together.
> ⭐ **EXAM NOTE:** Understanding these three progressive layers of agentic architecture is highly likely to be tested, as it forms the foundational roadmap for moving from simple LLM calls to complex autonomous systems.

**⭐ 🔹 Agents Calling Agents: Tools vs. Handoffs**
→ When setting up multi-agent collaboration (layer 3 of the architecture), the instructor notes there are two primary design patterns for how agents interact with one another:
1. **Treating agents as Tools:** One agent treats another agent exactly like a functional tool it can execute to get a specific result.
2. **Using Handoffs:** One agent explicitly transfers control and context of the workflow to another agent.
> ⭐ **EXAM NOTE:** This comparison is a core architectural concept in multi-agent engineering. Expect questions testing your ability to distinguish between treating a subordinate agent as a tool versus executing a full workflow handoff.

**🔹 SendGrid Tool Integration Setup**
→ To enable the agent to send emails, the course utilizes SendGrid (owned by Twilio, comparable to SparkPost). SendGrid is used specifically for sending transactional emails from servers. To set it up for agentic tool use, three steps are required:
1. **Generate an API Key:** Created in the SendGrid Settings menu and copied to the clipboard.
2. **Verify a Single Sender:** In the Sender Authentication tab, verify your own email address to prove ownership. For this experimental setup, the agent will only send "cold emails" back to this verified address rather than out to the real world.
3. **Environment Variable Configuration:** Add the copied API key to the project's `.env` file as `SENDGRID_API_KEY=your_key_here`.

**🔹 Required Code Imports for OpenAI Agents SDK**
→ The project requires specific imports to function, combining environment variables, Open AI Agents SDK classes, and SendGrid libraries:
- `dotenv`: To load the `.env` file overriding existing variables (`load_dotenv(override=True)`).
- `from agents import Agent, Runner, trace, function_tool`: Core building blocks of the OpenAI Agents SDK.
- `from openai.types.responses import ResponseTextDeltaEvent`: A crucial import required specifically for streaming text back bit-by-bit from the LLM.
- `SendGrid` imports: To handle the actual email formatting and dispatching.

**🔹 System Prompts, Context Setting, and Personas**
→ To test different agent behaviors, the instructor creates three distinct system prompts (instructions) for three different SDR personas. Proper prompt engineering is required to frame the agent's context, tone, mood, and background information.
- **The Context:** All agents work for "ComplAI", a mock SaaS company that provides a tool for ensuring SOC2 compliance and preparing for audits.
- **Instruction 1 (Professional):** Writes professional, serious cold emails.
- **Instruction 2 (Engaging):** Writes humorous, engaging, witty emails likely to get a response.
- **Instruction 3 (Concise):** Writes concise, to-the-point, busy-agent cold emails.

**🔹 Instantiating OpenAI Agents**
→ Using the OpenAI Agents SDK, agents are instantiated as Python objects by passing in their specific configurations. The instructor purposefully uses `gpt-4o-mini` to keep API costs cheap during experimentation.
```python
sales_agent1 = Agent(
    name="Professional Sales Agent",
    instructions=instructions1, # The system prompt defined earlier
    model="gpt-4o-mini"
)

# This pattern is repeated for sales_agent2 and sales_agent3 
# using their respective instruction variables.
```

**⭐ 🔹 Streaming Agent Responses (Async Execution vs. Standard Run)**
→ Executing an agent to get a real-time, typewriter-like output requires a different approach than a standard synchronous execution. Instead of using `Runner.run()`, developers must use `Runner.run_streamed()`. Because streaming returns data in chunks over time, it returns a **coroutine** rather than a completed string response.
To handle this coroutine, you cannot simply print the result; you must use asynchronous Python (`async for`) to iterate over the stream of events.
> ⭐ **EXAM NOTE:** This is a crucial implementation detail. You must know that streaming responses in the SDK requires `Runner.run_streamed()`, returns a coroutine, necessitates asynchronous iteration (`async for`), and requires checking event types for delta text.

**🔹 Processing Streaming Delta Events**
→ While iterating over the asynchronous stream, the SDK emits various types of events. To print just the actual text being generated by the LLM, the code must filter for a specific event type and instance, then print the "delta" (the new fragment of text) while preventing Python from creating a new line for every chunk.
```python
# Call the streamed runner (returns a coroutine)
result = Runner.run_streamed(sales_agent1, input="Write a cold sales email")

# Asynchronously iterate through the event stream
async for event in result.stream_events():
    
    # Filter for the specific event type that contains text deltas
    if event.type == "raw_response_event" and isinstance(event.data, ResponseTextDeltaEvent):
        
        # Print the tiny text chunk (delta) 
        # end="" prevents newlines, flush=True forces the console to update immediately
        print(event.data.delta, end="", flush=True)
```

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Architectural Layers of Agentic Systems
2. Agents Calling Agents: Tools vs. Handoffs
3. Streaming Agent Responses (Async Execution vs. Standard Run)