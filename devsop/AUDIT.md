# DevSOP Audit Protocol

You are executing a read-only audit of the project that contains this folder. Do not modify, create, or delete any files during this phase. Produce a structured report at the end.

---

## Phase 1: Project Fingerprinting

Before auditing, determine what you are looking at. Scan the project root and up to three levels of subdirectories. Identify:

- **Primary language(s):** Check file extensions, package manifests (package.json, requirements.txt, Cargo.toml, go.mod, Gemfile, pom.xml, build.gradle, mix.exs, composer.json, pubspec.yaml, *.csproj, CMakeLists.txt).
- **Framework(s):** Check for framework-specific files (next.config.*, nuxt.config.*, angular.json, svelte.config.*, django settings, Rails Gemfile, Spring Boot application.properties, FastAPI/Flask imports, Laravel artisan).
- **Project type:** Web app, API, library/package, CLI tool, mobile app (React Native, Flutter, Swift, Kotlin), monorepo, data pipeline, infrastructure-as-code, documentation site, game engine project, embedded/firmware, desktop app, browser extension, or other.
- **Maturity signal:** Count total commits if git history is accessible. Under 20 = greenfield. 20-200 = early-stage. 200-2000 = mature. Over 2000 = legacy or enterprise. If git is not accessible, estimate from file count and directory depth.
- **Exposure level:** Is this a public repository? Check for LICENSE file, .git/config remote URLs, GitHub-specific files (.github/), visibility indicators. Public repos have different security requirements than private ones.

Store these findings internally. Every subsequent check is conditional on this fingerprint.

---

## Phase 2: Repository Hygiene

### 2.1 Version Control

- [ ] `.gitignore` exists.
- [ ] `.gitignore` covers the basics for the detected language/framework. Check against known defaults:
  - Node.js: node_modules/, .env, dist/, .next/, coverage/
  - Python: __pycache__/, *.pyc, .env, venv/, .venv/, dist/, *.egg-info/
  - Ruby: vendor/bundle, .env, *.gem, .bundle/
  - Go: vendor/ (if not using modules), *.exe, *.test
  - Rust: target/, Cargo.lock (if library)
  - Java/Kotlin: build/, .gradle/, *.class, *.jar
  - .NET: bin/, obj/, *.user, *.suo
  - Flutter/Dart: .dart_tool/, build/, .flutter-plugins
  - General: .DS_Store, Thumbs.db, *.log, .idea/, .vscode/ (check if settings are project-specific or personal), *.swp, *.swo
- [ ] If the project uses Docker: .dockerignore exists and excludes .git, node_modules, .env, and test fixtures.
- [ ] No merge conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) in any tracked file.
- [ ] If both package-lock.json and yarn.lock exist, flag as a lockfile conflict. Same for pnpm-lock.yaml coexisting with either.

### 2.2 Dependency Health

- [ ] A lockfile exists and matches the package manager in use.
- [ ] If package.json exists: check for `*` or `latest` version ranges (flag as unpinned).
- [ ] If requirements.txt exists: check for unpinned dependencies (no `==` version).
- [ ] Check for known deprecated or abandoned packages if identifiable from naming conventions or version age.
- [ ] If a monorepo: check that workspaces/packages share compatible dependency versions and do not have conflicting peer dependencies.

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

### 3.2 Access Control and Permissions

- [ ] If the project has multiple user roles or permission tiers: are they defined somewhere? (middleware, route guards, RBAC config, database schema with role columns)
- [ ] If the project has an admin panel or dashboard: is it behind authentication?
- [ ] If the project exposes API endpoints: are they protected? Look for auth middleware applied to route groups.
- [ ] If the project uses file uploads: check for path traversal protections, file type validation, and size limits.

### 3.3 Public Repository Exposure (if public)

- [ ] README does not contain internal hostnames, IP addresses, internal tool URLs, staging/dev environment URLs, or employee names.
- [ ] No internal documentation (meeting notes, architecture decision records with business-sensitive context, salary data, customer names) is tracked in a public repo.
- [ ] No test fixtures contain real user data, real email addresses, or production database snapshots.
- [ ] CODEOWNERS file does not expose internal org structure that should be private.
- [ ] GitHub Actions or CI config does not print secrets to logs (check for `echo $SECRET` patterns).

---

## Phase 4: Documentation Completeness

### 4.1 Core Documentation

- [ ] README.md exists and contains:
  - What the project does (not just a title)
  - How to install/set up locally
  - How to run it
  - How to run tests (if tests exist)
- [ ] If the project is a library or package: API documentation or usage examples exist.
- [ ] If the project has contributors beyond the author: CONTRIBUTING.md exists with process guidance.
- [ ] CHANGELOG.md or a release history exists (if the project has tagged releases).
- [ ] If the project requires environment variables: they are documented somewhere (README, .env.example, or a dedicated config doc).

### 4.2 Architecture Documentation

- [ ] If the project has more than 10 source files: is there any architecture documentation? (docs/ folder, README sections, ADRs, diagrams)
- [ ] If the project is a monorepo: each package/workspace has its own README or description.
- [ ] If the project has database schemas: are migrations documented or self-describing? Can a new developer understand the data model?
- [ ] If the project integrates with external services: are those integrations documented? (which services, what they do, what credentials are needed)

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
- [ ] If CI exists: does it build the project (confirming the project compiles/bundles)?
- [ ] If the project deploys: is the deployment process documented or codified? (Dockerfile, docker-compose, Procfile, serverless.yml, terraform/, k8s manifests, deploy scripts)
- [ ] If the project has deployment: are staging and production environments distinguishable in config?

### 5.3 Testing

- [ ] Tests exist (any test runner, any test directory convention).
- [ ] If tests exist: do they run? (Check for test scripts in package.json, Makefile, tox.ini, or equivalent)
- [ ] If tests exist: what is the approximate coverage? (Count test files relative to source files as a rough heuristic -- not a substitute for actual coverage tools, but a signal)
- [ ] If no tests exist and the project has more than 20 source files: flag as MISSING.

---

## Phase 6: Contextual Checks

These checks apply only when the fingerprint matches. Skip any that do not apply.

### 6.1 Node.js / JavaScript / TypeScript Projects
- [ ] `engines` field in package.json specifies Node version.
- [ ] If TypeScript: tsconfig.json exists with `strict` enabled or an explicit relaxation documented.
- [ ] If Next.js/Nuxt/SvelteKit: environment variables use the framework's prefix convention (NEXT_PUBLIC_, NUXT_PUBLIC_, VITE_).
- [ ] If Express/Fastify/Koa: error handling middleware exists (not just default 500s).
- [ ] If the project uses ESM (type: "module"): imports use .js extensions where required.

### 6.2 Python Projects
- [ ] Virtual environment tooling specified (requirements.txt, Pipfile, pyproject.toml, setup.py, setup.cfg).
- [ ] Python version constraint specified somewhere (pyproject.toml, .python-version, runtime.txt).
- [ ] If Django: SECRET_KEY is not hardcoded. DEBUG is not True in production config.
- [ ] If FastAPI/Flask: CORS configuration exists and is not wildcard (`*`) in production.

### 6.3 Mobile Projects (React Native, Flutter, Swift, Kotlin)
- [ ] If React Native: New Architecture status is consistent (reactNativeArchitectures in build.gradle matches project intent).
- [ ] If iOS: no hardcoded bundle identifiers in source files that differ from the project config.
- [ ] If Android: keystore files are not tracked in git.
- [ ] If the app uses push notifications: server keys and certificates are not in the repository.
- [ ] App signing credentials are excluded from version control.

### 6.4 Infrastructure / DevOps Projects
- [ ] If Terraform: state files (.tfstate) are not tracked in git.
- [ ] If Kubernetes: no secrets are stored in plain YAML manifests.
- [ ] If Docker: images are not running as root unless explicitly justified.
- [ ] If Ansible: vault-encrypted files are identified and vault password is not in the repo.

### 6.5 Monorepo Projects
- [ ] Workspace configuration is valid (workspaces in package.json, pnpm-workspace.yaml, lerna.json, nx.json, turbo.json).
- [ ] Shared dependencies are hoisted or explicitly managed.
- [ ] Each package has its own README or at minimum a description field in its manifest.
- [ ] CI runs affected-only or has a mechanism to avoid rebuilding everything on every commit.

---

## Output Format

Produce the audit report in this structure. Use the exact status labels.

```
# DevSOP Audit Report

## Project Fingerprint
- Type: [detected type]
- Language(s): [detected]
- Framework(s): [detected or none]
- Maturity: [greenfield | early-stage | mature | legacy/enterprise]
- Exposure: [public | private | unknown]

## Findings

### [Category Name]
| Check | Status | Detail |
|-------|--------|--------|
| [check name] | PRESENT / MISSING / NEEDS REVIEW | [one-line explanation] |

### Critical Issues (immediate action)
[List anything that is actively dangerous: exposed secrets, missing .gitignore on a public repo, tracked credentials, production debug mode]

### Gaps (should address)
[List anything missing that standard practice expects: no tests, no CI, no .env.example, undocumented architecture]

### Recommendations (would improve)
[List anything that would strengthen the project but is not a gap: adding CODEOWNERS, improving README, adding type checking]
```

After producing this report, inform the user you are now proceeding to the onboarding phase and will read ONBOARD.md next.
