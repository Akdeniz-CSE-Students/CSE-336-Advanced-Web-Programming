📹 VIDEO TOPIC: CrewAI Project Recap and Assignment
🕐 COVERAGE: CrewAI CLI basics, project structure, `uv` dependency management, YAML configuration files, and multi-model agent assignments.

***

**🔹 CrewAI Underlying Dependency Management (`uv`)**
→ The instructor emphasizes that CrewAI projects are, under the hood, `uv` projects. `uv` is an extremely fast Python package and project manager. When you create and run CrewAI projects, `uv` is managing the dependencies and the virtual environment behind the scenes.

**⭐ 🔹 CrewAI Project Creation CLI Command**
→ To start a new project, CrewAI provides a CLI (Command Line Interface) tool that automatically generates the necessary boilerplate code and directory structure. You do not need to build the project from scratch.
```bash
# Command to create a new project:
crewai create crew <project_name>

# Examples from the video:
crewai create crew my_crew
crewai create crew debate
```
> ⭐ **EXAM NOTE:** Knowing the exact CLI command (`crewai create crew`) used to scaffold a new project is a fundamental practical requirement for working with the CrewAI framework.

**⭐ 🔹 CrewAI Project Directory Structure & Scaffolding**
→ Executing the creation command generates a specific, minimal scaffolding directory structure. This separation of concerns is a core design pattern of CrewAI, splitting configuration data from executable Python logic. The generated structure looks exactly like this:
```text
my_crew/               # Root project folder
└── src/
    └── my_crew/       # Source code module
        ├── config/
        │   ├── agents.yaml  # Agent definitions
        │   └── tasks.yaml   # Task definitions
        ├── crew.py          # Core logic and decorators
        └── main.py          # Entry point script
```
> ⭐ **EXAM NOTE:** You must memorize this standard directory structure. Understanding where YAML configurations live versus where Python logic lives is critical for architectural questions on CrewAI.

**⭐ 🔹 Configuration Files: `agents.yaml` and `tasks.yaml`**
→ CrewAI heavily utilizes YAML files in the `config/` directory to define the characteristics and goals of the crew, making the system highly configurable without changing Python code.
- **`agents.yaml`**: This file is used to define each individual agent. Crucially, the instructor notes that this is where you define the specific LLM **model** that each agent will use to generate its responses.
- **`tasks.yaml`**: This file defines the specific jobs the agents need to complete. It includes configurations such as the **expected output** of the task and the specific **output file** where the final result should be saved once the task is complete.
> ⭐ **EXAM NOTE:** The distinction between what goes into `agents.yaml` (roles, models, personas) vs. `tasks.yaml` (goals, expected outputs, output files) is a highly testable concept regarding CrewAI's declarative configuration pattern.

**🔹 Core Logic: `crew.py`**
→ The `crew.py` file is the heart of the application where everything is brought together. 
- It utilizes various CrewAI **decorators** to link the Python code to the YAML configurations.
- It contains the functions that instantiate the agents and tasks defined in the YAML files.
- It defines the actual `Crew` object.
- It is where the execution **process** is defined. In the instructor's example, the process was set to **sequential**, meaning the tasks are executed strictly one after another in order.

**🔹 Running a CrewAI Project**
→ Once your YAML files and Python logic are set, you execute the crew using a simple CLI command from within your project directory. This command triggers the entry point (`main.py`), kicks off the sequential process, generates the output, and saves it to the specified file.
```bash
crewai run
```

**🔹 Instructor's Assignment: Multi-Model Debate Crew**
→ To get comfortable with the framework, the instructor assigns a challenge to modify the existing "debate" codebase. The goal is to build an arena to test different LLMs against each other to see which is better at forming coherent and persuasive arguments.
To achieve this, the student must implement the following architectural changes:
1. **Separate Agents**: Instead of having a single agent handle both sides of an argument, break the architecture into **two distinct agents**: one for proposing the motion, and one for opposing the motion.
2. **Separate Tasks**: Create individual tasks assigned explicitly to these new separate agents.
3. **Multi-Model Strategy**: Assign **different LLM models** to the competing agents within the `agents.yaml` file. The instructor suggests making an OpenAI model debate against a DeepSeek model.
4. **Evaluation**: Act as the judge to evaluate the output and create a "leaderboard" based on the debating skills of the different underlying LLMs.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. CrewAI Project Creation CLI Command
2. CrewAI Project Directory Structure & Scaffolding
3. Configuration Files: `agents.yaml` and `tasks.yaml`