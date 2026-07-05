---
name: protect-lockfile
version: 0.1.0
description: Use pkglock to switch package-lock.json between local npm registries and the public registry, and install safeguards against committing local URLs
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /pkglock:protect-lockfile

Use this skill when the user wants to use `pkglock` in a JavaScript/TypeScript project with `package-lock.json`, especially when switching between a local npm registry such as Verdaccio/Nexus and `https://registry.npmjs.org`.

Examples:

- "Make this package-lock safe to commit."
- "Switch this package-lock to my local Verdaccio."
- "Install the pkglock pre-commit hook."
- "Check whether local registry URLs leaked into package-lock.json."

## Project Fit

`pkglock` operates on `./package-lock.json` in the current directory. Run it from the project root.

Use it when:

- the project uses npm lockfiles
- local registry mirrors are used for faster installs
- contributors need to avoid committing `localhost`, `.local`, `.lan`, private IP, or loopback registry URLs

Do not use it for `pnpm-lock.yaml`, `yarn.lock`, or Bun lockfiles.

## Install

```bash
cargo install pkglock
```

Confirm:

```bash
pkglock --help
```

If Cargo bin commands are not on PATH, add `$HOME/.cargo/bin`.

## Safe-To-Commit Workflow

Before committing a project with `package-lock.json`:

```bash
pkglock --to-public
git diff -- package-lock.json
```

This rewrites local-looking resolved URL hosts back to `https://registry.npmjs.org` while preserving package paths, query strings, and fragments.

Local-looking hosts include:

- `localhost`
- `.test`, `.local`, or `.lan`
- private IPv4 ranges
- loopback addresses

Review the diff before committing.

## Switch To Local Registry

Explicit local registry:

```bash
pkglock --to-local http://localhost:4873
pkglock --to-local https://verdaccio.lan/repo
```

Autodetect from a bare `registry=` line in `./.npmrc`:

```bash
pkglock --to-local
```

Do not put credentials in the URL. Use `.npmrc` auth token entries for authentication.

## Pre-Commit Hook

Install the local Git hook:

```bash
pkglock install-hook
```

The hook runs `pkglock --to-public` when `package-lock.json` is staged, then re-stages the file.

Important behavior:

- `.git/hooks/` is local-only and not committed.
- Existing hooks are not overwritten.
- The hook fails the commit if `pkglock` is missing from PATH.
- The hook rewrites the working-tree lockfile, not only the staged copy.

If an existing hook is present, integrate this pattern manually:

```sh
if git diff --cached --name-only --diff-filter=ACMR | grep -q '^package-lock\.json$'; then
    if ! command -v pkglock >/dev/null 2>&1; then
        echo "pkglock: command not found on PATH" >&2
        exit 1
    fi
    cd "$(git rev-parse --show-toplevel)"
    pkglock --to-public
    git add package-lock.json
fi
```

## Config-File Mode

Use config-file mode only for unusual registry setups:

```json
{
  "local": "http://localhost:4873",
  "remote": "https://registry.npmjs.org"
}
```

Commands:

```bash
pkglock --local
pkglock --remote
```

These replace every resolved URL authority unconditionally. Prefer `--to-public` and `--to-local` for safer conditional behavior.

## Safety Rules

- Always run from the directory containing `package-lock.json`.
- Always review `git diff -- package-lock.json` after rewriting.
- Do not commit local registry URLs unless the user explicitly wants an internal-only lockfile.
- Do not use URL-embedded credentials.
- Do not assume scoped `.npmrc` registries are honored by `pkglock --to-local`; it reads only a bare `registry=` line.
- Do not use `pkglock` on pnpm/yarn/bun lockfiles.
