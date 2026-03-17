📹 VIDEO TOPIC: CrewAI Recap, Project Setup, Tools, Context, and Serper API
🕐 COVERAGE: Week 3, Day 2 - Building with the CrewAI Framework

**⭐ 🔹 Agent (CrewAI)**
→ An Agent is the smallest autonomous unit in the CrewAI framework. It typically has a Large Language Model (LLM) associated with it (though the instructor notes you can technically have an agent without an LLM, it is standard to include one). Core attributes of an Agent include its **role**, **goal**, and **backstory**. Furthermore, Agents can be equipped with **memory** and **tools** (which are explored deeper later in the course). 
> ⭐ **EXAM NOTE:** This is the fundamental definition of a CrewAI agent. You must know its core components (Role, Goal, Backstory) and optional extensions (Memory, Tools) to answer architectural questions.

**⭐ 🔹 Task (CrewAI)**
→ A Task is a specific assignment to be carried out by an Agent. The instructor notes that this concept does *not* have a direct analog in the OpenAI Agents SDK, making it unique to frameworks like CrewAI. A task requires a **description** of what needs to be done, an **expected output**, potentially an output file definition, and must be assigned to a specific **Agent**.
> ⭐ **EXAM NOTE:** Understanding how Tasks map to Agents is critical. The explicit comparison made between CrewAI (which uses Tasks) and OpenAI Agents SDK (which does not have an explicit Task analog) is highly testable.

**⭐ 🔹 Crew & Execution Modes (Sequential vs. Hierarchical)**
→ A Crew is defined as a team of agents and tasks working together. When these tasks are assigned to agents, the Crew must execute them using a specific strategy.
1. **Sequential (Default):** Tasks run in the exact order they are defined.
2. **Hierarchical:** The framework uses a "Manager LLM" to dynamically figure out which task should be assigned to which agent and in what order.
> ⭐ **EXAM NOTE:** The distinction between Sequential and Hierarchical execution is a core routing concept. You must specifically remember that Hierarchical mode requires the implementation of a **Manager LLM** to delegate the tasks.

**⭐ 🔹 The Five Steps to Build a CrewAI Project**
→ The instructor outlines the standard 5-step workflow for initializing and running any CrewAI project:
1. **Create the project:** Run `crewai create crew <project_name>` in the terminal to build the entire file system structure.
2. **Configure YAML files:** Navigate to the `src/[project]/config` directory. Fill in the `agents.yaml` and `tasks.yaml` files. This keeps prompting text cleanly separated from Python code.
3. **Write `crew.py`:** Use decorators and functions provided by the CrewAI module to instantiate the Agents, Tasks, and the Crew itself. This code references the YAML files to pull in the predefined attributes (though you *could* pass them manually in code, the YAML reference is best practice).
4. **Update `main.py`:** Set up the configuration and run parameters. This is where you pass inputs into your crew (e.g., passing "AI regulation" as the topic for a debate).
5. **Run the project:** Execute `crewai run` from within the project folder. Behind the scenes, this executes a `uv run` command to launch the system.
> ⭐ **EXAM NOTE:** Memorizing these exact five steps and knowing which file handles which responsibility (YAML for prompts/config, `crew.py` for logic/decorators, `main.py` for execution/inputs) is mandatory for practical framework application.

**🔹 Tools (Going Deeper)**
→ Tools are mechanisms used to equip agents with specific capabilities, allowing them to interact with the outside world (e.g., searching the web, reading a file, querying a database). 

**⭐ 🔹 Context (Going Deeper)**
→ Context is the specific configuration used to tell the CrewAI framework exactly what information needs to be passed from one task to another. Because tasks run sequentially or hierarchically, the output of one task often serves as the required context for a subsequent task.
> ⭐ **EXAM NOTE:** Context passing is essential for complex workflows. You must know that "Context" is the formal mechanism used in CrewAI to chain information between independent tasks.

**🔹 Serper API (`serper.dev`)**
→ Serper is a lightning-fast, highly cost-effective Google Search API used to equip agents with web-searching capabilities. The acronym **SERP** stands for **Search Engine Results Page**. The instructor emphasizes choosing Serper because it offers 2,500 free queries upon sign-up (plenty for course development). 
*Caution:* The instructor explicitly warns not to confuse this with a different service called "SerpAPI". Ensure you are using `serper.dev`.

**🔹 Configuring the Serper API Key**
→ To use Serper, you must generate an API key from their dashboard and store it in your project's environment variables. It must be named exactly as follows inside your `.env` file:
```env
SERPER_API_KEY=your_copied_api_key_here
```

**🔹 Creating the "Financial Researcher" Project**
→ The instructor demonstrates creating a brand new project to test Tools and Context using the standard CLI process:
1. Open a fresh terminal inside the week's directory.
2. Run the command:
   ```bash
   crewai create crew financial_researcher
   ```
3. Use the interactive terminal prompts to select the provider (**OpenAI**) and the model (**gpt-4o-mini**).
4. Skip the API key setup prompt (since it is already handled securely via the local `.env` file).
5. The CLI generates the scaffolding, ready for development.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Agent (CrewAI)
2. Task (CrewAI)
3. Crew & Execution Modes (Sequential vs. Hierarchical)
4. The Five Steps to Build a CrewAI Project
5. Context (Going Deeper)