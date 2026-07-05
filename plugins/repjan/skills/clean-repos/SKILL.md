---
name: clean-repos
version: 0.1.0
description: Use repjan to audit GitHub repositories, review archive candidates, export decisions, and safely archive or unarchive repos
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /repjan:clean-repos

Use this skill when the user wants to clean up a GitHub account or organization with `repjan`, including auditing repositories, identifying archive candidates, exporting a review list, or safely archiving/unarchiving repositories.

Examples:

- "Use repjan to review my old repos."
- "Help me decide what to archive in this GitHub org."
- "Export archive candidates before I clean up repositories."
- "Run a repository janitor pass for acme-corp."

## Safety First

Archiving repositories is reversible on GitHub, but it still hides repos from normal active use and can disrupt collaborators.

- Never archive automatically just because `repjan` flags a candidate.
- Review marked repositories before confirming an archive action.
- Export marked repos before bulk archiving when the user is making portfolio/org cleanup decisions.
- Treat private repositories as sensitive; repjan hides them by default and highlights when private repos are visible.
- Do not run `repjan db reset --force` unless the user explicitly asks to delete local repjan state.

## Prerequisites

`repjan` requires:

- Go 1.21+ when building from source
- GitHub CLI (`gh`) installed and authenticated
- GitHub permissions for the target user or organization

Check GitHub auth:

```bash
gh auth status
```

Build from source when no packaged binary is available:

```bash
git clone https://github.com/llbbl/repjan.git
cd repjan
go build -o repjan ./cmd/repjan
```

## Audit Workflow

1. Identify the owner to audit. If omitted, repjan uses the authenticated GitHub user.
2. Sync repository metadata first for a non-TUI sanity check:

```bash
repjan sync --owner OWNER
repjan db status
```

3. Launch the TUI:

```bash
repjan --owner OWNER
```

4. Use filters to narrow the review:
   - `o` - old repos, 365+ days inactive
   - `n` - repos with no stars
   - `f` - forks only
   - `l` - language filter
   - `p` - include private repos
   - `x` - include archived repos
5. Mark candidate repos with `Space`.
6. Export marked repos with `e` before archiving when the review needs an audit trail.
7. Confirm archive/unarchive actions only after checking the confirmation modal.

## Archive Candidate Heuristics

repjan flags candidates for reasons including:

- no activity in 1+ year or 2+ years
- zero stars and zero forks
- stale forks, inactive for 180+ days
- inactive legacy-language repos such as PHP, CoffeeScript, Perl, ActionScript, or Objective-C

Use these as triage signals, not final decisions. Keep repos that are still useful, documented, depended on, or part of a public story.

## Decision Framework

Recommend archiving when most of these are true:

- no recent activity and no planned future work
- no stars, forks, issues, or external users
- duplicated by a better maintained repo
- stale experiment that no longer represents the owner well
- fork that is no longer needed

Recommend keeping when any of these are true:

- active users, stars, forks, packages, or docs link to it
- portfolio value or historical context matters
- it is a dependency, template, demo, or reference implementation
- it needs a README/status update more than archiving
- the owner is unsure

## Export Review

Exported JSON includes owner, total marked, repo names, stars, forks, days since activity, reasons, language, last push, fork/private status, and timestamp.

Use the export to produce a review table:

- repo
- reason
- risk of archiving
- suggested action: archive, keep, update README, transfer, or revisit later

## Troubleshooting

If GitHub fetch fails:

```bash
gh auth status
gh repo list OWNER --limit 5
```

If the TUI appears stale, force a sync or adjust sync interval:

```bash
repjan sync --owner OWNER
repjan --owner OWNER --sync-interval 0s
```

For debugging:

```bash
repjan --owner OWNER --log-level debug
repjan --owner OWNER --log-level debug --log-format json
```

Database inspection:

```bash
repjan db path
repjan db status
repjan db migrate
```

## Maintainer Notes

When working inside the `llbbl/repjan` source repo, follow its local workflow:

- use feature branches, not direct main commits
- use `just check` for lint and tests
- use `just ship "<conventional-commit-message>"` to check, sync beads, commit, and push
- every Go source file must include the repo's SPDX header
