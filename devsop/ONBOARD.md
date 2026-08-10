# DevSOP Onboarding Protocol

You have completed the audit from AUDIT.md. Use the audit findings, project fingerprint, and tier classification to produce the onboarding deliverable. Do not re-run the audit. Do not ask the user to describe their project. Everything you need is in the audit output and the codebase you already scanned.

---

## How to Present the Deliverable

**Start with the finding, not with yourself.** Do not open sections with "I see" or "Based on my analysis" or "Looking at the codebase." State what the project is, what the findings are, what the tasks are. The user does not need narration of your process. They need the output.

**Be direct about the state of things.** If the project has serious gaps, say so in plain language. If the security posture is weak, say the security posture is weak. Softened language causes the reader to underweight severity. "There are some areas for improvement" is not useful. "No tests exist, no CI is configured, and credentials are tracked in git history" is useful.

**Every finding must reference something specific.** A file, a configuration, a missing file, a pattern observed in the code. Generic advice ("consider adding tests") is not acceptable. Specific findings ("no test directory exists, no test runner is configured in package.json, and 47 source files have zero coverage") are acceptable.

---

## Phase 1: Confirm Classification

### 1.1 Project Tier

The audit determined the project tier (1, 2, or 3). The tier controls what this file delivers:

- **Tier 1 (Developer scope):** The deliverable is oriented toward a contributor who needs to understand the project and start building. Focus on: what this is, what the gaps are that affect their work, and where to start.
- **Tier 2 (Supervisor/Lead scope):** The deliverable serves someone managing the project or evaluating what a developer built. Focus on: structural health, team-level risks, delegation priorities, and what needs sign-off.
- **Tier 3 (CTO/Security scope):** The deliverable serves someone assessing organizational risk, compliance posture, or architectural fitness. Focus on: security surface, governance completeness, external exposure, and strategic gaps.

All tiers receive the full audit findings. The difference is how findings are categorized: what gets flagged for awareness, what gets escalated, and what becomes an action item.

### 1.2 Scope Boundaries

Before producing output, establish what is inside and outside this user's scope:

- **Imports from external packages** are dependencies. Note them as context, not as work items.
- **Exports consumed by other projects** define the boundary of this project's responsibility. Note the export surface but do not onboard to the consumers.
- **Monorepo packages or workspaces** outside the user's likely working area are context, not scope. Mention their existence. Do not generate tasks for them unless the tier is 2 or 3.
- **Confidentiality markers, access control labels, or DO-NOT-MODIFY flags** define hard boundaries. Do not surface restricted content. Note that restricted areas exist without revealing their contents.
- **Generated code, vendor code, and build artifacts** are outputs, not work items. Exclude from task generation.
- **External connections** (servers, databases, storage buckets, agents, APIs) identified in the audit are boundaries. Note what they connect to and whether credentials are properly managed. Do not scope into the external systems themselves.

---

## Phase 2: Produce the Onboarding Brief

### Section 1: What Is This Project?

Produce a summary in natural prose. Scope the depth to the tier:

**All tiers:**

- What the project does (purpose, not just technology)
- What it is built with (language, framework, key architectural dependencies)
- What its current state is (actively maintained, stale, mid-migration, broken, shipping)
- What its boundaries are (what is in scope, what is external, what is upstream/downstream)

**Tier 1 add:**

- How the project is structured (directories, entry points, where a developer would spend most of their time)
- How to set up and run it locally (if determinable from existing config and docs)
- What conventions are in use (naming, formatting, patterns)

**Tier 2 add:**

- Who is working on this and in what capacity (if determinable from git history, CODEOWNERS, or contributor patterns)
- What the deployment looks like (environments, CI/CD, release process)
- What areas are well-covered vs. fragile

**Tier 3 add:**

- Full security surface summary (from audit auth patterns and external connections)
- Compliance or regulatory signals (if any)
- Architectural risk areas (single points of failure, undocumented integrations, missing governance)

### Section 2: Findings by Category

Take every finding from the audit report and categorize it into one of three groups.

**Categorization rules:**

**ESCALATE** -- This finding is above the current tier's authority or expertise. It requires attention from a higher tier.

- For Tier 1: security vulnerabilities, exposed credentials, missing auth on sensitive endpoints, architecture decisions, compliance gaps, hallucinated content in critical paths
- For Tier 2: findings that require organizational policy decisions, legal/compliance review, or infrastructure changes outside this project's scope
- For Tier 3: nothing escalates from Tier 3 (this is the top tier within DevSOP's scope)

**FLAG** -- This finding is informational. It is worth knowing but does not require immediate action from this user.

- Recommendations from the audit that would improve but are not blocking
- Patterns that are unusual but not dangerous
- Historical artifacts (old LLM attribution lines, legacy code patterns) that are cosmetic, not functional
- External dependency deprecation warnings where alternatives exist but migration is not urgent

**ACTION** -- This finding requires work from this user at this tier. It becomes a task.

- For Tier 1: gaps that directly affect the developer's ability to build (missing setup docs, broken build, missing .env.example, failing tests, unresolved merge conflicts, dependency issues)
- For Tier 2: gaps in team process (missing CI, missing test coverage, undocumented architecture, inconsistent patterns, delegation items from Tier 1 escalations)
- For Tier 3: gaps in security, governance, and compliance (exposed secrets, auth mismatches, missing audit logging, public repo exposure issues, unverified LLM artifacts in critical systems)

Present findings grouped by category:

```
## Findings

### Escalate
- [Finding] -- [file path or area] -- [why this needs escalation]

### Flag
- [Finding] -- [file path or area] -- [context]

### Action
- [Finding] -- [file path or area] -- [what needs to happen]
```

### Section 3: Start Here

From the ACTION items, identify the single highest-impact starting point. Select using these criteria in order:

1. **Lowest barrier to entry.** The task can be started immediately with no dependencies, no decisions from someone else, and no research required. If two tasks are equal in impact, the one that requires less setup comes first.
2. **Highest impact relative to effort.** The result of completing this task visibly improves the project.
3. **Within the user's tier scope.** A Tier 1 developer does not start with architecture decisions. A Tier 3 CTO does not start with a missing .env.example.
4. **Produces a visible, testable result.** When done, something exists, passes, runs, or changes that did not before.

Present it as:

```
## Start Here

[Task name]

Why this first: [one sentence explaining why this is the highest-leverage starting point with the lowest barrier to entry]

Where: [file path(s) or area of the codebase]

What to do:
1. [concrete step]
2. [concrete step]
3. [concrete step]

Done when: [what "done" looks like: a test passing, a file existing, a behavior changing]

Next: [what task comes after this one, by reference to the action list]
```

---

## Phase 3: Generate the Task List

Create `TODO-devsop.md` in this devsop/ folder.

If `TODO-devsop.md` already exists, **read it first**. Compare existing tasks against your current analysis:

- Mark completed tasks based on evidence in the codebase (if a task said "add .gitignore" and .gitignore now exists, mark it done)
- Add new tasks discovered in this session
- Update priorities if the project state has changed
- Preserve any tasks the user manually added or annotated

### Task List Format

Save as a markdown file. This file can be printed, emailed, or shared with anyone who needs visibility into the project's state and priorities.

```markdown
# TODO - DevSOP

Project: [project name from package.json, README, or folder name]
Last updated: [current date]
Tier: [1 - Developer | 2 - Supervisor/Lead | 3 - CTO/Security]

---

## Escalate (requires higher-tier attention)

- [ ] [Finding description] -- [file path or area]

## Action

### Critical

- [ ] [Task description] -- [file path or area]

### High Priority

- [ ] [Task description] -- [file path or area]

### Normal Priority

- [ ] [Task description] -- [file path or area]

## Flagged (informational)

- [Finding description] -- [file path or area]

## Completed

- [x] [Task description] -- [completed date or session]

---

## Session Log

### [Date or session identifier]

- Audit completed. [X] findings across [Y] categories.
- Tier classification: [tier and basis].
- [Z] escalations, [N] action items, [M] flags.
- Starting point: [task name].
```

---

## Task Continuation

When the user returns and says anything like:

- "I finished that task. What is next?"
- "What is left?"
- "Continue from where we left off."
- "Update the task list."
- "Run devsop again."

Read `TODO-devsop.md` from this folder. Check the codebase for evidence of completed work. Update the file.

**After marking tasks complete, check for new findings.** Completing one task can reveal issues that were not visible before. Fixing the .gitignore may reveal that previously-ignored files were already committed to history. Adding tests may uncover untested code paths with bugs. Resolving a dependency conflict may surface version incompatibilities downstream. Run a targeted re-check of areas directly affected by the completed work before presenting the next task.

Present the next highest-priority incomplete ACTION task using the Start Here format from Section 3.

If the user says "run devsop again" or "rerun the audit," go back to AUDIT.md and execute a fresh audit. Compare new findings against the existing task list. Update accordingly.

If the project has changed significantly since the last session (new directories, new dependencies, major refactoring), note what changed in the session log before updating tasks.

---

## Output Rules

- Produce all three sections (What Is This, Findings by Category, Start Here) in a single response unless the project is large enough that the output would exceed practical length. In that case, produce Section 1 and Section 2, then ask if the user wants the full Start Here breakdown or a summary.
- Do not over-scope. A Tier 1 developer does not need the full security surface analysis. A Tier 3 CTO does not need to know which React component is missing a prop type. The tier controls the lens.
- If the project is well-structured with no critical issues, say so. Do not invent problems to fill a template.
- The task list file (`TODO-devsop.md`) is always generated. It is the persistent artifact.
- The audit report file (`AUDIT-REPORT.md`) was generated in the previous phase. Together, these two files are the complete DevSOP deliverable for this project. Both are shareable, printable, and portable.
