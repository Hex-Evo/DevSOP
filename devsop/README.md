# DevSOP

**Drop a folder into your project. Point your AI tool at it. Get a full audit, onboarding brief, and task list -- without explaining anything about your code.**

DevSOP is an agent-agnostic governance framework that reads any codebase, identifies structural and security gaps, and generates a role-scoped SOP and prioritized task list using whatever AI tool you already use.

No plugins. No API keys. No setup. Copy the folder, prompt once, and work from the output.

---

## The Problem

Every project needs the same things answered before real work starts:

- What does this project actually do?
- Is anything broken, missing, or exposed?
- What should I work on first?
- What is the full task list, in priority order?

Right now, answering those questions requires either reading the entire codebase yourself, or hoping someone wrote documentation that is still accurate. Neither scales. Both get skipped.

DevSOP replaces that process. Your AI tool reads the project, runs a structured audit, determines what is missing, and produces the operating document your team works from going forward.

---

## How It Works

### 1. Copy the `/devsop/` folder into your project.

```
git clone https://github.com/Hex-Evo/DevSOP.git
cp -r DevSOP/devsop/ ./your-project/devsop/
```

Or download and copy the folder manually. It is three markdown files.

### 2. Tell your AI tool to read it.

> "I just added a folder called devsop. Review it and tell me what it does."

That is the only prompt. DevSOP's files instruct the AI tool on what to do from there.

### 3. Get the output.

DevSOP produces three things:

**An audit report** covering repository hygiene, security posture, documentation completeness, governance markers, dependency health, and CI/CD configuration -- all conditional on your specific tech stack and project type.

**An onboarding brief** scoped to your role:
- *What is this project?* -- purpose, architecture, state, and boundaries
- *What are the tasks?* -- prioritized from audit findings, TODOs, and codebase analysis
- *Where do you start?* -- the single highest-impact first action with file paths and steps

**A living task list** (`DEVSOP-TASKS.md`) generated in your project root. Every future session picks up where the last one left off: "I finished that task. What is next?"

---

## What Makes This Different

**DevSOP is not a coding configuration file.** AGENTS.md, CLAUDE.md, .cursorrules, and copilot-instructions.md tell your AI tool how to *write code* in your project. DevSOP tells your AI tool how to *understand, audit, and onboard someone to* your project. They solve different problems. DevSOP works alongside any of them.

**DevSOP does not generate governance.** It does not create your .gitignore, your CI pipeline, or your security policies. It identifies whether they exist, whether they should exist for your stack, and what is missing. Your team decides what to implement.

**DevSOP is agent-agnostic.** It works with Claude, ChatGPT, Gemini, Copilot, Cursor, Windsurf, Codex, Aider, Cline, or any LLM you access via API, CLI, or chat interface. If it can read a markdown file, it works.

**DevSOP is stack-agnostic.** The audit protocol detects your language, framework, and project type, then runs only the checks that apply. A Python Django project gets different checks than a React Native mobile app. A monorepo gets different scoping than a single-service API.

**DevSOP is role-aware.** A CTO gets architecture and risk. A developer gets file paths and implementation steps. A new contributor gets orientation and safe starter tasks. Same project, different output.

---

## What is in the Folder

```
devsop/
  README.md       Entry point. Explains the framework to humans.
                  Activates the AI tool's audit/onboard sequence.

  AUDIT.md        Structured protocol for project health analysis.
                  Covers: version control, dependencies, secrets,
                  access control, documentation, governance, CI/CD,
                  testing, and stack-specific checks.

  ONBOARD.md      Role-scoped onboarding and task generation.
                  Produces: project summary, prioritized task list,
                  and a starting point. Generates DEVSOP-TASKS.md
                  as a persistent working document.
```

Three files. Total size under 30KB. No dependencies. No build step.

---

## Example Use Cases

- **You just cloned a repo you have never seen before.** Drop in DevSOP, prompt once, and get the full picture before touching anything.
- **A new developer joins your team.** Instead of a two-hour walkthrough, they add DevSOP and get an onboarding brief scoped to their role in minutes.
- **You are auditing a project for security or compliance.** DevSOP's audit protocol checks for exposed secrets, missing .gitignore entries, tracked credentials, and public repo exposure -- conditional on your actual stack.
- **You are inheriting a legacy codebase.** DevSOP classifies it, identifies what is broken or missing, and builds the task list for modernization.
- **You want a persistent task tracker that updates itself.** DEVSOP-TASKS.md lives in your project root and evolves with every AI session.

---

## Requirements

A project with files in it, and an AI tool that can read those files. That is it.

---

## License

Apache 2.0. See [LICENSE](LICENSE).

---

## What This Replaces

Every organization runs on SOPs. The problem is that traditional SOPs are static documents written by humans who read the system once, then maintained by no one. They go stale the day they are written.

DevSOP is what SOPs look like when your team already uses AI tools for everything else. Instead of a human writing a static document that describes the system, DevSOP reads the system and writes the document itself -- then updates it every session.

For **developers**, it is a project SOP generated from the code itself, not from someone's memory of the code.

For **managers and leads**, it is a task catalogue with priorities derived from the actual state of the project, not from a standup conversation.

For **IT, security, and CTO-level reviewers**, it is a clinical pass across governance, secrets management, documentation gaps, and structural health -- scoped to the concerns that matter at that level.

For every role, it is the same source of truth, rendered at the appropriate depth. No one writes it. No one maintains it. It regenerates from the project's current state every time it runs.

This is the first manual step on the path toward fully agentic project governance -- where the audit, onboarding, and task management happen autonomously. DevSOP is the human-in-the-loop version: you prompt once, you get the output, you work from it. The agentic version is a different product.

---

## Other Hex-Evo Projects

- [PREA](https://github.com/Hex-Evo/PREA) -- Autonomous AI agent for real-time operations and A2A orchestration.
- [agents-prea.json](https://github.com/Hex-Evo/agents-prea.json) -- Public A2A SDK for PREA agent integration.

---

Built by [Hex-Evo](https://hex-evo.com).
