Initialize Project Context Infrastructure

CONTEXT:
We are configuring a local, filesystem-driven context management system for this repository. This infrastructure must be compatible with terminal agents (like Claude Code), AI-native IDEs (like Antigravity), and custom MCP pipelines. It relies on explicit markdown files to pass rules and state seamlessly between different AI models.

TASK:
Analyze the current repository structure, primary programming languages, configuration files, and frameworks. Then, autonomously create the following directory layout and markdown files at the root of this project:

.context/
├── CORE_RULES.md         # Global instructions, tech stack definitions, and strict code style constraints.
├── ARCHITECTURE.md      # A human-and-agent readable map of the file layout, entry points, and API routes.
└── PROGRESS.md          # Active state tracking log, current task checklist, and blocker registry.

COMPLIANCE CRITERIA FOR THE FILES:
1. In 'CORE_RULES.md', include a "Banned Anti-Patterns" section specifying common model hallucinations or deprecated methods for our exact tech stack. Add instructions telling any model to strictly fail-fast and ask the user for clarity instead of guessing if a file path or dependency is missing.
2. In 'ARCHITECTURE.md', map out the directory paths. Write a concise 1-2 line index summarizing the engineering purpose of each major folder so an external model doesn't have to recursively list paths.
3. In 'PROGRESS.md', establish a clean, standardized Markdown state template. Include blocks for [Completed], [In Progress], [Next Steps], and [Discovered Gotchas] so subsequent models can read and write to this file to update session memory.
4. In 'CORE_RULES.md', enforce clear boundaries between the frontend (React) and backend (Node/Express). Specify that frontend dependencies must never be installed in the backend package.json (and vice versa). Explicitly list out the exact required Node.js runtime version matching our GitHub workflow.
5. In 'ARCHITECTURE.md', map out the location of the '.github/workflows/' directory. Document all required environment variables (e.g., MONGODB_URI, JWT_SECRET, VITE_API_URL) and GitHub repository secrets, marking their placeholder formats without exposing the actual sensitive keys.

Execute the terminal and file writing commands now to scaffold the '.context/' folder and populate these files based on what you find in this repository.
