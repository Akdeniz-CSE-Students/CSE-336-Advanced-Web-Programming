📹 VIDEO TOPIC:
🕐 COVERAGE: CrewAI Project Completion - Kickoff Implementation, Custom Tools (`BaseTool`), Autonomous Agent Execution, and Project Recap

**🔹 Implementing the CrewAI Kickoff (`main.py`)**
→ To run a CrewAI project, you must set up an entry point that initializes the crew and provides the initial input parameters. The instructor modifies the default `main.py` template by deleting the boilerplate and writing a clean `run()` function. A dictionary named `inputs` is created to pass specific variables (like `'sector': 'Technology'`) to the crew. The crew is then started using the `.kickoff()` method.

```python
import sys
import warnings
from datetime import datetime
from stock_picker.crew import StockPicker

warnings.filterwarnings("ignore", category=SyntaxWarning, module="pysbd")

def run():
    """Run the research crew."""
    # Define the initial variables required by the crew's tasks
    inputs = {
        'sector': 'Technology',
        # Note: The instructor mentions current_date is not actually needed/used in this specific implementation, so it is omitted.
    }
    
    # Create and run the crew by passing the inputs dictionary to the kickoff method
    result = StockPicker().crew().kickoff(inputs=inputs)
    
    # Print the raw string result of the crew's execution
    print("\n\n=== FINAL DECISION ===\n\n")
    print(result.raw)

if __name__ == "__main__":
    run()
```

**⭐ 🔹 Autonomy vs. Control in Agentic AI**
→ When the `crewai run` command is executed in the terminal, the crew begins processing. Because this project uses a **Hierarchical Process**, a Manager agent dynamically delegates tasks to worker agents (like the News Analyst and Researcher), who autonomously search the internet using tools (like Serper). The instructor highlights a crucial architectural trade-off regarding this framework:
- **The Pros (Autonomy):** The AI operates entirely independently. It figures out what to search for, evaluates the data, and orchestrates multiple agents to reach a final conclusion without human intervention.
- **The Cons (Lack of Predictability):** By giving the AI total autonomy, you surrender strict control over the execution path. The process becomes less predictable, and the system can take a long time "chugging away" as agents perform unexpected searches or delegate tasks multiple times before finishing.

> ⭐ **EXAM NOTE:** Understanding the trade-off between Agentic Autonomy and Execution Control is a fundamental architectural concept. You must know that highly autonomous systems (like Hierarchical CrewAI setups) offer powerful problem-solving capabilities but sacrifice predictability, execution speed, and granular control over the step-by-step workflow.

**🔹 Reviewing the Crew's Output and Structured Data**
→ Once the autonomous process completes, the final decision is printed to the terminal. In the first run, the agent recommended the AI company "Anthropic" over competitors like "Peregrine" (security) and "Circle" (crypto), notably using an OpenAI model to make this deduction. More importantly, the instructor checks the `/outputs` folder and verifies that `decision.md`, `research_report.json`, and `trending_companies.json` were generated successfully. The JSON files are formatted perfectly, proving that the structured output schema (defined via Pydantic in a previous step) worked exactly as intended.

**⭐ 🔹 Anatomy of a CrewAI Custom Tool (`BaseTool`)**
→ CrewAI generates a `tools` folder containing a `custom_tool.py` template. To allow an agent to interact with the outside world (in this case, sending a push notification), you must build a custom tool by inheriting from `BaseTool` and defining a Pydantic `BaseModel` for its inputs. The instructor renames the file to `push_tool.py` and explains the required structure:
1. **Input Schema (`BaseModel`):** You must define a Pydantic class that strictly describes the arguments the tool requires. The LLM reads the descriptions of these fields to understand what data to pass.
2. **Tool Definition (`BaseTool`):** The tool class itself requires a `name`, a clear `description` (which the LLM relies on to decide *when* to use the tool), and an `args_schema` pointing to your Pydantic input class.
3. **Execution Method (`_run`):** The core logic of the tool goes inside the `_run()` method, which accepts the parameters defined in the schema.

> ⭐ **EXAM NOTE:** You must memorize the components of a CrewAI Custom Tool. A valid tool requires inheriting `BaseTool`, defining a `name`, defining a `description` (crucial for LLM routing), mapping an `args_schema` to a Pydantic `BaseModel`, and implementing the tool's core logic inside the `_run()` method.

**🔹 Implementing the Pushover Notification Tool**
→ The instructor implements the custom tool to send a push notification to their phone using the Pushover API. It uses the `requests` library to make an HTTP POST request, pulling secure credentials from environment variables (`.env`).

```python
from crewai.tools import BaseTool
from typing import Type
from pydantic import BaseModel, Field
import os
import requests

# 1. Define the input schema
class PushNotificationInput(BaseModel):
    """Input schema for PushNotificationTool."""
    message: str = Field(..., description="The message to be sent to the user")

# 2. Define the Tool class
class PushNotificationTool(BaseTool):
    name: str = "Send a push notification"
    description: str = "This tool is used to send a push notification to the user."
    args_schema: Type[BaseModel] = PushNotificationInput

    # 3. Implement the execution logic
    def _run(self, message: str) -> str:
        # Retrieve credentials from the .env file
        pushover_user = os.getenv("PUSHOVER_USER")
        pushover_token = os.getenv("PUSHOVER_TOKEN")
        pushover_url = "https://api.pushover.net/1/messages.json"
        
        # Construct the API payload
        payload = {
            "user": pushover_user,
            "token": pushover_token,
            "message": message
        }
        
        # Send the POST request
        requests.post(pushover_url, data=payload)
        return "Notification sent successfully."
```

**⭐ 🔹 Assigning Custom Tools to Agents**
→ To give an agent access to a custom tool, you simply import the tool class and instantiate it inside the `tools` array parameter of the specific agent's definition. The instructor assigns this tool specifically to the `stock_picker` agent inside `crew.py`. Because the tool has a descriptive name and description, the LLM is smart enough to autonomously decide to invoke it when it finalizes its stock pick.

```python
# Inside crew.py
from tools.push_tool import PushNotificationTool

@agent
def stock_picker(self) -> Agent:
    return Agent(
        config=self.agents_config['stock_picker'],
        # Injecting the custom tool into the agent's toolkit
        tools=[PushNotificationTool()] 
    )
```

> ⭐ **EXAM NOTE:** Be prepared to identify how tools are bound to agents. In CrewAI, tools are passed as an instantiated list (e.g., `tools=[MyTool()]`) directly into the `Agent()` object constructor.

**🔹 Executing the Tool-Enabled Crew**
→ The instructor runs `crewai run` a final time. The autonomous process completes, this time recommending "Circle", and successfully invokes the `PushNotificationTool`. The instructor receives a real push notification on their device, validating that the agent successfully determined the final answer and executed external Python code to deliver the result.

**⭐ 🔹 Project Recap: 3 Core CrewAI Capabilities**
→ Wrapping up the Stock Picker project, the instructor emphasizes the three major advanced features implemented in this build compared to earlier, simpler projects.
1. **Structured Outputs:** Enforcing that the LLM returns data strictly conforming to a predefined JSON schema (via Pydantic).
2. **Hierarchical Process:** Moving away from sequential steps and using a Manager agent to autonomously delegate and orchestrate sub-agents.
3. **Custom Tools:** Writing arbitrary Python code wrapped in a `BaseTool` class, allowing the AI to interact with external APIs (like sending notifications).

> ⭐ **EXAM NOTE:** You should be able to list and explain these three specific capabilities. Questions may ask you to identify which CrewAI feature solves a specific problem (e.g., "How do you ensure data is returned in a predictable format for a database?" -> Structured Outputs. "How do you allow an agent to send an email?" -> Custom Tools).

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. **Autonomy vs. Control in Agentic AI**
2. **Anatomy of a CrewAI Custom Tool (`BaseTool`)**
3. **Assigning Custom Tools to Agents**
4. **Project Recap: 3 Core CrewAI Capabilities**