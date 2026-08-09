# DevSOP Onboarding Protocol

You are executing the onboarding phase for the project that contains this folder. You should have already completed the audit (AUDIT.md). If you have not, go back and complete it first. The audit findings feed directly into the task list you generate here.

---

## Phase 1: Project Classification

You already fingerprinted the project during the audit. Now classify it operationally. These classifications determine how you scope the onboarding, what you prioritize in the task list, and what level of detail you provide.

### 1.1 Determine the Project Archetype

Read the project structure, dependencies, entry points, and any existing documentation. Classify into exactly one archetype:

- **Greenfield scaffold:** Boilerplate or starter template with minimal custom code. The work ahead is building, not fixing.
- **Active product:** Shipping or near-shipping software with users, deployments, or release history. The work is feature development, maintenance, and hardening.
- **Internal tool:** Built for a team, not a market. Documentation expectations are lower but operational reliability matters.
- **Library or package:** Consumed by other projects. API surface, versioning, and documentation are primary concerns.
- **Prototype or proof-of-concept:** Experimental code. The work is deciding what to keep, what to rewrite, and what to throw away.
- **Legacy rescue:** Old codebase being revived, migrated, or modernized. The work is understanding before changing.
- **Infrastructure or platform:** DevOps, CI/CD, cloud config, or deployment automation. The work is reliability, security, and documentation.
- **Monorepo with mixed concerns:** Contains multiple projects, packages, or services. Must be onboarded per-package, not as a whole.

### 1.2 Determine Scope Boundaries

Not everything in the repository is this user's concern. Before generating output, identify scope limits:

- **Imports and exports:** If the project imports from external packages, those packages are dependencies, not part of the project. If the project exports modules consumed elsewhere, note the export surface but do not onboard to the consumers.
- **Monorepo boundaries:** If this is a monorepo, identify which package or workspace the user is likely working in. Onboard to that scope. Mention the others as context, not as work items.
- **Confidentiality markers:** If any files contain access control markers (role-based comments, permission tiers, classification labels, DO-NOT-MODIFY warnings), respect them. Do not surface restricted content in the onboarding output. Note that restricted areas exist without revealing their contents.
- **Generated code:** If the project contains generated files (compiled output, auto-generated types, migration snapshots, build artifacts), exclude them from the task list. They are outputs, not work items.
- **Third-party or vendor code:** If checked-in vendor code exists, it is not the user's code to modify. Exclude from tasks unless integration with it is required.

### 1.3 Infer the User's Role

You should determine the user's operational scope. Use the following signals:

- **How they prompted you.** "Run devsop" with no other context suggests a lead or owner who wants the full picture. "I just joined this project" suggests a new contributor. "What is broken?" suggests a senior troubleshooter.
- **What files they have open or recently edited.** Frontend components suggest a frontend developer. Infrastructure configs suggest a DevOps engineer. Everything suggests a lead or CTO.
- **What questions they ask.** "How does this deploy?" suggests ops. "Where are the tests?" suggests QA or a senior dev. "What does this project do?" suggests someone new at any level.

If you cannot determine role from available signals, ask one question: "What is your role on this project?" Use the answer to scope everything that follows.

Map to one of these operational scopes:

- **Executive / CTO scope:** Architecture overview, risk surface, security posture, strategic gaps. No file-level task assignments. Focus on what decisions need to be made.
- **Tech lead / senior scope:** Architecture + implementation detail. File-level awareness. Task list includes both technical debt and feature work. Focus on what to prioritize and delegate.
- **Developer scope:** Implementation detail within their domain. Task list is specific: file paths, function names, what to build next. Focus on what to do and how to start.
- **New contributor scope:** Orientation first. What the project does, how it is structured, how to set up a local environment, what conventions to follow. Task list is limited to starter tasks (good first issues, documentation improvements, test additions).
- **QA / review scope:** Test coverage, quality gaps, known issues, areas of fragility. Task list focuses on what to test, what is untested, and where bugs are likely hiding.
- **Security / compliance scope:** Full security audit findings, governance gaps, credential management, access control review, regulatory considerations. Task list focuses on hardening and compliance.

---

## Phase 2: Produce the Onboarding Brief

Generate three sections. Each section is scoped to the user's role as determined above. The depth and content of each section changes based on role.

### Section 1: What Is This Project?

Produce a summary that answers these questions in natural prose, not as a checklist:

- What does this project do? (purpose, not technology)
- Who is it for? (users, customers, internal team, other developers)
- What is it built with? (language, framework, key dependencies -- only the ones that matter architecturally)
- How is it structured? (major directories, entry points, data flow)
- What is its current state? (actively maintained, stale, mid-migration, broken, shipping)
- What are its boundaries? (what is in scope, what is external, what is upstream/downstream)

For executive scope: emphasize purpose, state, and risk. Skip implementation detail.
For developer scope: emphasize structure, entry points, and conventions. Be specific about file paths.
For new contributor scope: emphasize purpose, setup instructions, and where to find things. Assume nothing.

### Section 2: What Are the Tasks?

Compile a prioritized task list from three sources:

**Source 1: Audit findings.**
Every MISSING or NEEDS REVIEW item from the audit is a candidate task. Categorize by severity:
- Critical (security risk, data exposure, broken build) = immediate
- Structural (missing tests, no CI, undocumented architecture) = high priority
- Improvement (better README, add CODEOWNERS, improve error handling) = normal priority

**Source 2: Existing project signals.**
Scan for:
- TODO and FIXME comments in source files (extract the text, note the file and line)
- Open issues (if .github/ or issue tracker references are present)
- Incomplete implementations (stub functions, placeholder content, commented-out code blocks with notes)
- Unused imports, dead code paths, or orphaned files (signals of abandoned work)
- package.json scripts that exist but reference missing files or tools

**Source 3: Codebase analysis.**
Based on the project archetype and maturity:
- Greenfield: tasks are about establishing foundations (testing, CI, documentation, structure)
- Active product: tasks are about hardening, coverage gaps, and unfinished features
- Legacy rescue: tasks are about understanding, documenting, and stabilizing before changing
- Library: tasks are about API surface, documentation, versioning, and consumer experience

For executive scope: tasks are strategic decisions, not implementation items. "Decide on testing strategy" not "write tests for auth.js."
For developer scope: tasks are specific. File paths, function names, what the implementation should accomplish.
For new contributor scope: tasks are safe, bounded, and educational. No tasks that could break production.

### Section 3: Where Do You Start?

This is the single most actionable output. Identify ONE task from the list above that meets all of these criteria:

- Highest impact relative to effort
- Does not depend on completing another task first
- Is within the user's inferred scope
- Produces a visible, testable result when completed
- Does not require decisions from someone else (unless the user is the decision-maker)

Present it as:

```
## Start Here

[Task name]

Why this first: [one sentence explaining why this is the highest-leverage starting point]

Where: [file path(s) or area of the codebase]

What to do: [2-5 concrete steps, specific enough to act on without further research]

When you are done: [what "done" looks like -- a test passing, a file existing, a behavior changing]

Then: [what task comes next, by reference to the task list]
```

---

## Phase 3: Generate the Task List File

Create a file called `DEVSOP-TASKS.md` in the **parent project root** (one level up from this devsop/ folder). Not inside this folder.

If `DEVSOP-TASKS.md` already exists in the project root, **read it first**. Compare existing tasks against your current analysis:
- Mark completed tasks based on evidence in the codebase (if a task said "add .gitignore" and .gitignore now exists, mark it done)
- Add new tasks discovered in this session
- Update priorities if the project state has changed
- Preserve any tasks the user manually added or annotated

### Task List Format

```markdown
# DevSOP Task List

Project: [project name from package.json, README, or folder name]
Last updated: [current date]
Archetype: [detected archetype]
Scope: [user's role scope]

---

## Critical (act now)

- [ ] [Task description] -- [file path or area] -- [source: audit/TODO/analysis]
- [ ] ...

## High Priority

- [ ] [Task description] -- [file path or area] -- [source: audit/TODO/analysis]
- [ ] ...

## Normal Priority

- [ ] [Task description] -- [file path or area] -- [source: audit/TODO/analysis]
- [ ] ...

## Completed

- [x] [Task description] -- [completed date or session]
- [x] ...

---

## Session Log

### [Date or session identifier]
- Audit completed. [X] findings across [Y] categories.
- [Z] critical issues identified.
- Starting point: [task name].
```

### Continuation Protocol

When the user returns in a future session and says anything like:
- "I finished that task. What is next?"
- "What is left?"
- "Continue from where we left off."
- "Update the task list."

Read `DEVSOP-TASKS.md` from the project root. Check the codebase for evidence of completed work. Update the file. Present the next highest-priority incomplete task using the Start Here format from Section 3.

If the project has changed significantly since the last session (new directories, new dependencies, major refactoring), re-run the relevant audit checks from AUDIT.md before updating tasks. Note what changed in the session log.

---

## Output Rules

- Produce all three sections (What Is This, What Are The Tasks, Where Do You Start) in a single response unless the project is large enough that the output would exceed practical length. In that case, produce Section 1, then ask if the user wants the full task list or a summary.
- The task list file (`DEVSOP-TASKS.md`) is always generated. It is the persistent artifact of the onboarding.
- Do not produce generic advice. Every finding, task, and recommendation must reference something specific in this project: a file, a configuration, a missing file, a pattern you observed.
- Do not over-scope. A frontend developer does not need to know about the Kubernetes manifests unless they are broken. A CTO does not need to know which React component is missing a prop type.
- If the project is genuinely well-structured with no critical issues, say so. Do not invent problems to fill a template.
