📹 VIDEO TOPIC: Building a Multi-Agent Financial Researcher Crew with CrewAI
🕐 COVERAGE: Configuring Agents, Linking Tasks via Context, Assembling the Crew, and Multi-Model Execution

***

**🔹 Configuration Directory Structure**
→ The workflow begins in the `src/config/` directory of the CrewAI project. This directory holds the foundational YAML files (`agents.yaml` and `tasks.yaml`) where the behaviors, roles, and instructions for the agents and tasks are defined declaratively, keeping the Python code clean and focused on orchestration.

**⭐ 🔹 Agent Definition & The Power of "Backstory" (`agents.yaml`)**
→ Agents are defined with specific attributes: `role`, `goal`, `backstory`, and the target `llm`. The instructor emphasizes that providing a detailed `backstory` is a highly effective prompting technique. By assigning a persona (e.g., "You're a seasoned financial researcher...", "You're a meticulous analyst..."), you force the LLM to adopt a specific discipline and context. This generates significantly better, more structured outputs compared to basic system instructions. Furthermore, input variables like `{company}` are used as placeholders to make the agents reusable.
```yaml
researcher:
  role: >
    Senior Financial Researcher for {company}
  goal: >
    Research the company, news and potential for {company}
  backstory: >
    You're a seasoned financial researcher with a talent for finding the most relevant information about {company}...
  llm: openai/gpt-4o-mini # (Changed later in the video)

analyst:
  role: >
    Market Analyst and Report Writer focused on {company}
  goal: >
    Analyze {company} and create a comprehensive, well-structured report...
  backstory: >
    You're a meticulous, skilled analyst with a background in financial analysis...
  llm: openai/gpt-4o-mini # (Changed later in the video)
```
> ⭐ **EXAM NOTE:** Understanding the purpose of the `backstory` is critical. It acts as a system-level persona prompt that provides deep context and behavioral guidelines to the agent, which is a core best practice in prompt engineering for agentic frameworks.

**🔹 Task Definition & Formatting Requirements (`tasks.yaml`)**
→ Tasks define exactly what an agent needs to do. They consist of a `description`, `expected_output`, and the assigned `agent`. The instructor explicitly details the formatting requirements within the description (e.g., "focus on: 1. Current company status... 2. Historical performance...") and mandates that the output be a structured report. 

**⭐ 🔹 Task Context Linking (`tasks.yaml`)**
→ To create a multi-agent pipeline, the output of one task must often be fed into another. CrewAI handles this easily using the `context` key. By passing a list of previous task names to the `context` parameter of a subsequent task, the framework automatically injects the output of the referenced task into the prompt of the current task. This ensures continuity and allows the `analyst` to read the findings of the `researcher`.
```yaml
analysis_task:
  description: >
    Analyze the research findings and create a comprehensive report on {company}...
  expected_output: >
    A polished, professional report on {company} that presents the research findings...
  agent: analyst
  context:
    - research_task # Automatically passes the output of 'research_task' here
```
> ⭐ **EXAM NOTE:** The `context` parameter in CrewAI tasks is the primary mechanism for chaining tasks together and passing data between agents in a sequential process. Knowing how to link tasks via context is a fundamental architecture requirement.

**🔹 Task Output Files**
→ You can instruct CrewAI to automatically save the final output of a task directly to the file system. This is done by adding an `output_file` key to the task configuration.
```yaml
  output_file: output/report.md
```

**⭐ 🔹 CrewAI Decorators & Crew Assembly (`crew.py`)**
→ The orchestration happens in `crew.py`. The file uses decorators (`@CrewBase`, `@agent`, `@task`, `@crew`) to bind the YAML configurations to Python methods. 
1. The class loads the YAML files: `agents_config` and `tasks_config`.
2. `@agent` methods create `Agent` objects, passing the specific YAML config dictionary (e.g., `self.agents_config['researcher']`).
3. `@task` methods create `Task` objects similarly.
4. The `@crew` method instantiates the `Crew`. Because of the decorators, `self.agents` and `self.tasks` are automatically populated with the defined agents and tasks, meaning you do not have to manually list them again. The crew is set to `Process.sequential`, meaning tasks execute in the order they are defined.
```python
from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task

@CrewBase
class FinancialResearcher():
    """FinancialResearcher crew"""
    agents_config = 'config/agents.yaml'
    tasks_config = 'config/tasks.yaml'

    @agent
    def researcher(self) -> Agent:
        return Agent(config=self.agents_config['researcher'], verbose=True)

    @agent
    def analyst(self) -> Agent:
        return Agent(config=self.agents_config['analyst'], verbose=True)

    @task
    def research_task(self) -> Task:
        return Task(config=self.tasks_config['research_task'])

    @task
    def analysis_task(self) -> Task:
        return Task(config=self.tasks_config['analysis_task'])

    @crew
    def crew(self) -> Crew:
        return Crew(
            agents=self.agents, # Automatically populated
            tasks=self.tasks,   # Automatically populated
            process=Process.sequential,
            verbose=True
        )
```
> ⭐ **EXAM NOTE:** You must understand how CrewAI decorators automate crew assembly. The `@agent` and `@task` decorators automatically append their returned objects to the `self.agents` and `self.tasks` lists, which are then used by the `@crew` decorator.

**⭐ 🔹 Dynamic Inputs & Crew Kickoff (`main.py`)**
→ To execute the crew, a `run()` method is defined. Here, a dictionary named `inputs` is created. The keys in this dictionary must exactly match the template placeholders (like `{company}`) used in the YAML files. The `kickoff()` method is called on the crew, passing the `inputs` dictionary to inject dynamic data into the agents and tasks at runtime.
```python
def run():
    """Run the crew."""
    inputs = {
        'company': 'Tesla' # Injects 'Tesla' wherever {company} appears in YAML
    }
    
    result = FinancialResearcher().crew().kickoff(inputs=inputs)
    print(result.raw)
```
> ⭐ **EXAM NOTE:** Be prepared to explain how dynamic variables are passed into CrewAI. They are defined as bracketed placeholders `{variable}` in YAML and passed via an `inputs` dictionary into the `kickoff(inputs=...)` method.

**🔹 Multi-Model Architecture (Mixing LLMs per Agent)**
→ Before executing, the instructor demonstrates that you do not have to use the same LLM for every agent. You can assign specialized models based on the agent's task by altering the `llm` string in `agents.yaml`:
- **DeepSeek Chat (`deepseek/deepseek-chat`):** A massive 671-billion parameter model assigned to the `researcher` to handle the heavy, comprehensive data gathering.
- **Llama 3.3 70B (`groq/llama-3.3-70b-versatile`):** A fast, versatile 70-billion parameter model run via the Groq inference engine, assigned to the `analyst` for rapid formatting and synthesis.
*Note: The instructor mentions they will rely entirely on the models' internal knowledge for this run, bypassing external web search tools (like Serper) for now.*

**🔹 CLI Execution & Execution Observation**
→ The system is executed via the terminal using the command `crewai run`. During execution:
- The input variable `{company}` is visibly injected, logging tasks like: *"Conduct thorough research on Tesla."*
- The `researcher` (DeepSeek) takes roughly 30 seconds to generate a highly comprehensive raw data dump.
- The `analyst` (Llama via Groq) takes over instantly due to the `context` link and generates the final formatted markdown report (`report.md`) extremely quickly due to Groq's high-performance inference.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **Agent Definition & The Power of "Backstory" (`agents.yaml`)**
2. **Task Context Linking (`tasks.yaml`)**
3. **CrewAI Decorators & Crew Assembly (`crew.py`)**
4. **Dynamic Inputs & Crew Kickoff (`main.py`)**