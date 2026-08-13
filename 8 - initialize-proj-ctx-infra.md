# Initialize Project Context Infrastructure — Master Prompt

> **Copy everything below this line and paste it as your first prompt in any new project.**

---

Initialize Project Context Infrastructure

CONTEXT:
We are configuring a local, filesystem-driven context management system for this repository. This infrastructure must be compatible with terminal agents (like Claude Code), AI-native IDEs (like Antigravity / Gemini), Cursor, and custom MCP pipelines. It relies on explicit markdown files to pass rules and state seamlessly between different AI models.

TASK:
Analyze the current repository structure, primary programming languages, configuration files, and frameworks. Then, autonomously create the following:

## Part 1 — Context Files

Create this directory layout and populate the markdown files at the root of this project:

```
.context/
├── CORE_RULES.md
├── ARCHITECTURE.md
└── PROGRESS.md
```

## Part 2 — AI Tool Auto-Discovery Wiring

After creating the `.context/` files, wire them into every major AI tool's auto-discovery system so they are loaded **automatically on every new conversation** — no manual reminder needed:

1. **`AGENTS.md`** (repo root) — Auto-loaded by Antigravity / Gemini IDE. Must instruct the agent to:
   - Read `.context/PROGRESS.md` at the START of every conversation.
   - Read `.context/CORE_RULES.md` before any coding task.
   - Consult `.context/ARCHITECTURE.md` before asking "where does X live?"
   - Update `.context/PROGRESS.md` at the END of every conversation (move completed items, add new items, log gotchas, update timestamp).
   - Include a "Key Constraints (Summary)" section with the 5-7 most critical rules from CORE_RULES.md.

2. **`CLAUDE.md`** (repo root) — Auto-loaded by Claude Code terminal agent. Same instructions as AGENTS.md but more concise (Claude Code has tighter context windows).

3. **`.cursorrules`** — If it already exists, append a new numbered section pointing to `.context/` files. If it doesn't exist, create it with the project context reference. Must instruct Cursor to read `.context/CORE_RULES.md`, `.context/ARCHITECTURE.md`, and `.context/PROGRESS.md` before any task, and update `PROGRESS.md` after completing work.

## Compliance Criteria for `.context/` Files

### CORE_RULES.md
1. Include a **Tech Stack Definition** table listing every technology, framework, and their exact version constraints found in the project's package.json files and CI workflows.
2. Include a **Monorepo Boundary Enforcement** section if the project has multiple packages. Specify that dependencies must never be cross-installed between packages.
3. Include a **Banned Anti-Patterns** section specifying common model hallucinations or deprecated methods for the exact tech stack. Cover at least 8 backend and 6 frontend anti-patterns.
4. Include a **Fail-Fast Protocol** section with the exact instruction: "If a file path, dependency, database column, or API endpoint is missing — STOP and ask the user. Do NOT guess, hallucinate, or invent."
5. Include a **Code Style Constraints** section covering architecture patterns (e.g., service-layered), logging rules, error handling conventions, and API response format.
6. Enforce the exact Node.js runtime version matching the GitHub workflow files.

### ARCHITECTURE.md
1. Map out every major directory path with a concise 1-2 line summary of its engineering purpose, so an external model doesn't have to recursively list paths.
2. Include a complete **API Route Map** table derived from the actual route registration file (e.g., `app.js`), showing mount path, route file, and domain description.
3. Map out the `.github/workflows/` directory. Document each workflow's trigger, purpose, and Node version.
4. Document ALL environment variables from `.env` files with their placeholder formats (e.g., `CHANGE_ME_*`), marking which are required vs optional. **Never expose actual secret values.**
5. Document all GitHub repository secrets used in CI workflows.
6. If the project has a plugin/shortcode/component system, include a reference table mapping each identifier to its component file.

### PROGRESS.md
1. Establish a clean, standardized Markdown state template with these exact section headers:
   - `[Completed]` — items finished in previous sessions
   - `[In Progress]` — items currently being worked on
   - `[Next Steps]` — planned items not yet started
   - `[Discovered Gotchas]` — known pitfalls, bugs, or gotchas with workarounds
2. Pre-populate `[Completed]` with major features already built (infer from the codebase).
3. Include a format guide showing the checkbox notation: `[ ]` not started, `[/]` in progress, `[x]` completed.
4. Include a footer with `*Last updated: YYYY-MM-DD by [agent name]*`.

## Execution Order
1. Analyze the repository thoroughly (read package.json files, config, workflows, route files, directory structure).
2. Create `.context/CORE_RULES.md`
3. Create `.context/ARCHITECTURE.md`
4. Create `.context/PROGRESS.md`
5. Create `AGENTS.md` (repo root)
6. Create `CLAUDE.md` (repo root)
7. Create or update `.cursorrules` (repo root)
8. Verify all files exist with a directory listing.

Execute now. Do not ask for confirmation — scaffold everything based on what you find in this repository.
