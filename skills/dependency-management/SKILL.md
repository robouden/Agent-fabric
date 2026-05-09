---
name: dependency-management
description: "Structured workflow for managing project dependencies — evaluating, adding, updating, removing, and auditing packages."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Dependency Management Skill

## Capabilities
- **Add dependencies** — install new packages with correct version constraints
- **Update dependencies** — upgrade to latest compatible versions
- **Remove dependencies** — clean up unused packages
- **Audit dependencies** — check for CVEs and security issues
- **Lock files** — ensure reproducible installs

## Workflow

### Step 1 — Detect Package Manager
Identify the project's ecosystem from config files:

| Config file | Package manager |
|-------------|----------------|
| `package.json` / `package-lock.json` | npm |
| `package.json` / `yarn.lock` | yarn |
| `package.json` / `pnpm-lock.yaml` | pnpm |
| `requirements.txt` / `pyproject.toml` | pip / poetry |
| `pom.xml` | Maven |
| `build.gradle` / `build.gradle.kts` | Gradle |
| `go.mod` | Go modules |
| `Cargo.toml` | Cargo (Rust) |
| `Gemfile` | Bundler (Ruby) |
| `pubspec.yaml` | Flutter/Dart pub |

### Step 2 — Evaluate Before Adding
Before adding a new dependency, check:
- Does the project already have a package that does the same thing?
- Is the package actively maintained? (check last commit, open issues, bus factor)
- How large is it? (bundle size, transitive dependency count)
- Are there known CVEs? (`npm audit`, `pip audit`, Snyk, GitHub Advisories)
- Does the license allow your use case?

### Step 3 — Install Correctly
When adding or updating dependencies:
- Use exact or caret (`^`) version ranges — never wildcards (`*`)
- Add to the correct group (dependencies vs devDependencies, compile vs test)
- Commit the lock file alongside the manifest change
- Run the full build and test suite after installation

### Step 4 — Audit Regularly
Check the health of existing dependencies:
- Run `npm audit` / `pip audit` / `./gradlew dependencyCheckAnalyze` for CVEs
- Identify unused dependencies (`depcheck`, `pip-autoremove`, IDE inspections)
- Check for major version updates that may require migration
- Review transitive dependencies, not just direct ones

### Step 5 — Clean Up
Remove dependencies that are no longer needed:
- Uninstall via the package manager (don't just delete from the manifest)
- Remove all import/require references from the codebase
- Verify the build and tests still pass after removal
- Commit the updated lock file

## Anti-Patterns to Avoid
- ❌ Adding a dependency without checking if one already exists in the project
- ❌ Using wildcard versions (`*`, `latest`) in manifests
- ❌ Editing lock files manually
- ❌ Adding large packages for trivial functionality
- ❌ Ignoring audit warnings on known CVEs
- ❌ Forgetting to commit lock file changes

## When to Use
- Adding a new library or framework to the project
- Upgrading dependencies for security patches
- Cleaning up unused dependencies
- Setting up a new project
- Periodic dependency health checks

