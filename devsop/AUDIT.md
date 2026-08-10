# DevSOP Audit Protocol

Do not describe this file. Execute it. Begin the audit now.

Execute a read-only audit of the project that contains this folder. Do not modify, create, or delete any project files during this phase. Scan the project root and up to three levels of subdirectories. Produce a structured report and save it as a file. The findings from this audit feed directly into ONBOARD.md. Read that file next.

**Skip these directories entirely.** Do not list, scan, or read files inside `node_modules/`, `vendor/`, `.venv/`, `venv/`, `dist/`, `build/`, `.next/`, `__pycache__/`, `target/`, or any other dependency, build output, or cache directory. Assess dependency health from the manifest (package.json, requirements.txt, Cargo.toml, etc.) and the lockfile. Do not scan vendored package contents.

---

## How to Think During This Audit

These instructions govern how you approach the analysis, not just what you check.

**Start with the finding, not with yourself.** Do not open any finding with "I see" or "I notice" or "Looking at your project." State the finding directly. Not "I notice your .gitignore is missing node_modules." Instead: ".gitignore is missing node_modules. This exposes dependency files in version control."

**Evaluate what is missing, not just what is present.** A project with no tests, no CI, and no .env.example has three individual gaps. Together they indicate no quality infrastructure exists. Name the pattern, not just the items. Absence is often more significant than presence.

**Evaluate findings against each other.** A single gap can be minor in isolation and dangerous in combination. .env exists + public repo + .gitignore missing the .env entry = compound exposure. JWT implementation + no token expiry + admin routes unprotected = compound auth failure. After completing all checks, review the full findings set for compound risks that no individual check would catch.

**Do not soften findings.** If something is broken, state that it is broken. If something is missing and should exist, state that it is missing and should exist. Vague language ("there are some areas for improvement") causes the reader to underweight the severity. Clarity protects the user. Vagueness does not.

**Decompose ambiguity into specific checks.** When a classification is unclear (project maturity is borderline, tier is uncertain, a finding could go either way), do not guess. Break the ambiguity into specific observable facts. If project maturity is unclear from commit count alone, check: are there tagged releases? Is there a changelog? Are there multiple branches? Is there a CI pipeline? Each answer is objective. The combination determines the classification.

---

## Phase 1: Project Fingerprinting

Determine what you are looking at before auditing anything.

### 1.1 Stack Detection

- **Primary language(s):** Check file extensions and package manifests (package.json, requirements.txt, Cargo.toml, go.mod, Gemfile, pom.xml, build.gradle, mix.exs, composer.json, pubspec.yaml, \*.csproj, CMakeLists.txt).
- **Framework(s):** Check for framework-specific config files (next.config._, nuxt.config._, angular.json, svelte.config.\*, django settings, Rails Gemfile, Spring Boot application.properties, FastAPI/Flask imports, Laravel artisan).
- **Project type:** Classify as exactly one: web app, API, library/package, CLI tool, mobile app (React Native, Flutter, Swift, Kotlin), monorepo, data pipeline, infrastructure-as-code, documentation site, game engine project, embedded/firmware, desktop app, browser extension, or other.

### 1.2 Maturity Signal

Count total commits if git history is accessible. Under 20 = greenfield. 20-200 = early-stage. 200-2000 = mature. Over 2000 = legacy or enterprise. If git is not accessible, estimate from file count and directory depth.

If commit count alone is ambiguous, decompose further: are there tagged releases? Is there a changelog? Are there multiple long-lived branches? Is there a CI pipeline? Does the project have a docs/ folder? Each answer sharpens the classification.

### 1.3 Exposure Level

Determine if this is a public or private repository. Check for: LICENSE file at root, .git/config remote URLs containing github.com or gitlab.com with public visibility indicators, GitHub-specific files (.github/), published package references. Public and private repos have different risk surfaces.

### 1.4 Project Tier Classification

Determine the operational tier of this project by weighing the following signals together. No single signal is definitive. Use the combination to classify.

**Tier 1 -- Developer scope (single contributor, bounded work):**

- Simple auth or no auth (single API key, basic session, no RBAC)
- No external service integrations beyond a database and maybe one third-party API
- No CI/CD or only basic CI (lint + test)
- Single deployment target or no deployment config
- Flat directory structure, single-service architecture
- Under 50 source files

**Tier 2 -- Supervisor/Lead scope (team-level, multi-concern):**

- Role-based access control, multi-user auth, OAuth integrations
- Multiple external service connections (payment processors, email services, analytics, CDNs, storage buckets)
- CI/CD with staging and production separation
- Multiple deployment targets or containerized infrastructure
- Modular architecture with clear service boundaries
- 50-500 source files
- Evidence of multiple contributors in git history

**Tier 3 -- CTO/Security scope (organizational, high-exposure):**

- Enterprise auth patterns (SSO, SAML, LDAP, mTLS, certificate-based auth)
- Connections to regulated systems (financial APIs, healthcare data, government systems)
- Automated agents, background workers, queue processors, scheduled jobs
- Infrastructure-as-code (Terraform, CloudFormation, Pulumi)
- Secrets management services (Vault, AWS Secrets Manager, Azure Key Vault)
- Audit logging, compliance markers, data classification labels
- Monorepo with multiple services or microservice architecture
- 500+ source files
- CISO, security, or compliance-specific documentation or configs

**Signals that adjust tier upward regardless of project size:**

- Encrypted credentials management (not just .env files)
- Connection strings to external servers, R2/S3 buckets, message queues, or agent registries
- Webhook endpoints or callback URLs (the project is part of a larger automated system)
- Import/export patterns that reference other internal repositories or private packages
- Git submodules pointing to private repos
- Any file or directory specifically marked with access restrictions or classification labels

Do not ask follow-up questions to determine tier. Classify from what is observable in the project. If signals conflict (simple codebase but enterprise auth patterns), weight the security signals higher. A small project with enterprise auth is operating at Tier 2 or 3, not Tier 1.

Store the tier classification. It determines what ONBOARD.md delivers.

---

## Phase 2: Repository Hygiene

### 2.1 Version Control

- [ ] `.gitignore` exists.
- [ ] `.gitignore` covers the standard exclusions for the detected language/framework:
  - Node.js: node_modules/, .env, dist/, .next/, coverage/
  - Python: **pycache**/, _.pyc, .env, venv/, .venv/, dist/, _.egg-info/
  - Ruby: vendor/bundle, .env, \*.gem, .bundle/
  - Go: vendor/ (if not using modules), _.exe, _.test
  - Rust: target/, Cargo.lock (if library)
  - Java/Kotlin: build/, .gradle/, _.class, _.jar
  - .NET: bin/, obj/, _.user, _.suo
  - Flutter/Dart: .dart_tool/, build/, .flutter-plugins
  - General: .DS*Store, Thumbs.db, *.log, .idea/, .vscode/ (check if settings are project-specific or personal), \_.swp, \*.swo
- [ ] If Docker is used: .dockerignore exists and excludes .git, node_modules, .env, and test fixtures.
- [ ] No merge conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) in any tracked file.
- [ ] If both package-lock.json and yarn.lock exist: flag as lockfile conflict. Same for pnpm-lock.yaml coexisting with either.

### 2.2 Dependency Health

- [ ] A lockfile exists and matches the package manager in use.
- [ ] If package.json exists: check for `*` or `latest` version ranges (flag as unpinned).
- [ ] If requirements.txt exists: check for unpinned dependencies (no `==` version).
- [ ] Check for known deprecated or abandoned packages if identifiable from naming conventions or version age.
- [ ] If a monorepo: check that workspaces/packages share compatible dependency versions and do not have conflicting peer dependencies.

### 2.3 Dependency Security

- [ ] Check installed/locked dependency versions against known critical or high-severity vulnerabilities. For Node.js projects, reference npm audit patterns. For Python, reference pip audit or safety check patterns. For other ecosystems, check for equivalent signals.
- [ ] Flag any dependency that has not been updated in over 2 years and has known alternatives.
- [ ] Flag import statements that reference packages not present in the dependency manifest (phantom imports may indicate removed dependencies or hallucinated packages).
- [ ] Flag version mismatches between what is specified in the manifest and what is locked in the lockfile.
- [ ] If the project uses multiple sub-packages or workspaces: flag conflicting versions of the same dependency across packages.

---

## Phase 3: Security Posture

### 3.1 Secrets and Credentials

- [ ] No `.env` file is tracked by git. If `.env` exists in the working directory, confirm it is listed in `.gitignore`.
- [ ] No hardcoded API keys, tokens, passwords, or secrets in source files. Scan for patterns:
  - Strings matching `sk-`, `pk_`, `ghp_`, `AKIA`, `xox`, `Bearer `, `-----BEGIN`, `password =`, `secret =`, `apiKey =`, `token =`
  - Base64-encoded blobs longer than 40 characters assigned to variables with suggestive names
  - Connection strings with embedded credentials (postgres://, mysql://, mongodb://, redis://)
- [ ] `.env.example` or `.env.template` exists showing required environment variables WITHOUT values.
- [ ] If the project uses AWS: no credentials in `~/.aws/` referenced by relative path. No hardcoded region fallbacks masking missing config.
- [ ] If the project handles user authentication: secrets are loaded from environment, not config files.
- [ ] If the project is public: scan git history (last 50 commits if accessible) for secrets that were committed and later removed. Presence in history means they are still exposed.

**Compound check:** If .env exists AND the repo is public AND .gitignore does not exclude .env, this is not three separate findings. This is a single critical exposure. Report it as one compound finding, not three individual items.

### 3.2 Access Control and Permissions

- [ ] If the project has multiple user roles or permission tiers: are they defined somewhere? (middleware, route guards, RBAC config, database schema with role columns)
- [ ] If the project has an admin panel or dashboard: is it behind authentication?
- [ ] If the project exposes API endpoints: are they protected? Look for auth middleware applied to route groups.
- [ ] If the project uses file uploads: check for path traversal protections, file type validation, and size limits.

**Auth pattern inventory (feeds into tier classification):**
Catalog every auth mechanism found in the project. For each one, note:

- What type it is (API key, JWT, OAuth, session cookie, SSO/SAML, mTLS, certificate-based)
- Where it is implemented (middleware file, route guard, service layer)
- What it protects (all routes, specific route groups, admin-only endpoints, external integrations)
- Whether the implementation matches the complexity it guards (a single API key protecting financial transactions is a mismatch; flag it as a compound risk)

If auth patterns suggest a higher tier than the initial fingerprint indicated, adjust the tier classification upward.

### 3.3 External Connections Inventory

Scan the project for all outbound connections. These inform both security posture and tier classification.

- [ ] Database connections (connection strings, ORM configs, migration files)
- [ ] External API integrations (HTTP client calls, SDK imports, webhook configs)
- [ ] Cloud storage (S3, R2, GCS, Azure Blob references in code or config)
- [ ] Message queues and event buses (RabbitMQ, Kafka, SQS, Redis pub/sub)
- [ ] Agent registries, A2A protocols, or automated system integrations
- [ ] Scheduled jobs, cron configs, background workers
- [ ] Outbound email, SMS, or notification services
- [ ] Analytics, logging, or monitoring service integrations

For each connection found: note whether credentials are properly externalized and whether the connection is to a production, staging, or development environment.

---

## Phase 4: LLM Artifact Detection

This section applies to all projects regardless of tier or exposure level.

### 4.1 Hallucinated Content

Scan source files, documentation, and configuration for signs of LLM-generated content that was never verified:

- [ ] Attribution lines claiming AI co-authorship that were not authored by a human ("Codeveloped by Claude," "Generated by GPT," "Co-authored-by: Copilot," "Written with AI assistance," or similar)
- [ ] Import statements referencing packages that do not exist in the dependency manifest and do not exist as published packages (phantom dependencies)
- [ ] Fabricated URLs in documentation, comments, or config files (URLs that follow plausible patterns but reference non-existent endpoints, docs pages, or repositories)
- [ ] Fabricated contributor names, email addresses, or license references that do not correspond to actual project history or real entities
- [ ] Comments explaining code that does not match what the code actually does (a sign the comment was generated separately from the implementation)
- [ ] Placeholder content that appears functional but is not (mock data presented as real config, stub functions that look complete but do nothing, test files with no actual assertions)

**For any hallucinated content found:** Flag it with the file path and line reference. Note which git commits introduced it if history is accessible. Do not action it in this phase. Add it to findings for ONBOARD.md to categorize as Escalate, Flag, or Action depending on tier.

### 4.2 Unverified LLM Patterns

- [ ] Large blocks of code with no commit message context or with generic messages ("update," "fix," "changes," "initial commit" covering hundreds of lines)
- [ ] Inconsistent coding patterns within the same file suggesting multiple generation sessions without review (different naming conventions, mixed formatting, contradictory approaches to the same problem)
- [ ] README or documentation content that describes features the codebase does not implement
- [ ] Configuration files with values that appear auto-generated but serve no clear purpose

---

## Phase 5: Governance Markers

### 5.1 Ownership and Boundaries

- [ ] LICENSE file exists at the project root.
- [ ] If the project has vendor code, generated code, or third-party code checked in: is it clearly separated and marked? (vendor/, generated/, third-party/, or file-level comments)
- [ ] If any files should not be modified by contributors: are they marked? (DO-NOT-MODIFY comments, .DO-NOT-MODIFY dotfiles, CODEOWNERS restrictions, or README warnings)
- [ ] If the project has generated files (compiled output, auto-generated types, lock files for generated schemas): are they distinguishable from hand-written source?

### 5.2 CI/CD and Automation

- [ ] CI configuration exists (GitHub Actions, GitLab CI, CircleCI, Jenkins, Travis, Bitbucket Pipelines, or equivalent).
- [ ] If CI exists: does it run tests?
- [ ] If CI exists: does it run linting or formatting checks?
- [ ] If CI exists: does it build the project?
- [ ] If the project deploys: is the deployment process documented or codified? (Dockerfile, docker-compose, Procfile, serverless.yml, terraform/, k8s manifests, deploy scripts)
- [ ] If the project has deployment: are staging and production environments distinguishable in config?

### 5.3 Testing

- [ ] Tests exist (any test runner, any test directory convention).
- [ ] If tests exist: do they run? (Check for test scripts in package.json, Makefile, tox.ini, or equivalent)
- [ ] If tests exist: what is the approximate coverage signal? (Count test files relative to source files as a rough heuristic)
- [ ] If no tests exist and the project has more than 20 source files: flag as MISSING.

---

## Phase 6: Documentation Assessment

This phase runs at different depths depending on the project tier determined in Phase 1.

### 6.1 Base Documentation (all tiers)

- [ ] README.md exists and contains at minimum: what the project does, how to set it up, and how to run it.
- [ ] If the project requires environment variables: they are documented somewhere (README, .env.example, or a dedicated config doc).

### 6.2 Extended Documentation (Tier 2 and Tier 3 only)

- [ ] If the project has more than 10 source files: architecture documentation exists (docs/ folder, README sections, ADRs, diagrams).
- [ ] If the project is a monorepo: each package/workspace has its own README or description.
- [ ] If the project has database schemas: migrations are documented or self-describing.
- [ ] If the project integrates with external services: integrations are documented (which services, what they do, what credentials are needed).
- [ ] If the project is a library or package: API documentation or usage examples exist.
- [ ] If the project has contributors beyond the author: CONTRIBUTING.md exists.
- [ ] CHANGELOG.md or release history exists (if tagged releases are present).

---

## Phase 7: Contextual Checks

Run only the checks that match the detected stack. Skip everything that does not apply.

### 7.1 Node.js / JavaScript / TypeScript

- [ ] `engines` field in package.json specifies Node version.
- [ ] If TypeScript: tsconfig.json exists with `strict` enabled or an explicit relaxation documented.
- [ ] If Next.js/Nuxt/SvelteKit: environment variables use the framework's prefix convention (NEXT*PUBLIC*, NUXT*PUBLIC*, VITE\_).
- [ ] If Express/Fastify/Koa: error handling middleware exists.
- [ ] If ESM (type: "module"): imports use .js extensions where required.

### 7.2 Python

- [ ] Virtual environment tooling specified (requirements.txt, Pipfile, pyproject.toml, setup.py, setup.cfg).
- [ ] Python version constraint specified somewhere (pyproject.toml, .python-version, runtime.txt).
- [ ] If Django: SECRET_KEY is not hardcoded. DEBUG is not True in production config.
- [ ] If FastAPI/Flask: CORS configuration exists and is not wildcard (`*`) in production.

### 7.3 Mobile (React Native, Flutter, Swift, Kotlin)

- [ ] If React Native: New Architecture status is consistent across configs.
- [ ] If iOS: no hardcoded bundle identifiers in source files that differ from the project config.
- [ ] If Android: keystore files are not tracked in git.
- [ ] If push notifications are used: server keys and certificates are not in the repository.
- [ ] App signing credentials are excluded from version control.

### 7.4 Infrastructure / DevOps

- [ ] If Terraform: state files (.tfstate) are not tracked in git.
- [ ] If Kubernetes: no secrets stored in plain YAML manifests.
- [ ] If Docker: images are not running as root unless explicitly justified.
- [ ] If Ansible: vault-encrypted files are identified and vault password is not in the repo.

### 7.5 Monorepo

- [ ] Workspace configuration is valid (workspaces in package.json, pnpm-workspace.yaml, lerna.json, nx.json, turbo.json).
- [ ] Shared dependencies are hoisted or explicitly managed.
- [ ] Each package has its own README or at minimum a description field.
- [ ] CI runs affected-only or avoids rebuilding everything on every commit.

---

## Phase 8: Compound Risk Review

After completing all individual checks, review the full set of findings for compound risks. Individual findings that are low-severity alone may combine into critical exposure.

Examples of compound patterns to check:

- Public repo + missing .gitignore entries + any tracked credentials = critical compound exposure
- No tests + no CI + active deployment config = code ships to production without any verification
- Multiple auth mechanisms + inconsistent implementation across routes = security surface with gaps
- Hallucinated package imports + no lockfile or unpinned dependencies = dependency chain cannot be trusted
- Admin dashboard exists + no auth middleware on admin routes + public repo = open admin access

Report compound risks as single findings with the contributing factors listed. These take priority over their individual components.

---

## Audit Report Output

Save the report as `AUDIT-REPORT.md` in this devsop/ folder. This file is the persistent, shareable deliverable. It can be printed, emailed, or attached to any project management tool.

Use this structure:

```markdown
# DevSOP Audit Report

Project: [project name from package.json, README, or folder name]
Date: [current date]

## Project Fingerprint

- Type: [detected type]
- Language(s): [detected]
- Framework(s): [detected or none]
- Maturity: [greenfield | early-stage | mature | legacy/enterprise]
- Exposure: [public | private | unknown]
- Tier: [1 - Developer | 2 - Supervisor/Lead | 3 - CTO/Security]
- Tier basis: [2-3 sentences summarizing the signals that determined tier]

## External Connections

[List all detected outbound connections: databases, APIs, storage, queues, agents, services]

## Auth Pattern Summary

[List all auth mechanisms found, what they protect, and any mismatches]

## Findings

### [Category Name]

| Check        | Status                           | Detail                            |
| ------------ | -------------------------------- | --------------------------------- |
| [check name] | PRESENT / MISSING / NEEDS REVIEW | [direct explanation, no preamble] |

## Compound Risks

[Any findings that combine into higher-severity exposure than their individual parts]

## LLM Artifact Findings

[Hallucinated content, phantom dependencies, fabricated attributions, unverified patterns. File paths and commit references where applicable.]

## Critical Issues

[Actively dangerous: exposed secrets, compound exposures, auth mismatches, production debug mode]

## Gaps

[Missing elements standard practice expects for this tier and stack]

## Recommendations

[Items that would strengthen the project but are not gaps]
```

## Session Log

### [Date or session identifier]

- [Summary of audit: finding counts, tier classification, starting point]
- [Any notable observations or decisions]

```

The session log lives in this file, not in the TODO. Each time DevSOP runs against this project, append a new entry. This creates the historical record of the project's evolution.

Also present the report contents in the conversation so the user can review it immediately. Then proceed to ONBOARD.md.
```
