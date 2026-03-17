📹 VIDEO TOPIC: Building a Multi-Agent Research, Writing, and Email Pipeline
🕐 COVERAGE: Tool Creation, Structured Outputs, Asynchronous Orchestration, and Parallel Task Execution

---

**⭐ 🔹 Converting a Python Function to a Tool (`@function_tool`)**
→ To allow an AI agent to interact with the outside world (like sending an email), you must convert standard code into an LLM-accessible tool. The instructor demonstrates using the `@function_tool` decorator on a standard Python `send_email` function. This decorator automatically parses the function signature and docstrings to generate the required "JSON gumpf" (JSON schema) behind the scenes, which the LLM needs to understand how and when to invoke the tool. 

> ⭐ **EXAM NOTE:** Understanding how decorators like `@function_tool` bridge the gap between Python execution and LLM JSON schemas is a foundational concept in agent tool calling.

```python
@function_tool
def send_email(subject: str, html_body: str) -> dict[str, str]:
    """
    Send out an email with the given subject and HTML body.
    """
    # Uses SendGrid API to send the email
    sg = sendgrid.SendGridAPIClient(api_key=os.environ.get("SENDGRID_API_KEY"))
    # Instructor notes: Always ensure 'from_email' and 'to_email' 
    # are updated to your verified environment variables.
    ...
```

**🔹 Setting up the Email Agent (Delegating Formatting)**
→ Instead of hardcoding the logic for the email's subject line or HTML structure, the instructor creates a dedicated `email_agent`. By giving this agent the `send_email` tool and full discretion, the LLM determines the most appropriate subject line and handles the conversion of markdown reports into clean, well-presented HTML. The agent is powered by `gpt-4o-mini`.

**⭐ 🔹 Enforcing Structured Outputs (`BaseModel`)**
→ When the pipeline reaches the report-writing phase, the output needs to be highly predictable so it can be passed programmatically to the next agent. The instructor uses Pydantic's `BaseModel` to define a strict schema called `ReportData`. By passing `output_type=ReportData` to the `writer_agent`, the LLM is forced to return a JSON object that perfectly matches this structure, rather than a loose text response.

> ⭐ **EXAM NOTE:** Structured Outputs using Pydantic (`BaseModel`) is a critical pattern for agentic pipelines. It guarantees the shape of the data flowing between agents, preventing parsing errors downstream.

```python
class ReportData(BaseModel):
    short_summary: str
    """A short 2-3 sentence summary of the findings."""
    
    markdown_report: str
    """The final report"""
    
    follow_up_questions: list[str]
    """Suggested topics to research further"""

# The agent definition utilizing the structured output
writer_agent = Agent(
    name="WriterAgent",
    instructions=INSTRUCTIONS, # Tells the agent to act as a senior researcher
    model="gpt-4o-mini",
    output_type=ReportData     # Enforces the Pydantic schema
)
```

**⭐ 🔹 The Asynchronous Orchestration Pipeline (The 5 Functions)**
→ To connect the individual agents (`planner`, `search`, `writer`, `email`), the instructor breaks the orchestration logic into five distinct asynchronous Python functions. This modular approach keeps the workflow organized.

1. **`plan_searches(query: str)`**: Takes the initial user prompt and passes it to the `planner_agent` to generate a `WebSearchPlan` (a structured list of searches to perform).
2. **`perform_searches(search_plan: WebSearchPlan)`**: Iterates through the generated plan and triggers the searches.
3. **`search_item(item: WebSearchItem)`**: The helper function that actually calls the `search_agent` for an individual search query.
4. **`write_report(query: str, search_results: list[str])`**: Takes the aggregated search results and the original query, passing them to the `writer_agent` to generate the `ReportData` object.
5. **`send_email_report(report: ReportData)`**: Takes the structured report and passes it to the `email_agent` to be formatted and sent.

> ⭐ **EXAM NOTE:** You must understand the sequence of a standard multi-agent research pipeline: Plan → Execute Searches (Gather Context) → Synthesize/Write → Act/Deliver. 

**⭐ 🔹 Parallel Execution with `asyncio.gather`**
→ When executing the search plan, running searches sequentially (one after the other) would be painfully slow. The instructor uses Python's `asyncio.gather` to execute multiple agent calls concurrently. This drastically reduces the total execution time of the pipeline.

> ⭐ **EXAM NOTE:** Utilizing `asyncio.gather` for parallel task execution is a vital performance optimization technique in multi-agent architectures, especially during information retrieval phases.

```python
async def perform_searches(search_plan: WebSearchPlan):
    print("Searching...")
    tasks = [
        # Create an async task for each individual search item
        asyncio.create_task(search_item(item)) 
        for item in search_plan.searches
    ]
    # Await all tasks concurrently
    results = await asyncio.gather(*tasks) 
    print("Finished searching")
    return results
```

**⭐ 🔹 Providing Context to Sub-Agents (`query` + `reason`)**
→ When the `perform_searches` function delegates a task to the `search_agent`, it does not just pass the raw search string. Instead, it constructs a prompt that includes both the `item.query` and the `item.reason` (generated earlier by the planner). The instructor explicitly notes that providing the "reason" gives the sub-agent the context it needs to formulate a better, more accurate search strategy.

> ⭐ **EXAM NOTE:** Passing contextual reasoning along with a task instruction is a proven prompt engineering pattern in agentic systems to reduce hallucinations and improve sub-agent accuracy.

```python
async def search_item(item: WebSearchItem):
    # Providing both query and reason for optimal agent context
    input_str = f"Search term: {item.query}\nReason for searching: {item.reason}"
    result = await Runner.run(search_agent, input_str)
    return result.final_output
```

**🔹 Main Execution Flow & Tracing ("Showtime")**
→ The instructor manually writes out the final execution block to demonstrate how the asynchronous functions chain together to form the complete application. A `with trace("Research trace"):` context manager is used to wrap the execution, which logs the progression of the agents and tool calls for debugging and visibility. (Note: The instructor playfully fights with their AI code editor, Cursor, ignoring its auto-complete suggestions to write the code from scratch and show "autonomy from the machines").

```python
# The final execution block tying it all together
query = "Latest AI Agent frameworks in 2025"

with trace("Research trace"):
    print("Starting research...")
    
    # 1. Plan
    search_plan = await plan_searches(query)
    
    # 2. Execute parallel searches
    search_results = await perform_searches(search_plan)
    
    # 3. Synthesize structured report
    report = await write_report(query, search_results)
    
    # 4. Deliver
    await send_email_report(report)
```

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **Converting a Python Function to a Tool (`@function_tool`)**
2. **Enforcing Structured Outputs (`BaseModel`)**
3. **The Asynchronous Orchestration Pipeline (The 5 Functions)**
4. **Parallel Execution with `asyncio.gather`**
5. **Providing Context to Sub-Agents (`query` + `reason`)**