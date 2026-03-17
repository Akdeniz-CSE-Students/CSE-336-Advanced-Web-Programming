📹 VIDEO TOPIC: Building a Planner Agent with Structured Outputs and Chain-of-Thought
🕐 COVERAGE: Agent creation, Pydantic BaseModels, Structured Outputs via JSON parsing, and Next-Token Prediction optimizations.

**🔹 The Planner Agent Concept & Cost Considerations**
→ The "Planner Agent" is a specialized agent designed to take a user's initial query and break it down into a handful of targeted web searches. This agent acts as the preliminary step for conducting deep research. 
→ **Cost Management:** When designing an agentic workflow that relies on external tools (like a web search API), cost is a major factor. The instructor notes that each web search costs 2.5 cents. To prevent a "big bill," the parameter `HOW_MANY_SEARCHES` is set to `3` instead of a higher number like `10`. This demonstrates a common trade-off in AI engineering: balancing the comprehensiveness of the research against the financial cost of API calls.
→ **System Prompt / Instructions:** The agent is guided by a straightforward system prompt: *"You are a helpful research assistant. Given a query, come up with a set of web searches to perform to best answer the query. Output [HOW_MANY_SEARCHES] terms to query for."*

**⭐ 🔹 Pydantic BaseModels for Structured Outputs**
→ To force the Large Language Model (LLM) to return data in a predictable, programmatic format rather than free-flowing text, AI engineers use Structured Outputs. In Python, this is heavily implemented using the Pydantic library, specifically by inheriting from `BaseModel`. By defining a class that subclasses `BaseModel`, you create a strict schema that the LLM must follow.
→ **The Docstring Annotation Trick:** A critical technique when defining these schemas is providing string annotations (docstrings) beneath the variables. When the schema is passed to the model, these descriptions are included in the prompt payload. This explicitly teaches the model *what* the field is for and *why* it should populate it a certain way, drastically improving the quality of the output.

> ⭐ **EXAM NOTE:** Understanding how to enforce structured outputs using Pydantic `BaseModel` and utilizing docstrings to guide the model's generation is a foundational pattern in modern AI agent development. Expect to identify how to map Python classes to LLM JSON outputs.

```python
# ⭐ Code Example: Defining the Web Search Item Schema
class WebSearchItem(BaseModel):
    # The reason field is placed first (explained below)
    reasons: str
    # This docstring is passed to the LLM to guide its output generation
    """Your reasoning for why this search is important to the query."""
    
    query: str
    # Another explicit instruction for the LLM
    """The search term to use for the web search."""
```

**⭐ 🔹 Chain-of-Thought via Schema Ordering (Reason Before Query)**
→ The architecture of the `WebSearchItem` schema intentionally asks the model to generate a `reasons` string *before* generating the `query` string. This is not accidental; it is a structural implementation of Chain-of-Thought (CoT) prompting.
→ **The Next-Token Prediction Mechanism:** LLMs do not "think" with human logic; they generate text via next-token prediction—predicting the statistically most likely next word based on all preceding words. 
→ By forcing the model to write out its rationale (`reasons`) first, you inject those "reasoning tokens" into the context window. When the model eventually generates the `query` field, it conditions that query on the logic it just established. This forces the model to "think through" the problem, resulting in highly coherent, relevant, and accurate queries. If `query` was placed before `reasons`, the reasoning would be an afterthought, and the quality of the query would drop.

> ⭐ **EXAM NOTE:** This is an incredibly critical concept. You must understand that ordering schema fields to require "reasoning" before "answers" leverages the LLM's next-token prediction engine to create an automatic Chain-of-Thought, yielding higher-quality outputs.

**🔹 Nested Pydantic Models (The WebSearchPlan)**
→ To handle multiple search queries, you can nest Pydantic models. The instructor creates a wrapper class called `WebSearchPlan` that contains a list of the previously defined `WebSearchItem` objects.
→ Just like the inner fields, the wrapper field also receives an annotation to guide the model on exactly what this list represents in the grand scheme of the task.

```python
# Code Example: Nesting the Schema
class WebSearchPlan(BaseModel):
    # Creating a list of the nested Pydantic objects
    searches: list[WebSearchItem]
    # Guiding the model on the purpose of this list
    """A list of web searches to perform to best answer the query."""
```

**⭐ 🔹 Agent Instantiation and the "Magic" of `output_type`**
→ When instantiating the agent object, the parameter `output_type` is passed, linking directly to the `WebSearchPlan` Pydantic model. This parameter explicitly tells the agent to forgo its default behavior (generating natural language text) and instead output a structured Python object.
→ **De-mystifying the Magic (JSON parsing):** The instructor emphasizes that there is no deep architectural "magic" allowing a transformer model to natively write Python objects. Behind the scenes, the framework is converting the Pydantic schema into a JSON Schema. The LLM is prompted strictly to return a JSON string that conforms to this schema. Once the LLM generates the JSON string, the framework parses that JSON back into the structured Python objects (`WebSearchPlan` and `WebSearchItem`). 

> ⭐ **EXAM NOTE:** Understanding the underlying mechanism of structured outputs is vital. You must know that LLMs do not return native code objects; they return JSON strings mapped to a JSON Schema, which the backend application parses into native objects.

```python
# Code Example: Agent Instantiation
planner_agent = Agent(
    name="PlannerAgent",
    instructions=INSTRUCTIONS,  # The system prompt defined earlier
    model="gpt-4o-mini",        # Using a smaller, cost-effective model
    # ⭐ This is the key parameter that enforces the JSON structured output
    output_type=WebSearchPlan,  
)
```

**🔹 Execution and Output Analysis**
→ The agent is run using the query: *"Latest AI Agent frameworks in 2025"*.
→ Because of the `output_type` parameter, the result (`result.final_output`) is not a string, but a populated `WebSearchPlan` object containing a list of `WebSearchItem` objects.
→ **The Output Breakdown:**
  1.  *Item 1:* **Reason:** "To find the most recent developments and releases in AI agent frameworks for 2025." / **Query:** "latest AI agent frameworks 2025"
  2.  *Item 2:* **Reason:** "To explore industry-specific or academic research on AI agent frameworks emerging in 2025." / **Query:** "emerging AI agent frameworks 2025"
  3.  *Item 3:* **Reason:** "To stay updated with major conferences, publications, or announcements related to AI agent frameworks in 2025." / **Query:** "AI agent frameworks conference announcements 2025"
→ *Conclusion:* The output perfectly conforms to the JSON schema. The reasoning fields successfully guided the model to generate three distinctly different, logical angles for researching the single user query, proving the effectiveness of the "reasoning before query" schema architecture.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Pydantic BaseModels for Structured Outputs
2. Chain-of-Thought via Schema Ordering (Reason Before Query)
3. Agent Instantiation and the "Magic" of `output_type` (JSON Parsing mechanism)