


📹 VIDEO TOPIC: Windows PC Setup for Agentic AI Engineering
🕐 COVERAGE: Git, Cursor IDE, uv Package Manager, OpenAI API Keys, and Environment Variables (.env)

**🔹 GitHub Repository and README**
→ The course utilizes a central GitHub repository (`github.com/ed-donner/agents`) containing all code, guides, and assets for the 6-week journey from foundations to MCP (Model Context Protocol). The instructor emphasizes that reading the repository's `README.md` and specific setup instructions (for Windows, Mac, or Linux) is a critical prerequisite, as it acts as a living document continuously updated with solutions to common problems students face.

**⭐ 🔹 Windows Setup Gotchas (Long Paths & Antivirus)**
→ Before starting the technical setup, the instructor highlights two common "traps" or "gotchas" specific to Windows machines that can cause the entire setup to fail:
1. **Long File Names:** Windows historically restricts file paths to 260 characters. If this limit is hit during package installation or repository cloning, errors will occur. This restriction must be explicitly disabled in Windows settings to allow longer file names.
2. **Antivirus Software:** Aggressive antivirus software can interfere with the installation of developer tools or the execution of terminal scripts, requiring temporary pausing or the addition of specific exceptions.
> ⭐ **EXAM NOTE:** Understanding these system-level constraints is crucial for troubleshooting environment setup issues, as they are the most common invisible blockers when configuring a Windows machine for modern Python development.

**🔹 PowerShell and Project Directory Setup**
→ The setup requires a command-line interface, specifically Windows PowerShell (not the older Command Prompt, though guides are provided if needed). The instructor advises creating a dedicated directory for all coding projects to maintain organization.
1. Open Windows PowerShell from the Start menu.
2. Create a projects directory in the home folder: `mkdir projects`
3. Navigate into the new directory: `cd projects`

**⭐ 🔹 Git and Cloning the Repository**
→ Git is the foundational code management tool used to pull the course materials onto the local machine. The process involves verifying Git is installed and then creating a local copy of the remote repository to establish the project base.
1. Check if Git is installed: `git --version`
2. If not installed, download the standalone installer for Windows from the official Git website and install it.
3. Once Git is active (which may require restarting the PC or PowerShell), navigate to the `projects` directory.
4. Clone the repository using the HTTPS URL copied from GitHub:
   ```powershell
   git clone https://github.com/ed-donner/agents.git
   ```
5. Navigate into the newly cloned directory: `cd agents`
> ⭐ **EXAM NOTE:** `git clone` is the fundamental command used to duplicate a remote repository to a local machine. Knowing how to retrieve project files using Git is a strict prerequisite for any Agentic Engineering pipeline.

**🔹 Project Root Directory**
→ The folder created by cloning the repository (in this case, `projects/agents`) is referred to as the "Project Root Directory." This is the foundational context for all terminal commands, package syncing, and relative file paths within the course. 

**⭐ 🔹 Cursor AI Code Editor Installation & Setup**
→ Cursor is the recommended IDE (Integrated Development Environment) for the course. It is built specifically for AI-assisted coding and acts as the primary interface for building agentic systems.
1. Download the Windows executable from `cursor.com`.
2. Run the installer, accept agreements, and choose the installation path.
3. Select additional tasks: Create a desktop icon, Add "Open with Cursor" to Windows Explorer, and **Add to PATH** (which requires a shell restart to take effect).
4. On first launch, configure basic settings: Keyboard shortcuts, Command Line installation (allowing `code` or `cursor` commands from the terminal to launch the IDE), and Data Preferences.
5. Select **Privacy Mode** to ensure your code and prompts are not logged or stored by third parties.
6. Sign up for a free Cursor account and log in.
7. Open the project by navigating to `This PC > C: > Users > [Username] > projects > agents` and selecting the folder.
> ⭐ **EXAM NOTE:** Cursor is the primary tool used to write and execute code in this track. Understanding how to integrate it with the system PATH and strictly configuring Privacy Mode is important for secure enterprise-grade AI development.

**⭐ 🔹 uv Package Manager by Astral**
→ `uv` is an extremely fast Python package installer and project manager written in Rust, created by Astral (the creators of Ruff). It replaces traditional, slower tools like `pip`, `poetry`, or Anaconda. It manages Python versions, dependencies, and virtual environments seamlessly in one tool.
To install `uv` on Windows, run the following command in a fresh PowerShell window:
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```
After installation, you must exit and restart the PowerShell shell for the PATH changes to take effect, then verify the installation with the command `uv --version`.
> ⭐ **EXAM NOTE:** `uv` is heavily emphasized as the modern, high-performance standard for Python dependency management. You must know that it is written in Rust, acts as a drop-in replacement for pip/conda, and drastically reduces environment setup time.

**⭐ 🔹 Building the Environment (`uv sync` & `.venv`)**
→ Once `uv` is installed, it is used to build an isolated Python environment specific to the project, ensuring all dependencies match the course requirements without interfering with the global Windows system.
1. Open the integrated terminal inside Cursor (View > Terminal).
2. Run the command `uv sync`.
3. `uv` automatically resolves and downloads a dedicated version of Python (e.g., Python 3.12) required by the project specifications.
4. It installs all necessary packages.
5. It creates a hidden `.venv` (virtual environment) folder in the project root containing the completely isolated Python setup.
*Comparison:* The instructor compares `uv` to Anaconda. While setting up a full, isolated Python environment with Anaconda can take an hour to an hour and a half, `uv` accomplishes the exact same task in roughly 1 to 5 minutes due to its Rust-based caching and resolution architecture.
> ⭐ **EXAM NOTE:** `uv sync` is the critical command used to synchronize the local project environment with the declared dependencies, automatically handling Python versioning and virtual environment (`.venv`) creation in a fraction of traditional times. 

**⭐ 🔹 Running Scripts with uv (`uv run`)**
→ To execute Python scripts within the securely isolated virtual environment created by `uv`, you do not use the standard `python script.py` command. Instead, you use the `uv` execution wrapper:
```powershell
uv run <script_name>
```
This ensures the script runs using the specific Python binary and dependencies located safely in the local `.venv` folder, rather than defaulting to a potentially conflicting global system Python installation.
> ⭐ **EXAM NOTE:** Understanding `uv run` is exam-critical. It represents the operational boundary of the isolated environment; failing to use it may result in "Module Not Found" errors because the system will look in the global environment instead of the project environment.

**⭐ 🔹 API Keys and Billing (OpenAI & Alternatives)**
→ To build agentic systems, the code must programmatically communicate with frontier AI models (LLMs). This requires an API key—a unique authentication string acting as a machine-to-machine password.
1. **OpenAI Setup:** Go to the developer platform at `platform.openai.com`.
2. **Billing:** API access is strictly "pay as you go." You must navigate to Settings > Billing and add a credit balance (minimum $5 upfront) to activate the API. 
3. **Key Creation:** Navigate to the API Keys section and click "Create new secret key". Assign it to the default project, give it all permissions, name it, and copy the resulting string (which begins with `sk-proj-...`).
4. **Alternatives:** If developers prefer not to pay for OpenAI, the course architecture fully supports alternatives like DeepSeek, Google's Gemini, Groq, or OpenRouter as drop-in replacements.
> ⭐ **EXAM NOTE:** API keys are the fundamental authentication mechanism for Agentic Engineering. You must know that consumer subscriptions (like ChatGPT Plus) do not cover API usage; the developer platform requires separate, prepaid billing to execute programmatic LLM calls.

**⭐ 🔹 The API Key Word Processor Trap**
→ The instructor explicitly warns of a highly specific but common trap when handling API keys: copying an API key into a word processor (like Microsoft Word or Apple Pages) for temporary storage.
*The Problem:* Word processors utilize "smart formatting." They will automatically replace standard hyphens (`-`) inside the API key string with long hyphens (em-dashes) or alter character encodings to match local international settings. If you paste this visually identical but structurally altered key into your code, the API will reject it as invalid, causing a frustrating authentication error.
*The Solution:* Only paste API keys directly into plain text editors or directly into your project's environment file.
> ⭐ **EXAM NOTE:** This is a classic debugging scenario. If an API key is structurally invalid despite being copied directly from the provider, "smart formatting" from an intermediary word processor or rich-text environment is the primary architectural suspect.

**⭐ 🔹 Environment Variables and the `.env` File**
→ API keys are highly sensitive "secrets" that must never be hardcoded into source code or uploaded to public version control repositories like GitHub. To manage this safely, developers use a local environment file.
1. In the Project Root Directory in Cursor, create a new file and name it exactly `.env` (starting with a dot, followed by e, n, v).
2. Inside this file, declare the environment variables in a strict `KEY=VALUE` format without spaces or quotation marks around the values.
```env
OPENAI_API_KEY=sk-proj-123456789abcdef...
DEEPSEEK_API_KEY=your_deepseek_key_here
GROQ_API_KEY=your_groq_key_here
```
This file acts as a local vault. The system will load these variables into the environment at runtime, allowing the agentic code to access the keys securely without exposing them in the codebase.
> ⭐ **EXAM NOTE:** The `.env` file pattern is the universal standard for secret management. You must know its exact naming convention (starting with a dot), its exact internal syntax (`KEY=VALUE` without quotes or spaces), and its primary security purpose: keeping secrets out of source control.

***
## ⭐ MUST-KNOW LIST (Exam-Critical Concepts)
1. Windows Setup Gotchas (Long Paths & Antivirus)
2. Git and Cloning the Repository
3. Cursor AI Code Editor Installation & Setup
4. uv Package Manager by Astral
5. Building the Environment (`uv sync` & `.venv`)
6. Running Scripts with uv (`uv run`)
7. API Keys and Billing (OpenAI & Alternatives)
8. The API Key Word Processor Trap
9. Environment Variables and the `.env` File
