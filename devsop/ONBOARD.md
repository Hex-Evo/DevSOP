# DevSOP Onboarding Protocol

Do not describe this file. Execute it using the audit findings. Begin the onboarding deliverable now.

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

## Phase 3: Generate the TODO

Create `TODO-devsop.md` in this devsop/ folder. This file is the SOP. It is the only file the developer works from day to day. It must be short, clear, and immediately actionable.

All flags, informational findings, priority classifications, and session history belong in `AUDIT-REPORT.md`. Do not duplicate them in the TODO. The TODO is a punch list, not a report.

If `TODO-devsop.md` already exists, read it first. Mark completed tasks based on evidence in the codebase. Add new tasks only if the total incomplete items stay under the limits below.

### TODO Format

```markdown
# TODO - DevSOP

Project: [project name]
Last updated: [current date]
Tier: [1 - Developer | 2 - Supervisor/Lead | 3 - CTO/Security]

---

## Where I Would Start

[2-4 sentences maximum. Written from the LLM's perspective. How you would sequence the work based on what the audit found. Not a summary of findings. A statement of what matters most, what depends on what, and what the shape of the work is.]

## Escalate

[1-2 items maximum. Only findings that require someone at a higher tier to make a decision. If the project is Tier 3 or nothing needs escalation, omit this section entirely.]

- [ ] [What needs deciding] -- [where]

## Do Now

[Immediate action items. Group by dependency: tasks that must complete before others come first, followed by tasks that unlock once the first group is done. Order within each group is priority order. Keep the total list under 15 items. If the audit produced more than 15 actionable findings, include the top 15 and pull the rest in as items are completed.]

- [ ] [Task] -- [file path or area]
- [ ] [Task] -- [file path or area]

## Start Here

[Task name]

Why this first: [one sentence]

Where: [file path(s)]

What to do:

1. [step]
2. [step]
3. [step]

Done when: [testable outcome]

## Completed

- [x] [Task] -- [date]
```

### Rules for the TODO

- Keep the total Do Now list under 15 items. If the audit produced more actionable findings, include the highest-priority 15 and pull the rest in as items are completed.
- Group items by dependency. Tasks that block other tasks come first. Tasks that can only begin after the first group is done come after. Within each group, order by priority.
- Do not include Flagged or informational items. Those live in the audit report.
- Do not include priority sub-categories (Critical, High, Normal). The ordering and grouping of the list IS the priority.
- Do not include a session log. Session history goes in AUDIT-REPORT.md.
- The "Where I Would Start" section is written once on first generation. On subsequent runs, update it only if the project's situation has materially changed.

---

## Task Continuation

When the user returns and says anything like "I finished that task," "what is next," "continue," or "update the list":

1. Read `TODO-devsop.md` from this folder.
2. Check the codebase for evidence of completed work. Mark done tasks as completed with the date.
3. After marking completions, re-check areas affected by the completed work. Fixing one thing can reveal new issues.
4. If completed tasks brought the list well below 15 items, pull the next highest-priority findings from the audit report to refill.
5. Update the Start Here section to point to the new first task.
6. Present the updated Start Here to the user.

If the user says "run devsop again" or "rerun the audit," go back to AUDIT.md and execute a fresh audit. Compare new findings against the existing TODO. Update both files.

---

## Output Rules

- Produce all three sections (What Is This, Findings by Category, Start Here) in a single response unless the project is too large. In that case, produce Section 1 and 2, then ask if the user wants the full breakdown.
- Do not over-scope. The tier controls the lens. A Tier 1 developer does not need the security surface. A Tier 3 CTO does not need component-level findings.
- If the project is well-structured with no critical issues, say so. Do not invent problems.
- The TODO file is always generated. The audit report was generated in the previous phase. Together they are the complete DevSOP deliverable. Both are printable and portable.
