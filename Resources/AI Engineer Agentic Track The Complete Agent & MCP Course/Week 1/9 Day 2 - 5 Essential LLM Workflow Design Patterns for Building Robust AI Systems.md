


📹 VIDEO TOPIC: Agentic Systems: Anthropic's 5 Workflow Design Patterns
🕐 COVERAGE: Introduction to Workflows vs. Agents and the 5 Workflow Architectures

**⭐ 🔹 Anthropic's Distinction: Workflows vs. Agents**
→ According to Anthropic, agentic systems can be broadly categorized into two distinct types: Workflows and Agents. 
*   **Workflows:** Systems where Large Language Models (LLMs) and tools are orchestrated through predefined code paths. They follow a specific, step-by-step track laid out by the developer.
*   **Agents:** Systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks with a high degree of autonomy.

> ⭐ **EXAM NOTE:** This is a foundational definition for the entire course. Understanding the strict distinction Anthropic makes between a predefined code-driven "Workflow" and a dynamically self-directed "Agent" is highly likely to be tested.

**🔹 Diagram Terminology (Blue vs. Yellow Boxes)**
→ Throughout the workflow diagrams presented, the instructor specifies a strict color-coding system to understand how tasks are executed:
*   **Yellow Boxes:** Represent calls to an LLM (Large Language Model).
*   **Blue Boxes:** Represent traditional software code (e.g., Python code) written by the developer to handle logic, routing, or aggregation. This code execution is entirely optional depending on the specific workflow.

**⭐ 🔹 Workflow Pattern 1: Prompt Chaining**
→ **Definition:** A workflow pattern that decomposes a task into a fixed sequence of sub-tasks. 
→ **How it works:** 
1. An input enters the system and is processed by LLM 1.
2. The output of LLM 1 passes through an optional software gate (Code/Blue Box).
3. The result is passed to LLM 2, whose output then goes to LLM 3, finally producing the end output.
→ **Why use it?:** It allows developers to frame each LLM call very precisely. By breaking a large task down, you get the most effective response from the LLM at each step while keeping the entire process on strict "guardrails."
→ **Analogy/Example:** The instructor compares this to a commercial sales prompt sequence: First, ask an LLM to pick a market sector. Second, given that sector, ask a second LLM to pick a pain point. Third, given that pain point, ask a third LLM to pick a solution.
→ **Instructor's Caveat on Autonomy:** While Anthropic categorizes this strictly as a "workflow," the line between workflows and agents is blurry. Because LLM 1 decides the topic (which inherently dictates what LLM 2 and LLM 3 will do), the LLM still exercises a degree of discretion and autonomy.

> ⭐ **EXAM NOTE:** You must know Prompt Chaining as the process of passing outputs sequentially from one LLM to the next. Expect to be tested on the primary benefit: applying strict guardrails while allowing precise, step-by-step prompt framing.

**⭐ 🔹 Workflow Pattern 2: Routing**
→ **Definition:** A pattern that directs an input into a specialized sub-task, ensuring a "separation of concerns."
→ **How it works:**
1. An input arrives at an "LLM Router."
2. The Router's sole job is to classify the task and understand what needs to be done.
3. Based on its classification, the Router sends the task to one of several specialized expert models (e.g., LLM 1, LLM 2, or LLM 3).
→ **Why use it?:** This is a powerful and very common model because it leverages specialist models. You can have different LLMs equipped with different tools or system prompts tailored for specific domains, and the Router acts as the traffic cop directing the prompt to the best-equipped expert.
→ **Instructor's Caveat on Autonomy:** Similar to Prompt Chaining, classifying this as having "no autonomy" is artificial. The Router LLM is making active decisions on where to send the data, albeit within the guardrails of a given workflow.

> ⭐ **EXAM NOTE:** Remember the term "Separation of Concerns" in relation to the Routing pattern. If an exam question asks which pattern is best for directing tasks to different domain experts, the answer is Routing.

**⭐ 🔹 Workflow Pattern 3: Parallelization**
→ **Definition:** A workflow pattern dedicated to breaking down tasks and running multiple subtasks concurrently to save time and process complex inputs efficiently.
→ **How it works:**
1. An input is sent to a **Code Coordinator** (a Blue Box — e.g., Python code, *not* an LLM). 
2. The code breaks the task down and sends it to multiple LLMs (LLM 1, LLM 2, LLM 3) at the exact same time (concurrently).
3. The LLMs process their tasks in parallel.
4. The outputs are sent to a **Code Aggregator** (another Blue Box / Python code), which stitches the answers together to produce the final output.
→ **Task Variations:** The tasks run in parallel do *not* necessarily need to be different. 
*   *Variation A:* Running three distinctly different sub-tasks concurrently.
*   *Variation B:* Running the *exact same task* three times concurrently, and using the Aggregator to calculate the average of the responses. 

> ⭐ **EXAM NOTE:** Pay extreme attention to the fact that in Parallelization, the Coordinator and Aggregator are **traditional code (Blue Boxes)**, not LLMs. This is the critical differentiator between Parallelization and the Orchestrator-Worker pattern.

**⭐ 🔹 Workflow Pattern 4: Orchestrator-Worker**
→ **Definition:** A highly dynamic workflow where complex tasks are broken down dynamically, assigned to workers, and then synthesized.
→ **How it works:**
1. An input goes to an **Orchestrator LLM** (Yellow Box).
2. The Orchestrator LLM dynamically decides how to divvy up the tasks and how many worker LLMs are needed.
3. The Worker LLMs (LLM 1, LLM 2, LLM 3) execute the tasks.
4. The outputs are sent to a **Synthesizer LLM** (Yellow Box), which combines the results into a final output.
→ **Comparison: Orchestrator-Worker vs. Parallelization**
*   *Parallelization:* Uses rigid **Code** to split tasks (Coordinator) and combine them (Aggregator).
*   *Orchestrator-Worker:* Uses **LLMs** to dynamically analyze and split tasks (Orchestrator) and intelligently combine them (Synthesizer).
→ **Instructor's Caveat on Autonomy:** The instructor strongly argues that calling Orchestrator-Worker a "fixed workflow" is a massive stretch. Because the Orchestrator LLM has full discretion over how to divide tasks and how many LLMs to assign, this pattern heavily blurs the line and borders on being a fully autonomous Agent pattern.

> ⭐ **EXAM NOTE:** You will almost certainly be tested on the difference between Parallelization and Orchestrator-Worker. If the system uses an LLM to dynamically decide how to split the work, it is Orchestrator-Worker. If it uses fixed code, it is Parallelization.

**⭐ 🔹 Workflow Pattern 5: Evaluator-Optimizer**
→ **Definition:** A pattern designed to increase accuracy and robustness by having one LLM generate an output, and another LLM strictly validate it. Also frequently referred to in the industry as "Evaluators," "Validation," or "Validation Agents."
→ **How it works (The Feedback Loop):**
1. An input is sent to the **LLM Generator**, which creates a proposed solution.
2. The solution is passed to the **LLM Evaluator**. The Evaluator is armed with extra context/instructions explicitly telling it *not* to generate content, but only to check the Generator's work.
3. **Decision Fork:**
   *   *If Accepted:* The workflow ends, and the output is delivered.
   *   *If Rejected:* The Evaluator sends the rejection *along with feedback/reasons* back to the LLM Generator.
4. The LLM Generator uses this feedback to create a new solution, repeating the loop.
→ **Why use it?:** The number one concern when building production-grade LLM systems is accuracy, predictability, and robustness. Because there are no 100% guarantees with LLMs, this evaluator loop acts as strict quality control, significantly raising the guarantee of a high-quality final output. 

> ⭐ **EXAM NOTE:** The concept of the "Feedback Loop" is critical here. If an exam scenario requires a system architecture that maximizes output accuracy, quality control, and robustness through self-correction, you must select the Evaluator-Optimizer pattern.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **Anthropic's Distinction: Workflows vs. Agents**
2. **Workflow Pattern 1: Prompt Chaining**
3. **Workflow Pattern 2: Routing**
4. **Workflow Pattern 3: Parallelization**
5. **Workflow Pattern 4: Orchestrator-Worker**
6. **Workflow Pattern 5: Evaluator-Optimizer**
