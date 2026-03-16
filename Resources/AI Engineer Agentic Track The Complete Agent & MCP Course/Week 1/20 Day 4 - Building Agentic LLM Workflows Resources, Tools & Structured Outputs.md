


📹 VIDEO TOPIC: Week 1 Foundations Recap & Native Agentic Patterns
🕐 COVERAGE: Recap of Agent Frameworks, Resources, Tools, the Evaluator-Optimizer Pattern, and Introduction to Project 1 (Personal Career Agent)

**⭐ 🔹 Core Primitives: Resources vs. Tools**
→ The instructor recaps the core building blocks covered in the day's session, drawing a functional distinction between "Resources" and "Tools" within agent frameworks. **Resources** are utilized to "arm an LLM with information." In the context of the day's lab, resources were specifically used to feed the LLM contextual data about the instructor's (and the student's) professional career. **Tools**, on the other hand, are functional capabilities that the LLM can execute. Understanding this separation—Resources for *context/state injection* and Tools for *action/execution*—is fundamental to building native agent systems. 
> ⭐ **EXAM NOTE:** The distinction between providing an LLM with context (Resources) versus providing it with actionable capabilities (Tools) is a foundational architectural concept in Model Context Protocol (MCP) and agentic design. You may be tested on when to use a resource versus when to use a tool.

**⭐ 🔹 The Evaluator-Optimizer Pattern via Structured Outputs**
→ The instructor highlights the implementation of the **Evaluator-Optimizer pattern**, a critical agentic workflow pattern. This pattern operates through a cyclic, "backwards and forwards" interaction where an initial output is generated (by an LLM), evaluated against specific criteria, and then optimized/refined in subsequent iterations. 
To achieve this programmatically, the instructor emphasizes the use of **Structured Outputs**. By forcing the LLM to reply in a strict, predictable schema (like JSON), the system can easily parse the response, evaluate its quality or correctness, and feed instructions back to the LLM for optimization. The instructor also notes a strong structural connection between this pattern and the use of tools, as tools often rely on structured outputs to parse arguments correctly.
> ⭐ **EXAM NOTE:** The Evaluator-Optimizer pattern is a named, highly-testable agentic architecture. You must know that it involves an iterative feedback loop and relies heavily on Structured Outputs to allow the evaluating function to reliably parse and critique the LLM's response.

**⭐ 🔹 Native Agentic Flows**
→ The primary pedagogical goal of the first week ("Foundations") is to equip students with the skills to build agentic flows *natively*. Before relying on heavy, abstracted frameworks (like LangGraph or CrewAI, which appear later in the course roadmap), students must understand how to orchestrate interactions directly between LLMs. This native orchestration relies on manually stitching together the core primitives discussed:
1.  **Resources** (for context)
2.  **Structured Outputs** (for predictable data flow)
3.  **Tools** (for interacting with external systems)
By mastering these natively, developers can achieve complex "agentic workflows" without being locked into a specific high-level abstraction layer.
> ⭐ **EXAM NOTE:** Building "natively" refers to orchestrating LLMs using basic primitives (tools, structured outputs, resources) rather than relying on black-box framework abstractions. Understanding this baseline is crucial for debugging and architectural design questions.

**🔹 Project 1: The "Alter Ego" Personal Career Agent**
→ The culmination of the first week's foundation (taking place on Day 5) is the packaging of these native skills into a deployable, commercial-grade project. The instructor introduces this as an "alter ego" agent. 
**The Project Pipeline:**
1.  **Context Injection:** Use *Resources* to feed the LLM your specific professional history and expertise.
2.  **Logic & Refinement:** Utilize *Structured Outputs* and the *Evaluator-Optimizer pattern* to ensure the agent answers questions accurately and stays on persona.
3.  **Deployment:** Package the native flow into a standalone application that can be deployed on a personal website.
4.  **User Interaction:** Visitors to the website can interact with this agent to ask questions and learn about the creator's professional expertise, serving as an interactive resume or personal representative.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **Core Primitives: Resources vs. Tools**
2. **The Evaluator-Optimizer Pattern via Structured Outputs**
3. **Native Agentic Flows**
