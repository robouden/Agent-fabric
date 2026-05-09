---
name: security-audit
description: "Structured workflow for auditing code and dependencies for security vulnerabilities, exposed secrets, and anti-patterns."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Security Audit Skill

## Capabilities
- **CVE scanning** — check dependencies for known vulnerabilities
- **Secret detection** — find hardcoded passwords, API keys, tokens
- **OWASP checks** — detect common web security issues
- **Auth review** — verify authentication and authorization patterns
- **Input validation** — check for injection vulnerabilities

## Workflow

### Step 1 — Define Audit Scope
Before auditing, clarify the boundaries:
- **Target** — specific files, a PR diff, a module, or the full project?
- **Depth** — quick scan or comprehensive review?
- **Focus** — general audit or specific concern (e.g., auth, secrets, dependencies)?

### Step 2 — Scan Dependencies
Check third-party code for known vulnerabilities:
- Run the package manager's audit command (`npm audit`, `pip audit`, `./gradlew dependencyCheckAnalyze`)
- Review lock files for outdated or unmaintained packages
- Check for dependencies with known CVEs in security databases
- Flag any dependency pulled from untrusted or unofficial sources

### Step 3 — Scan for Secrets
Search the codebase for exposed credentials:
- Grep for patterns: API keys, tokens, passwords, connection strings, private keys
- Check config files, environment files, and test fixtures
- Verify `.gitignore` excludes `.env`, credentials, and key files
- Review git history for previously committed secrets (they persist even if deleted)

### Step 4 — Review Code for Vulnerabilities
Examine the code against these categories:

| Category | What to look for |
|----------|-----------------|
| **Injection** | Unsanitized user input in SQL, HTML, shell commands, LDAP, or template engines |
| **Authentication** | Weak password hashing, missing rate limiting, insecure session handling, token leakage |
| **Authorization** | Missing access checks, IDOR (direct object references), privilege escalation paths |
| **Data Exposure** | Sensitive data in logs, error messages, API responses, or client-side code |
| **Transport** | HTTP instead of HTTPS, missing TLS validation, insecure cookie flags |
| **CORS/CSRF** | Overly permissive CORS (`*`), missing CSRF tokens on state-changing operations |
| **Deserialization** | Untrusted data deserialized without validation |
| **Cryptography** | Weak algorithms (MD5, SHA1 for hashing), hardcoded keys, predictable random values |

### Step 5 — Report Findings
Classify and report each finding:

- 🔴 **Critical** — actively exploitable, immediate fix required
- 🟠 **High** — significant risk, fix in current sprint
- 🟡 **Medium** — moderate risk, fix soon
- 🟢 **Low** — minimal risk, fix when convenient

For each finding, include:
1. **What** — the vulnerability or risk
2. **Where** — file path and line reference
3. **Impact** — what an attacker could achieve
4. **Fix** — recommended remediation with code example when possible

## Anti-Patterns to Avoid
- ❌ Reporting only dependency CVEs and ignoring application code
- ❌ Flagging theoretical risks without checking if they are actually reachable
- ❌ Listing every low-severity finding without prioritization
- ❌ Recommending fixes that break functionality without noting the tradeoff
- ❌ Skipping git history review (secrets may be committed then "deleted")

## When to Use
- Before merging code to main/production branches
- When adding new dependencies
- During periodic security audits
- When handling user authentication or sensitive data
- After discovering a vulnerability in a dependency

