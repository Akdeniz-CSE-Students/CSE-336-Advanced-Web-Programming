


📹 VIDEO TOPIC: Introduction to API Interactions, Multi-LLM Orchestration, and Environment Setup
🕐 COVERAGE: Lab 2, Week 1, Day 3 - Setting up API keys, formatting message payloads, making calls to OpenAI, and orchestrating a multi-model evaluation.

**🔹 Teaching Philosophy: Code Execution Over Live Typing**
→ The instructor explains their specific pedagogical approach: rather than live-typing code (which slows down momentum), the course uses pre-written Jupyter Notebook cells. The instructor's method is to explain what the cell does, run it, inspect it, and print the results. Students are strongly encouraged to come back after the lecture, run the code themselves, add print statements, change variables, and experiment.

**🔹 Open Source Community Contributions (PRs)**
→ Students are encouraged to save their code experiments and variations in a folder named `community_contributions` and submit a Pull Request (PR) to the course's official GitHub repository. This practice helps other students learn from different variations and demonstrates a student's ability to collaborate in an open-source environment.

**🔹 Career Advice: Public Portfolio and Social Proof**
→ The instructor advises students to maintain their own personal GitHub repositories for the projects they build in the course. Furthermore, students are encouraged to post their learnings and project outcomes on LinkedIn and tag the instructor. This creates an amplification effect, drawing attention to the student's newly acquired skills, which is highly beneficial for attracting future clients or employers.

**🔹 Initialization and Package Imports**
→ The notebook begins with standard package imports: `os`, `json`, `dotenv`, `openai`, `anthropic`, and `IPython.display`. The instructor emphasizes the practical necessity of explicitly running this cell (`Shift + Enter`); failing to do so will result in `NameError` exceptions later in the notebook when these libraries are called.

**⭐ 🔹 Environment Variable Management (`load_dotenv`)**
→ To securely load API keys, the code uses the `dotenv` library. The instructor specifically highlights the use of the `override=True` parameter.
```python
from dotenv import load_dotenv
load_dotenv(override=True)
```
> ⭐ **EXAM NOTE:** Understanding the `override=True` parameter is critical; it ensures that variables defined in your local `.env` file explicitly take precedence and overwrite any pre-existing environment variables on your system with the same name.

**⭐ 🔹 API Providers and Pricing Models**
→ The instructor sets up connections to a multitude of LLM providers and details their general accessibility and pricing structures:
1. **OpenAI & Anthropic:** Typically require a minimum upfront deposit (e.g., $5 in the US) to begin using the API.
2. **DeepSeek:** Requires a minimum $2 upfront deposit.
3. **Google (Gemini):** Offers a free tier within certain usage and tiering limits.
4. **Groq:** Operates strictly on a pay-as-you-go model with no minimum upfront cost, noted for being exceptionally cheap and fast.
The notebook includes a validation script using `os.getenv("KEY_NAME")` to check which keys are active and prints a confirmation. It is perfectly fine if a student does not have all of them configured.
> ⭐ **EXAM NOTE:** Familiarity with the diverse landscape of LLM providers and their varying billing models (upfront vs. pay-as-you-go vs. free tiers) is essential for selecting the appropriate models for agentic architectures based on scaling and budget constraints.

**🔹 Exercise Goal: Multi-LLM Evaluation and Orchestration**
→ The core objective of the lab is to make calls to multiple LLMs to evaluate their intelligence. This serves as a foundational exercise in orchestration—a core design pattern in agentic systems where different models are sequentially or parallelly interacted with.

**🔹 Prompt Engineering: Formulating a Nuanced Request**
→ To test the models, the instructor tasks an LLM with generating the test question itself. The prompt is: *"Please come up with a challenging, nuanced question that I can ask a number of LLMs to evaluate their intelligence."*
To ensure the output is programmatic and clean, a strict constraint is appended: *"Answer only with the question, no explanation."*

**⭐ 🔹 Standard LLM Message Structure (Role/Content Mapping)**
→ The code formats the prompt into the standard construct expected by modern LLM APIs: a list of dictionaries mapping the `role` to the `content`.
```python
request = "Please come up with a challenging, nuanced question..."
messages = [{"role": "user", "content": request}]
```
> ⭐ **EXAM NOTE:** The list-of-dictionaries structure `[{"role": "user", "content": "..."}]` is the universal standard format for chat completion APIs across almost all providers. Knowing how to construct and append to this list is a mandatory fundamental skill.

**⭐ 🔹 System Messages vs. User Messages**
→ The instructor addresses the deliberate omission of a "system" message (e.g., `{"role": "system", "content": "You are a helpful assistant"}`). System messages are entirely optional. For straightforward tasks where the context and constraints are fully provided within the user prompt, a system message is not required.
> ⭐ **EXAM NOTE:** Recognizing that system prompts are optional is important for understanding minimal payload requirements and how context is distributed in API calls.

**⭐ 🔹 OpenAI Python Client API Structure**
→ The video demonstrates the exact sequence for instantiating the client, making a completion request, and parsing the response payload using the OpenAI SDK.
```python
# 1. Instantiate the client
openai = OpenAI()

# 2. Call the chat completions endpoint
response = openai.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages
)

# 3. Parse the specific text response from the nested object
question = response.choices[0].message.content
print(question)
```
> ⭐ **EXAM NOTE:** The specific object traversal syntax `response.choices[0].message.content` is the industry-standard way to extract the generated text string from an OpenAI chat completion response object. You must have this memorized for any code-level exam questions.

**🔹 Generating the Evaluation Question**
→ Executing the API call with the `gpt-4o-mini` model yields a highly complex question. The model asks: *"How would you analyze the ethical implications of using artificial intelligence in predictive policing, considering factors such as bias, accountability, and societal impact?"*

**🔹 Orchestration Setup: Tracking Competitors and Answers**
→ To prepare for comparing different models against this generated question, the instructor initializes two empty lists to store the state of the orchestration loop.
```python
competitors = []
answers =[]
```

**🔹 Executing the Evaluation Loop (Testing GPT-4o-mini)**
→ The instructor creates a new message payload where the user content is the *generated question*. They then query `gpt-4o-mini` to answer its own question, utilizing the lists to store the results.
```python
# Create new message payload with the complex question
messages = [{"role": "user", "content": question}]
model_name = "gpt-4o-mini"

# Call the API
response = openai.chat.completions.create(
    model=model_name,
    messages=messages
)
answer = response.choices[0].message.content

# Store the results for later comparison
competitors.append(model_name)
answers.append(answer)
```

**🔹 Rendering LLM Output (IPython Markdown)**
→ Because LLMs naturally generate output formatted in Markdown (using headers, bolding, and bullet points), printing the raw string in a Jupyter Notebook can look messy. The instructor uses the `display(Markdown(answer))` function from the `IPython.display` library to render the text properly. The resulting output from GPT-4o-mini is shown to be a robust, multi-faceted answer broken down by headers like Bias, Accountability, and Societal Impact.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Environment Variable Management (`load_dotenv`)
2. API Providers and Pricing Models
3. Standard LLM Message Structure (Role/Content Mapping)
4. System Messages vs. User Messages
5. OpenAI Python Client API Structure
