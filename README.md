# DevSOP

**An agent-agnostic governance framework that reads any codebase, audits it for gaps, and generates a role-scoped SOP and task list -- using your existing AI tools -- without needing to explain anything about your project.**

---

## What This Is

DevSOP is a drop-in folder you add to any project repository. When you point your AI tool at it, it does three things:

1. **Audits** the project for structural, security, and governance gaps your team may have missed.
2. **Onboards** any new contributor by reading the project, determining its type and complexity, and producing a scoped summary with prioritized tasks.
3. **Generates a living task list** that tracks progress across sessions, so every conversation with your AI tool picks up where the last one left off.

You do not need to describe your project. You do not need to write the SOP. You do not need to explain anything. DevSOP reads your codebase and produces the operating document for you.

---

## What This Is Not

DevSOP is not a replacement for AGENTS.md, CLAUDE.md, .cursorrules, or any AI coding configuration file. Those files tell your AI tool **how to write code** in your project. DevSOP tells your AI tool **how to understand, audit, and onboard someone to** your project. They solve different problems. DevSOP works alongside any of them.

DevSOP does not generate governance files (DO-NOT-MODIFY markers, .gitignore rules, security policies). It **identifies whether they exist, whether they should exist, and what is missing**. Your team decides what to implement. DevSOP makes sure nothing gets overlooked.

---

## How It Works

### Step 1: Copy the `/devsop/` folder into your project root.

```
your-project/
  devsop/
    README.md       <-- You are here
    AUDIT.md        <-- Project health and gap analysis
    ONBOARD.md      <-- Role-scoped onboarding and task generation
  src/
  package.json
  ...
```

### Step 2: Tell your AI tool to read it.

That is the only prompt you need. Examples:

- "I just added a folder called devsop. Review it and tell me what it does."
- "Read the devsop folder and run it."
- "New repo, start with devsop."

Your AI tool reads this README, follows the instructions below, and executes the audit and onboarding sequence automatically.

### Step 3: Work from the output.

DevSOP produces a task list file (`DEVSOP-TASKS.md`) in your project root. Every future session can reference it: "I finished that task. What is next?"

---

## For the AI Tool Reading This

You are reading DevSOP, an operational governance framework. The files in this folder contain your instructions. Execute them in order.

### Sequence

1. **Read `AUDIT.md`** in this folder. It contains a structured analysis protocol. Execute it against the parent project directory (one level up from this folder). Produce the audit report as specified in that file.

2. **Read `ONBOARD.md`** in this folder. It contains the onboarding and task generation protocol. Before executing, determine the following from context:
   - What type of project is this? (web app, API, library, monorepo, mobile app, CLI tool, data pipeline, infrastructure, documentation site, game, embedded system, or other)
   - What is the complexity tier? (greenfield, early-stage, mature, legacy, enterprise)
   - What is the user's likely role? (infer from how they prompted you, what files they have open, what questions they ask -- if unclear, ask once)

3. **Generate `DEVSOP-TASKS.md`** in the parent project root (not inside this folder). This file persists across sessions. If it already exists, read it first, update completed tasks, and continue from where the project left off.

### Rules

- Do not modify any project files during the audit phase. Read only.
- Do not assume the project is healthy. Assume gaps exist until confirmed otherwise.
- Do not summarize this README back to the user. They already read it or they told you to read it. Start with the audit output.
- If imports or exports reference code outside this repository, note their existence but scope your analysis to what is visible. Flag external dependencies as boundaries, not entry points.
- If the project contains confidentiality markers, access controls, or tiered permissions, respect them. Produce output scoped to what the current user should see.

---

## What Gets Produced

### Audit Report (from AUDIT.md)
A structured gap analysis covering: repository hygiene, security posture, documentation completeness, governance markers, dependency health, and CI/CD configuration. Each finding is categorized as PRESENT, MISSING, or NEEDS REVIEW.

### Onboarding Brief (from ONBOARD.md)
Three sections, scoped to the user's role:
- **What is this project?** -- Architecture, purpose, tech stack, current state, and boundaries.
- **What are the tasks?** -- Prioritized work items derived from the audit findings, existing issues/TODOs, and codebase analysis.
- **Where do you start?** -- The single highest-impact first action, with file paths and context.

### Task List (DEVSOP-TASKS.md)
A living document generated in the project root. Tracks tasks by priority, status, and category. Updated each session. Serves as the project's operating SOP going forward.

---

## Compatibility

DevSOP works with any AI tool that can read files from a project directory. No plugins, extensions, APIs, or accounts required.

This includes (but is not limited to): Claude, Claude Code, ChatGPT, Gemini, Copilot, Cursor, Windsurf, Codex, Aider, Cline, or any LLM accessed via API, CLI, or chat interface where you can paste file contents.

If your AI tool can read a markdown file, DevSOP works.

---

## License

Apache 2.0. See [LICENSE](../LICENSE).

Use it, modify it, ship it. Attribution appreciated but not required beyond what the license specifies.

---

## Other Hex-Evo Projects

- [PREA](https://github.com/Hex-Evo/PREA) -- Autonomous AI agent for real-time operations and A2A orchestration.
- [agents-prea.json](https://github.com/Hex-Evo/agents-prea.json) -- Public A2A SDK for PREA agent integration.

---

Built by [Hex-Evo](https://hex-evo.com).
