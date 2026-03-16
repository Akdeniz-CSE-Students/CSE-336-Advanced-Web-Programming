


📹 VIDEO TOPIC: Mac/Linux Environment Setup for AI Agent Development
🕐 COVERAGE: Complete 5-Step Workflow for Bootstrapping an Agentic AI Development Environment (Terminal, Git, Cursor, `uv`, OpenAI API, and `.env`)

***

**🔹 Target Audience & OS Prerequisites**
→ This setup guide is strictly designed for macOS and Linux users. The instructor explicitly notes that Windows/PC users should skip this video and proceed to the dedicated Windows setup lecture.

**🔹 Opening the Terminal (macOS)**
→ The terminal is the primary interface for setting up the development environment. For macOS users unfamiliar with finding it, it is located by navigating through the Finder to `Applications` → `Utilities` → `Terminal`.

**🔹 5-Step Setup Process Overview**
→ The instructor outlines a structured, 5-step pipeline for configuring the environment:
1. Clone the GitHub Repository
2. Install the Cursor AI IDE
3. Install and configure the `uv` package manager
4. Set up an OpenAI API Key
5. Store the API Key securely in a `.env` file

**🔹 Establishing a Projects Directory**
→ It is a common developer best practice to have a dedicated directory for local code repositories, typically named `projects` inside the home directory. 
- You can check your current working directory using `pwd`.
- If the directory does not exist, you create it using `mkdir projects` and then navigate into it using `cd projects`.

**🔹 Course GitHub Repository & Project README**
→ The course code is hosted on GitHub at `github.com/ed-donner/agents`. The instructor heavily emphasizes reading the `Project README` located at the root of the repository. It contains vital up-to-date links, API cost warnings, troubleshooting notebooks, and detailed written instructions that supplement the video.

**🔹 Verifying Git Installation**
→ Before cloning the repository, you must verify that Git is installed on your local machine. 
- You do this by running:
  ```bash
  git --version
  ```
- If Git is not installed (or if it returns an error), macOS users can install the fundamental developer tools by running:
  ```bash
  xcode-select --install
  ```

**🔹 Cloning the Repository**
→ To bring the code to your local machine, you navigate to the GitHub repository, click the green "Code" button, select the **HTTPS** tab, and copy the provided URL. You then execute the clone command in your `projects` directory.
  ```bash
  git clone https://github.com/ed-donner/agents.git
  ```
This downloads the code and creates a new directory named `agents`. You must then navigate into it using `cd agents`.

**⭐ 🔹 Project Root Directory**
→ The top-level folder of your newly cloned repository (in this case, `agents/` inside your `projects` folder) is referred to as the "Project Root Directory" (or "Project Roots"). The instructor notes that this term will be used constantly throughout the course. It is the architectural anchor point where configuration files and environment variables must reside.
> ⭐ **EXAM NOTE:** Understanding what the Project Root Directory is, and that critical files like `.env` and `pyproject.toml` belong there, is essential for debugging pathing and environment issues in any software project.

**🔹 Cursor: The AI Code Editor**
→ Cursor is an AI-native Integrated Development Environment (IDE) built to make coding with AI exceptionally productive. 
- To install, navigate to `cursor.com`, sign up for an account (the instructor uses a Google credential and a Pro trial), download the macOS installer, run it, and drag the application to the `Applications` folder.
- You open the project by launching Cursor, clicking "Open Project", and navigating to your Project Root Directory (`projects/agents`).

**🔹 Cursor UI Breakdown**
→ Upon opening a project in Cursor, the instructor highlights three main UI areas:
1. **Explorer (Left Sidebar):** Shows all files organized by directory.
2. **Welcome Screen/Main Editor (Center):** The primary coding space (can be closed initially).
3. **Chat Screen (Right Sidebar):** Where you interact with the AI to discuss, write, and troubleshoot code.

**⭐ 🔹 The `uv` Package Manager**
→ `uv` is an extremely fast Python package and project manager written in Rust. The instructor highly recommends it over traditional tools like Anaconda. While Anaconda can take an hour to resolve and build environments, `uv` performs the same tasks in 3 to 4 minutes. It is becoming the standard for modern AI/Agentic platforms.
> ⭐ **EXAM NOTE:** You should know `uv` as a high-performance alternative to pip/conda, specifically recognized for its speed (written in Rust) and its ability to act as a drop-in replacement for environment resolution.

**🔹 Installing `uv` via Terminal**
→ To install `uv` on macOS/Linux, you run a curl command. You can do this in the standard Mac terminal or inside Cursor's integrated terminal.
- To open Cursor's integrated terminal, you go to `View -> Terminal` or use the keyboard shortcut `Control + \` (backtick/tick mark).
- The installation command is:
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- After installation, you may need to close and reopen the terminal to refresh environment variables. Verify it by running:
  ```bash
  uv --version
  ```

**⭐ 🔹 Building the Environment with `uv sync`**
→ Once `uv` is installed, you must build your isolated project environment. You do this by running a single command inside the Project Root Directory:
  ```bash
  uv sync
  ```
→ **How it works:** `uv` scans the directory for configuration files like `.python-version`, `pyproject.toml`, and `uv.lock`. It reads the dependencies listed in these files and downloads them to create an isolated environment.
> ⭐ **EXAM NOTE:** `uv sync` is the specific command used to synchronize and build your project's environment based on the dependencies locked in `pyproject.toml` and `uv.lock`. 

**🔹 The `.venv` Directory**
→ After running `uv sync`, a new hidden directory called `.venv` will appear in your Project Root. This represents a traditional Python Virtual Environment, which isolates your project's dependencies from your system-wide Python installation.

**🔹 Python Versioning in `uv`**
→ When running `uv sync`, `uv` automatically downloads and installs a dedicated version of Python for the project if you don't already have it. For this course, it utilizes **Python 3.12** because the latest version (3.13) is not yet fully compatible with all necessary data science packages.

**⭐ 🔹 Executing Scripts: `uv run` vs `python`**
→ In traditional setups, you execute scripts by typing `python script.py`. In a `uv`-managed project, you must prepend your commands with `uv run`.
  ```bash
  uv run script.py
  ```
→ **Why it matters:** Using `uv run` guarantees that the script executes *inside* the isolated `.venv` environment utilizing the exact dependencies and Python version defined by the project, avoiding system-level conflicts.
> ⭐ **EXAM NOTE:** You will likely be tested on how to properly execute code within a `uv` environment. The correct answer is always prefixing the execution command with `uv run`.

**🔹 Setting up the OpenAI API Key**
→ To use OpenAI's models, you need an API key (though alternatives like DeepSeek can be used instead).
1. Go to `platform.openai.com` and log in.
2. Navigate to `Settings -> Billing`. OpenAI requires a prepaid credit balance to function (the minimum top-up is $5).
3. Navigate to the `API keys` section.
4. Click "Create new secret key".
5. Assign it a name, select "Default project", ensure Permissions are set to "All", and generate the key.
6. Copy the key to your clipboard (it will look like `sk-proj-...`).

**⭐ 🔹 The "Rich Text Hyphen" Trap**
→ The instructor gives a severe warning regarding copying and pasting API keys. Do NOT paste your API key into a rich-text editor (like Notepad, Word, or Apple TextEdit) to "save it for later." 
→ **Why:** These programs often apply auto-formatting, changing standard hyphens (`-`) into long em-dashes (`—`). If this altered key is pasted into your code, it will invalidate the key and cause authentication failures that are incredibly difficult to debug because the key *looks* correct.
> ⭐ **EXAM NOTE:** Be aware of environment variable string corruption caused by rich text editors formatting hyphens. Always paste keys directly into raw text/code editors like Cursor.

**⭐ 🔹 Creating the `.env` File**
→ Environment variables (like API keys) must be stored securely. 
1. In Cursor, right-click in the Explorer at the Project Root level and select "New File".
2. Name it EXACTLY `.env` (a period followed by env). Do not misspell it (e.g., `.envi` or `env.txt`). 
→ **Why it matters:** The `.env` file is explicitly ignored by Git (via the `.gitignore` file). This ensures that your private passwords, tokens, and keys are never uploaded to a public GitHub repository.
> ⭐ **EXAM NOTE:** `.env` files are the industry standard for storing environment variables securely. Understanding that this file is deliberately ignored by version control to prevent credential leaks is a fundamental security concept.

**🔹 Defining the API Key in `.env`**
→ Inside the freshly created `.env` file, paste your API key using exact syntax:
  ```env
  OPENAI_API_KEY=sk-proj-1234567890...
  ```
→ **Critical Syntax Rules:**
- The variable name must be exactly `OPENAI_API_KEY` (not `OPEN_API_KEY` or `OPENAI_KEY`). If the spelling is off by a single character, the codebase will not detect it.
- Do NOT put spaces around the equals sign (`=`).
- Do NOT wrap the key in quotation marks (`"` or `'`).

***

## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Project Root Directory
2. The `uv` Package Manager
3. Building the Environment with `uv sync`
4. Executing Scripts: `uv run` vs `python`
5. The "Rich Text Hyphen" Trap
6. Creating the `.env` File
