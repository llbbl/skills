---
name: rewrite-code
version: 0.1.0
description: Use grepwrite gw to find matches, preview code rewrites, apply transactional edits with snapshots, and undo safely
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /grepwrite:rewrite-code

Use this skill when the user wants to use `grepwrite` (`gw`) for code search, safe regex or AST-aware rewrites, LLM-friendly output, transactional apply, snapshots, or undo.

Examples:

- "Use gw to find TODOs in this repo."
- "Preview replacing this API name across src."
- "Apply a safe rewrite and keep an undo snapshot."
- "Undo the last grepwrite rewrite."
- "Use caveman output for a compact agent-readable match list."

## Install

```bash
cargo install grepwrite
```

Confirm:

```bash
gw --help
```

`gw` wraps ripgrep-style search and adds a transactional rewrite layer with snapshots.

## Search Workflow

Find matches:

```bash
gw find 'pattern' src/
```

Use output formats deliberately:

```bash
gw find 'pattern' src/ -o compact
gw find 'pattern' src/ -o caveman
gw find 'pattern' src/ -o json
```

Use `caveman` when you need a token-minimal `path:line` list for an agent. Use `json` when scripting or post-processing.

AST-aware search:

```bash
gw find 'pattern' src/ --in function
gw find 'pattern' src/ --in class
gw find 'pattern' src/ --in imports
gw find 'pattern' src/ --in comments
```

## Rewrite Workflow

Preview first. Rewrites are dry-run by default:

```bash
gw rewrite 'oldName' 'newName' src/ -o diff
```

Review the diff. If it is correct, apply:

```bash
gw rewrite 'oldName' 'newName' src/ --apply
```

Applied rewrites are atomic per file and wrapped in a git-ref snapshot. Record the snapshot ID in your notes when coordinating multi-step work.

## Undo

List snapshots:

```bash
gw snapshots
```

Undo the latest snapshot:

```bash
gw undo
```

Undo a specific snapshot:

```bash
gw undo --snapshot SNAPSHOT_ID
```

`gw undo` refuses to clobber edits made after the snapshot. If undo fails, inspect the reported files and decide whether to revert manually.

## Agent-Safe Rules

- Always preview rewrites before `--apply`.
- Use narrow paths first, such as `src/` or a specific package.
- Do not use broad root rewrites until the preview is reviewed.
- Prefer `-o diff` for human review and `-o json` for tooling.
- Use AST constraints when a plain regex risks touching comments, strings, or unrelated contexts.
- After `--apply`, run the project’s relevant tests or type checks.
- Keep snapshot IDs in the final report for applied changes.

## Common Patterns

Find candidate identifiers:

```bash
gw find '\boldName\b' src/ -o compact
```

Preview a rename:

```bash
gw rewrite '\boldName\b' 'newName' src/ -o diff
```

Apply after review:

```bash
gw rewrite '\boldName\b' 'newName' src/ --apply
```

Search imports only:

```bash
gw find 'old-package' src/ --in imports -o compact
```

## Validation

After applied rewrites, run the target repo’s checks. Examples:

```bash
cargo test
pnpm test
go test ./...
just check
```

If validation fails, inspect the failure before using undo; sometimes the rewrite is correct and only a follow-up adjustment is needed.
