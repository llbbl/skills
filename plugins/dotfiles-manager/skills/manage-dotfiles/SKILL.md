---
name: manage-dotfiles
version: 0.1.0
description: Use dfm to initialize dotfile backups, track files safely, sync private repositories, and review AI-suggested dotfile improvements
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /dotfiles-manager:manage-dotfiles

Use this skill when the user wants to use `dfm` from `dotfiles-manager` to manage shell/editor/tool dotfiles, set up private backups, track or untrack files, inspect audit state, or ask an AI agent for reviewable dotfile improvements.

Examples:

- "Set up dotfiles-manager on this machine."
- "Track my zsh and git config safely."
- "Review these dotfiles before backing them up."
- "Use dfm to suggest improvements to my shell config."
- "Sync my dotfiles backup repo."

## Safety First

Dotfiles commonly contain secrets and machine-specific state.

- Never track a file before checking for secrets.
- Respect dfm's secrets pre-flight; do not pass `--force` unless the user explicitly accepts the risk.
- Prefer a private GitHub backup repository.
- Review AI suggestions as diffs before applying.
- Avoid tracking bulky caches, histories, generated files, private keys, tokens, and machine-local credentials.

## Install And Initialize

Install `dfm` from GitHub Releases when available, or build from source:

```bash
git clone https://github.com/llbbl/dotfiles-manager.git
cd dotfiles-manager
just build-versioned
./bin/dfm version
```

First-run setup:

```bash
dfm init --remote git@github.com:you/dotfiles-backup.git
```

To create the private remote in one step:

```bash
dfm init --remote git@github.com:you/dotfiles-backup.git --create-remote
```

Optional Turso/libSQL state store:

```bash
dfm init --turso
```

State lives under `~/.local/share/dotfiles/`; config lives at `~/.config/dotfiles/config.toml`.

## Track Files

Before tracking, inspect the file and explain why it is safe or unsafe:

```bash
dfm track ~/.zshrc
dfm track ~/.gitconfig
dfm track ~/.config/starship.toml
```

If dfm blocks on suspected secrets:

1. Read the finding.
2. Remove or externalize the secret.
3. Re-run tracking.
4. Use `--force` only after explicit user approval.

Good candidates:

- shell rc files
- git config without credentials
- editor settings
- prompt/tool config
- aliases and functions

Bad candidates:

- SSH private keys
- `.env` files
- token files
- history files
- caches
- machine-generated state

## Review And Apply Suggestions

Use dfm suggestions as reviewable patches, not automatic edits.

General flow:

```bash
dfm status
dfm suggest
dfm apply
dfm reject
```

Before applying:

- inspect the diff
- reject cosmetic churn that does not improve portability or clarity
- preserve user-specific behavior
- avoid changes that require uninstalled tools

## Sync And Audit

Use sync/status/log commands to understand the current state before changing files:

```bash
dfm status
dfm sync
dfm log
dfm list
```

For backup confidence:

1. Confirm the backup remote is private.
2. Confirm newly tracked files appear in the backup repo.
3. Confirm audit history records what changed and why.

## Recovery

If a tracked file gets worse after an apply, use dfm's review/reject/snapshot workflow first. Avoid hand-editing the backup repo unless dfm state is known to be broken.

For source repo development, use:

```bash
just check
just test-race
just build-versioned
```

## User-Facing Guidance

When helping a user build a dotfiles system:

- start small with 2-3 safe files
- explain what should stay machine-local
- keep a written list of tracked files and rationale
- add AI improvements gradually
- sync after each meaningful batch
