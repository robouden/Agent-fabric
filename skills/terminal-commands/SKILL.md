---
name: terminal-commands
description: "Execute shell commands, install packages, run build scripts, and manage processes."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Terminal Commands Skill

## Capabilities
- **Run commands** — execute shell commands in the project directory
- **Install packages** — npm install, pip install, etc.
- **Run scripts** — build, test, lint, format
- **Background processes** — start servers, watchers
- **Check output** — verify command results

## Best Practices
1. Always use the correct shell syntax for the user's OS.
2. Use `&&` to chain dependent commands.
3. For long-running processes, run in background mode.
4. Check command exit codes and output for errors.
5. Never run destructive commands (rm -rf, format disk) without explicit user confirmation.

## Safety Rules
- Never execute commands that could damage the system.
- Never expose secrets or credentials in command output.
- Prefer dry-run flags when available for destructive operations.

## When to Use
- Installing dependencies for a project.
- Running test suites or build pipelines.
- Starting development servers.
- Executing database migrations.

