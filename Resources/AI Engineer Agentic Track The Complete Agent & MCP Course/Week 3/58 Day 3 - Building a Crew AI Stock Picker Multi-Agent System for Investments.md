📹 VIDEO TOPIC: Building a "Stock Picker" CrewAI Project: Agents, Tasks, and Hierarchical Process
🕐 COVERAGE: CrewAI Project Initialization, Agent Configuration, Task Configuration, Task Context Mapping, and Prompt Consistency Best Practices.

***

⭐ **🔹 The 5 Steps to Build a CrewAI Project**
→ The instructor recaps the foundational five-step workflow required to build and execute any CrewAI project from scratch. Memorizing this sequence is essential for standard CrewAI development. 
1. **Create the project:** Run `crewai create crew my_project` in the terminal to generate the boilerplate scaffold.
2. **Fill in the config YAML files:** Define the overarching configurations for the Agents (`agents.yaml`) and Tasks (`tasks.yaml`).
3. **Complete the `crew.py` module:** Write the Python code to create the Agents and Tasks classes, referencing the definitions from the YAML config files, and instantiate the Crew.
4. **Update `main.py`:** Set up any initial input variables (e.g., `{sector}`) required for the run.
5. **Run the project:** Execute the project from the terminal using `crewai run`.

> ⭐ **EXAM NOTE:** This 5-step lifecycle is the definitive foundational workflow for CrewAI scaffolding. Knowing the exact sequence and which files are responsible for which configurations (YAML for definitions, `crew.py` for instantiation) is highly likely to be tested on an architecture or practical application exam.

**🔹 Advanced CrewAI Concepts: The "Deep Dive" Focus Areas**
→ For this specific "Stock Picker" project, the instructor highlights three advanced CrewAI capabilities that will be implemented to elevate the system beyond basic sequential agents:
*   **Structured Outputs:** Forcing the LLM outputs into specific formats (like JSON) to ensure data is passed reliably between systems.
*   **Custom Tools:** In addition to using built-in tools (like the Serper tool for web searches), the project will feature a completely custom-built tool (specifically, a push notification tool).
*   **Hierarchical Process:** Moving away from a strict sequential process, this allows the Crew to use a "Manager" agent to dynamically manage the workflow and decide which task goes to which agent.

⭐ **🔹 The Hierarchical Process Architecture**
→ In standard CrewAI setups, tasks execute in a linear, sequential order. The Hierarchical Process introduces a completely different architecture where a designated "Manager" agent sits at the top of the chain. Instead of hardcoding the step-by-step flow, the Manager is given the overarching goal and is responsible for evaluating the state of the project, delegating tasks to the appropriate specialized agents, and reviewing their work. 

> ⭐ **EXAM NOTE:** The distinction between a Sequential Process and a Hierarchical Process is a core framework concept. You must understand that in a Hierarchical setup, a "Manager" agent dictates the flow of delegation rather than relying on a predetermined list of execution steps.

**🔹 Project Initialization via CLI**
→ To start the project, the instructor uses the terminal to navigate to the desired directory and runs the standard CrewAI creation command.
```bash
# Command to scaffold the new project
crewai create crew stock_picker
```
During the CLI setup wizard, the instructor makes the following configuration choices:
*   **Provider:** OpenAI
*   **Model:** `gpt-4o-mini` (used for standard agents)
*   **Environment Variables:** Skipped setting them up in the terminal (relying on `.env` file instead).
*   *Disclaimer:* The instructor explicitly notes that this "Stock Picker" is strictly for educational and investigatory purposes and should never be used to make actual financial trading decisions.

**🔹 Configuring Agents (`src/config/agents.yaml`)**
→ The first coding step is defining the agents in the `agents.yaml` file. Each agent requires a `role`, a `goal`, a `backstory`, and an assigned LLM. The instructor defines four specific agents for this project:

1.  **`trending_company_finder`**:
    *   **Role:** Financial News Analyst
    *   **Goal:** Read latest news and find 2-3 trending companies in a `{sector}`. Never pick the same company twice.
    *   **Backstory:** A market expert with a knack for picking interesting companies based on latest news.
    *   **LLM:** `openai/gpt-4o-mini`

2.  **`financial_researcher`**:
    *   **Role:** Senior Financial Researcher
    *   **Goal:** Given details of trending companies, provide comprehensive analysis of each in a report.
    *   **Backstory:** A financial expert with a proven track record of deeply analyzing hot companies.
    *   **LLM:** `openai/gpt-4o-mini`

3.  **`stock_picker`**:
    *   **Role:** Stock Picker From Research
    *   **Goal:** Given researched companies, select the best one for investment, notify the user, and provide a detailed report.
    *   **Backstory:** A meticulous, skilled financial analyst with a talent for synthesizing research.
    *   **LLM:** `openai/gpt-4o-mini`

4.  **`manager`** (Crucial for Hierarchical Process):
    *   **Role:** Manager
    *   **Goal:** Pick the best company for investment.
    *   **Backstory:** A skilled project manager who can delegate tasks to achieve the goal. Highly effective at delegating to the right people.
    *   **LLM:** `openai/gpt-4o-mini`
    *   *Note:* The instructor emphasizes keeping the Manager agent's description very simple and "vanilla." It only needs to know its overarching goal and its role as a delegator.

**🔹 Configuring Tasks (`src/config/tasks.yaml`)**
→ Following the agents, the instructor defines the specific jobs they must perform in the `tasks.yaml` file. Tasks must be clearly defined with descriptions, expected outputs, assigned agents, and output files. 

1.  **`find_trending_companies`**:
    *   **Description:** Find top trending companies in `{sector}` by searching latest news. Find new companies not found before.
    *   **Expected Output:** A list of trending companies in `{sector}`.
    *   **Agent:** `trending_company_finder`
    *   **Output File:** `output/trending_companies.json` (The `.json` extension foreshadows the use of Structured Outputs).

2.  **`research_trending_companies`**:
    *   **Description:** Given a list of trending companies, provide detailed analysis of each in a report.
    *   **Expected Output:** A report containing detailed analysis of each company.
    *   **Agent:** `financial_researcher`
    *   **Context:** `[find_trending_companies]` (Links tasks together).
    *   **Output File:** `output/research_report.json`

3.  **`pick_best_company`**:
    *   **Description:** Analyze findings, pick the best company. Send a push notification to the user with the decision. Respond with a detailed report on why chosen and why others were rejected.
    *   **Expected Output:** The chosen company and rationale; rejected companies and rationale.
    *   **Agent:** `stock_picker`
    *   **Context:** `[research_trending_companies]`
    *   **Output File:** `output/decision.md`
    *   *Note:* The "push notification" instruction is the hook for where the Custom Tool will be utilized later in the workflow.

⭐ **🔹 Task Context Mapping (`context` field)**
→ In `tasks.yaml`, the `context` field is used to explicitly map the data flow between tasks. For example, the `research_trending_companies` task includes `context: [find_trending_companies]`. This tells the CrewAI framework that the financial researcher agent must wait for and consume the specific output data generated by the trending company finder task before beginning its own work.

> ⭐ **EXAM NOTE:** Understanding how tasks pass data to one another is critical. The `context` key in the task definition is the exact mechanism used to chain tasks together, ensuring an agent has the required inputs from a prior agent's output.

⭐ **🔹 Prompt Engineering Best Practices for CrewAI Stability**
→ The instructor provides a "Pro-Tip" regarding how to write prompts (descriptions, goals, backstories) in the YAML config files. 
*   **Be Instructive and Crisp:** Keep commands straightforward and avoid overly complex, convoluted phrasing. Small, clear steps yield coherent responses.
*   **Consistent Terminology:** You must use exact, consistent language across different agents and tasks. For example, consistently using the phrase "trending companies" across the finder, researcher, and picker tasks. 
*   **The Consequence of Inconsistency:** The instructor notes from personal experimentation that using inconsistent language across agents and tasks causes instability and hallucinations in the framework, as the agents fail to properly align the data being handed off.

> ⭐ **EXAM NOTE:** A common troubleshooting question on AI Agent exams is why an agent pipeline is failing to pass context accurately or hallucinating. "Inconsistent terminology/language across agent and task prompts" is a primary architectural culprit for framework instability.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **The 5 Steps to Build a CrewAI Project**
2. **The Hierarchical Process Architecture**
3. **Task Context Mapping (`context` field)**
4. **Prompt Engineering Best Practices for CrewAI Stability**