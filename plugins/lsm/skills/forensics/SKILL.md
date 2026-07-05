---
name: forensics
version: 0.1.0
description: Investigate lsm audit logs with query patterns for suspicious local secret access
allowed-tools: Bash, Read, Grep, Glob
---

# /lsm:forensics

Use this skill when the user asks for forensic analysis of `lsm` audit logs, such as:

- "Did anything weird touch prod last night?"
- "Show me non-interactive secret reads."
- "Which parent processes accessed secrets recently?"
- "Was there a burst of secret reads?"
- "Investigate this time window."

## Prerequisites

The `lsm` binary must be installed and available on `PATH`.

Before running any `lsm` command, verify it is available:

```bash
command -v lsm >/dev/null 2>&1 || {
  echo "lsm not found on PATH - install it with: brew install llbbl/tap/lsm" >&2
  exit 1
}
```

## Safety Rules

- Never print secret values.
- Treat audit output as sensitive operational metadata.
- Prefer `--format json` for analysis and summarize findings in plain language.
- Use names, timestamps, event types, parent process names, TTY presence, agent marker status, and counts.
- Do not read `~/.lsm/key.txt` or encrypted `*.age` files.
- Ask before running broad or long time-window queries if the user did not specify scope.

## Workflow

1. Identify the audit log path. By default, `lsm audit query` uses the configured local audit log.
2. Ask for or infer the time window.
3. Run focused `lsm audit query --format json` commands.
4. Group results by event type, app, env, parent process, TTY presence, and agent marker.
5. Call out unusual patterns:
   - access outside the expected window
   - non-interactive reads
   - new or rare parent processes
   - bursts of reads
   - prod access from unexpected apps or commands
6. Summarize findings without dumping raw logs unless the user asks.

## Common Commands

Inspect supported filters:

```bash
lsm audit query --help
```

Query recent events as JSON:

```bash
lsm audit query --since 24h --format json
```

Filter by app or environment:

```bash
lsm audit query --app myapp --env production --since 24h --format json
```

Find non-interactive access:

```bash
lsm audit query --tty absent --since 24h --format json
```

## Examples

See `examples.md` for canned investigation patterns.
