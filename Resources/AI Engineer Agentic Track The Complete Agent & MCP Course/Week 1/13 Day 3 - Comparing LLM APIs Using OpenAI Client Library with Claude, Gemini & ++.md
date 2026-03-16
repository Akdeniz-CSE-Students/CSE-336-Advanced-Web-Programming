


📹 VIDEO TOPIC: Evaluating and Calling Multiple LLM APIs (Anthropic, Gemini, DeepSeek, Groq, Ollama)
🕐 COVERAGE: Using Python Clients, API Compatibility, and Local Inference Setup

**🔹 Anthropic API and Claude 3.7 Sonnet Setup**
→ To interact with Anthropic's models (specifically Claude 3.7 Sonnet), you must use their dedicated Python client library rather than the standard OpenAI one, because their API structure is slightly different. You instantiate the client and call the `messages.create` method. A critical distinction is that Anthropic **strictly requires** you to define a `max_tokens` parameter limit, whereas OpenAI's API does not force this requirement. 

```python
import anthropic

# Create an Anthropic client instance
claude = anthropic.Anthropic()

# Call the API using Anthropic's specific syntax
response = claude.messages.create(
    model="claude-3-7-sonnet-latest",
    messages=messages_array,
    max_tokens=1000  # Anthropic MANDATES this parameter
)
```

**⭐ 🔹 The OpenAI Client Library as a Universal API Wrapper**
→ A major surprise in the AI engineering space is that you can use the **OpenAI Python client library** to call models from completely different providers like Google, DeepSeek, and Groq. The OpenAI library is simply a lightweight HTTP wrapper that sends web requests containing specific JSON structures (like lists of dictionaries representing message history). Because OpenAI's API format became an industry standard, other providers intentionally built endpoints that mimic this exact specification. By swapping out the `base_url` and `api_key` during client initialization, you can redirect the OpenAI library to talk to Google, DeepSeek, or local servers.

> ⭐ **EXAM NOTE:** Understanding how to override the `base_url` within the OpenAI client is a standard industry practice. It allows developers to test and switch between different model providers seamlessly without rewriting their core application logic or learning new SDKs.

**🔹 Google Gemini API Setup via OpenAI Client**
→ Google provides an OpenAI-compatible endpoint specifically for calling models like Gemini 2.0 Flash. To use it, you initialize the OpenAI client but provide your Google API key and point the base URL to Google's specialized OpenAI route.

```python
from openai import OpenAI

# Overriding the OpenAI client to hit Google's servers
gemini = OpenAI(
    api_key=google_api_key,
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)

# Standard OpenAI syntax, but it routes to Gemini
response = gemini.chat.completions.create(
    model="gemini-2.0-flash",
    messages=messages_array
)
```

**🔹 DeepSeek API Setup via OpenAI Client**
→ DeepSeek hosts a massive, full-sized model containing 671 billion parameters. Unlike Anthropic or Google, DeepSeek **does not have its own Python client library**. They rely entirely on developers using the OpenAI library and redirecting it to their endpoint.

```python
from openai import OpenAI

# Overriding the OpenAI client to hit DeepSeek's servers
deepseek = OpenAI(
    api_key=deepseek_api_key,
    base_url="https://api.deepseek.com/v1"
)
```

**🔹 DeepSeek Model Variants (Chat vs. Reasoner)**
→ Through the DeepSeek API, you can access two primary model variants:
1. `deepseek-chat`: The standard chat model.
2. `deepseek-reasoner`: DeepSeek's famous R1 reasoning model.
The instructor intentionally uses `deepseek-chat` to maintain an apples-to-apples comparison with other standard (non-reasoning) chat models evaluated in the video.

**🔹 Groq API Setup and Fast Inference Hardware**
→ Groq (spelled with a 'q', completely distinct from Elon Musk's Grok) is an AI provider that builds specialized hardware designed specifically for blazing-fast inference. Like DeepSeek, they offer an OpenAI-compatible endpoint.

```python
from openai import OpenAI

# Overriding the OpenAI client to hit Groq's fast inference servers
groq = OpenAI(
    api_key=groq_api_key,
    base_url="https://api.groq.com/openai/v1"
)
```

**🔹 Llama 3.3 70B Model via Groq**
→ Using the Groq endpoint, the instructor tests the `llama-3.3-70b-versatile` model. Despite "only" having 70 billion parameters, this model is incredibly powerful, performing on par with or even outperforming the older, much larger Llama 3.1 405B model. Thanks to Groq's specialized hardware, the lengthy generation of text happens almost instantaneously.

**⭐ 🔹 Ollama: Architecture and Local Endpoint**
→ Ollama is a piece of software that runs locally on your computer. Built with highly optimized C++ code, its purpose is to run open-source models locally and wrap them in a web service. Crucially, Ollama spins up a web server on your `localhost` that perfectly matches the OpenAI API specification. This allows your existing Python code to seamlessly talk to local hardware just by changing the URL.

> ⭐ **EXAM NOTE:** Knowing how Ollama creates an OpenAI-compatible local endpoint is vital for privacy-first, offline, or low-cost agentic system development. It allows you to build agents with API logic, but execute inference entirely on local compute.

**🔹 Ollama Installation and Verification**
→ To set up Ollama:
1. Visit `ollama.com` and click download.
2. Install the software for your OS.
3. Verify it is running by opening a web browser and navigating to `http://localhost:11434`. You should see a simple webpage stating **"Ollama is running"**.
4. If it is not running, you can manually start the background server by opening a terminal and running the command: `ollama serve`.

**🔹 Ollama CLI Commands**
→ Ollama provides a command-line interface to manage your local models. 
- `ollama pull <model_name>`: Downloads a specific model to your local machine (e.g., `ollama pull llama3.2`).
- `!ollama pull <model_name>`: Use this format with an exclamation mark to execute the pull command directly from within a Jupyter Notebook or Cursor environment.
- `ollama ls`: Lists all the models currently downloaded on your machine.
- `ollama rm <model_name>`: Deletes a specific model from your local storage.

**⭐ 🔹 Ollama Hardware Constraints and Model Sizing Warning**
→ The instructor gives a severe warning regarding model selection in Ollama. You **must not** attempt to run massive models like Llama 3.3 (70B parameters) locally on a standard laptop. A model of that size requires up to 100GB of storage and RAM, making it strictly intended for massive computer clusters or high-end cryptocurrency mining rigs. Attempting to run it on a standard machine will crash the computer and swallow all system resources.

> ⭐ **EXAM NOTE:** Understanding parameter size versus local compute capability is critical. Selecting inappropriate model sizes will cause system failure. A standard local machine is limited to small-parameter models (typically under 8 billion parameters).

**🔹 Recommended Local Models for Ollama**
→ For local execution on standard hardware, the instructor recommends smaller open-source models:
- **Llama 3.2** (Meta): Comes in 3 Billion (`llama3.2`) and 1.5 Billion (`llama3.2:1b`) parameter variants.
- **Qwen** (Alibaba Cloud)
- **Gemma** (Google)
- **Phi** (Microsoft)
- **DeepSeek Distilled Models**: Smaller models trained synthetically on outputs from the larger DeepSeek models.
*(Note: A full directory of models and sizes can be found at `ollama.com/library`)*

**🔹 Ollama Python API Setup via OpenAI Client**
→ Because Ollama mimics OpenAI, you use the standard OpenAI client to call it, pointing it at the local port. 

```python
from openai import OpenAI

# Point the OpenAI client to your local Ollama server
ollama_client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama" # The API key doesn't matter for local execution, but the library requires a string to be passed here.
)

# The model name MUST perfectly match the string of the model you pulled locally
response = ollama_client.chat.completions.create(
    model="llama3.2", 
    messages=messages_array
)
```
→ *Performance Analogy:* The instructor notes that running a small local model like Llama 3.2 (3B) yields a noticeably shorter, more "mediocre/bored" response compared to the massive API-based models. This illustrates the inherent limits of smaller local models when answering complex ethical prompts.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. The OpenAI Client Library as a Universal API Wrapper
2. Ollama: Architecture and Local Endpoint
3. Ollama Hardware Constraints and Model Sizing Warning
