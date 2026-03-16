
📹 VIDEO TOPIC: Master AI Agentic Engineering - Orchestrating Multiple LLMs & The "Cast of Characters"
🕐 COVERAGE: Introduction to Day 3, Orchestrating Multiple LLMs, Cloud vs. Local Deployment, Survey of Major LLMs (OpenAI, Anthropic, Google, DeepSeek, Groq, Ollama), Model Evaluation, and Troubleshooting Patterns.

**🔹 Day 3 Objective: Orchestrating Multiple LLMs**
→ Having built a basic agentic workflow in the previous lessons, Day 3 marks a transition into highly practical, code-heavy implementation. The primary goal is learning how to orchestrate between multiple Large Language Models (LLMs). This means architecting systems where numerous LLMs are called sequentially or concurrently to complete complex tasks, rather than relying on a single monolithic prompt to a single model. 

**🔹 Model Selection Flexibility: Paid vs. Open-Source ($0 Spend Option)**
→ Throughout the course, students will be writing code that calls both paid APIs (in the cloud) and open-source models (both cloud-hosted and run locally). The instructor emphasizes that students have complete flexibility in choosing which models to use at any point in the course. A highly recommended exercise is taking the provided code—which might be configured for a specific paid API—and adapting it to run entirely on free, open-source local models. This allows students to complete the course spending $0, though they must account for the fact that performance and reasoning capabilities will vary depending on the chosen model.

**🔹 Pre-requisite Knowledge Disclaimer: LLM Selection and Deployment**
→ The instructor explicitly avoids diving into the deep technical details of *how* to select, fine-tune, or deploy LLMs to production servers, referring to it as a "rabbit hole" that would distract from the core focus of agentic engineering. He assumes students come in with a basic understanding of closed-source versus open-source models (like the concept of "inference"). Students needing foundational knowledge on model deployment are pointed toward his other dedicated course, "LLM Engineering."

**⭐ 🔹 The Cast of Characters: OpenAI Models & Reasoning Architectures**
→ OpenAI is the first major player introduced. The course primarily relies on `gpt-4o-mini`, which is recognized as the most well-known, cost-effective model for everyday tasks. Its larger cousin, `gpt-4o`, is also available for more complex needs. Crucially, the instructor introduces OpenAI's "Reasoning Models" (specifically mentioning `o1` and `o3-mini`). These models are architecturally different because they are trained to "think through their steps" internally before outputting a conclusion. By forcing the LLM to process steps in an agentic, workflow-like manner hidden in the background, these reasoning models yield significantly better and more accurate outcomes for complex problems. 

> ⭐ **EXAM NOTE:** Understanding the distinction between standard generative models (like `gpt-4o-mini`) and reasoning models (like `o1` / `o3-mini`) is highly likely to be tested. You must know that reasoning models natively employ a step-by-step internal workflow before generating an answer, structurally mimicking an agentic thought process to improve accuracy.

**🔹 The Cast of Characters: Anthropic Models**
→ Anthropic is introduced as OpenAI's great rival, founded by former OpenAI employees. The model that will see the most use in this course from their lineup is `Claude-3-7-Sonnet`, a powerful but fairly inexpensive model. For even more cost-sensitive operations, the instructor highlights `Claude-3-Haiku`, which offers a significantly cheaper alternative for high-volume, lower-complexity tasks.

**🔹 The Cast of Characters: Google Models**
→ Google's offering in this ecosystem is the `Gemini` family. While there is a Pro version available, the course specifically utilizes `Gemini-2.0-Flash`. The massive advantage of Flash at the time of the recording is that it is effectively free to use via their API (as long as you stay within certain usage rate limits). This makes it an ideal choice for students wanting access to a "frontier" (state-of-the-art) cloud model without incurring API costs.

**⭐ 🔹 The Cast of Characters: DeepSeek AI (Innovation in Training & Distillation)**
→ DeepSeek AI is highlighted as a Chinese startup that shocked the AI industry with its highly capable `DeepSeek V3` and `DeepSeek R1` models. The instructor notes that their true industry sensation wasn't necessarily that they beat OpenAI's absolute peak performance (they were slightly behind), but rather their astonishing efficiency: they achieved frontier-level performance while spending a fraction of the cost (approximately 30 times less spend) compared to OpenAI. 
Because the main DeepSeek model has a massive 671 billion parameters, it is far too large for a standard developer to run locally. To solve this, DeepSeek released **Distilled Versions**. These are much smaller, open-source models (based on existing architectures like Meta's `Llama` and Alibaba's `Qwen`) that have been fine-tuned using the high-quality data generated by the massive DeepSeek model. These distilled versions are free and small enough to run on local hardware.

> ⭐ **EXAM NOTE:** The concept of "Distillation" as explained via DeepSeek is a critical exam topic. You must understand that a distilled model is a smaller, cheaper model (like a small Llama) that is fine-tuned using the outputs of a massively larger, expensive model (like the 671B DeepSeek), allowing developers to access high-tier reasoning on local, consumer-grade hardware.

**🔹 The Cast of Characters: Groq (Inference Infrastructure)**
→ The instructor carefully distinguishes **Groq** (spelled with a 'q') from Grok (spelled with a 'k', which is X/Twitter's AI model). Groq (with a 'q') is an infrastructure company that provides extremely cheap and lightning-fast "inference" (the runtime execution of an AI model). Using Groq's API, developers can run massive open-source models—such as Meta's `Llama 3.3` (a 70 billion parameter model)—at blazingly fast speeds and incredibly low cost, bypassing the need for heavy local hardware. 

**⭐ 🔹 The Cast of Characters: Ollama & Local Execution (`llama.cpp`)**
→ Ollama is introduced not as a model, but as a local execution platform. It allows developers to download and run open-source models directly on their own computers. Crucially for agentic engineering, Ollama spins up local API endpoints that are structurally consistent/compatible with standard OpenAI endpoints. This means code written for OpenAI can often be pointed at a local Ollama server with minimal changes. Under the hood, Ollama achieves high-performance local execution by utilizing highly optimized C++ code via a library called `llama.cpp`.

> ⭐ **EXAM NOTE:** Ollama is a foundational tool for local agentic architecture. You must know that it relies on `llama.cpp` for optimized local execution and that its primary architectural benefit is providing local endpoints that mimic the standard OpenAI API structure, enabling seamless code portability from cloud to local.

**🔹 Model Evaluation: The Vellum Leaderboard**
→ To navigate this wide array of models, the instructor (jokingly referring to himself as a "leader-bore" due to his fanaticism for metrics) recommends the **Vellum Leaderboard** (https://www.vellum.ai/llm-leaderboard). This resource provides a side-by-side comparison of both closed-source and open-source models. It breaks down critical architectural metrics including:
- API Costs
- Context window sizes
- Input and Output token pricing
- Key benchmark performances across multiple dimensions.
It serves as an essential tool for an AI engineer to gauge costs and capabilities before assigning an LLM to a specific node in an agentic workflow.

**🔹 Three Core Principles for the Course**
→ Before starting the hands-on lab, the instructor outlines three vital rules of engagement:
1. **Use the resources:** Rely on the provided website (videos/links) and the GitHub repo (guides/troubleshooting). Because AI moves rapidly, the GitHub labs are constantly updated to reflect new models that drop after the videos are recorded.
2. **Stay patient:** Having a "thick skin" is mandatory. Students *will* hit roadblocks and bugs. The instructor emphasizes that real learning and concept retention happen during the pain of debugging, diagnosing, and fixing broken code.
3. **Contact the instructor:** Students are encouraged to reach out via LinkedIn or email if they are completely stuck; the instructor wants to ensure no one is "suffering in pain" needlessly.

**⭐ 🔹 The "Manual Evaluator-Optimizer" Workflow Pattern**
→ As a final tip for problem-solving, the instructor outlines a manual way to emulate an agentic workflow using multiple LLMs. If a student is stuck on a coding problem, they should not just ask ChatGPT. Instead, they should execute the following sequence:
1. Ask the coding question to ChatGPT.
2. Take the generated response/code from ChatGPT.
3. Feed that response into Claude (Anthropic).
4. Ask Claude: *"I received this response to my problem. Do you agree with it? Is it accurate?"*
This technique pits two different models against each other, allowing one to evaluate and optimize the output of the other, directly mirroring the automated "Evaluator-Optimizer" architectural pattern used in agentic systems.

> ⭐ **EXAM NOTE:** This manual trick perfectly illustrates the "Evaluator-Optimizer" pattern. You must be able to recognize this pattern—where one LLM acts as a generator and a distinct, separate LLM acts as an independent evaluator/critic to improve the final output accuracy.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. The Cast of Characters: OpenAI Models & Reasoning Architectures
2. The Cast of Characters: DeepSeek AI (Innovation in Training & Distillation)
3. The Cast of Characters: Ollama & Local Execution (`llama.cpp`)
4. The "Manual Evaluator-Optimizer" Workflow Pattern
