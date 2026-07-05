---
name: maintain
version: 0.1.0
description: Use cargo-upkeep to audit, update, score, and plan maintenance for Rust crates and workspaces
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /upkeep-rs:maintain

Use this skill when the user wants to maintain a Rust crate or workspace with `cargo-upkeep`, including dependency review, RustSec audits, project health scoring, dependency trees, unused dependency checks, unsafe-code review, or CI maintenance gates.

Examples:

- "Run a Rust maintenance pass."
- "Check this crate for outdated dependencies."
- "Audit this Rust project for vulnerabilities."
- "How healthy is this Rust workspace?"
- "Use cargo-upkeep to plan what to fix next."

## Project Fit

`cargo-upkeep` is a Cargo subcommand for Rust project maintenance. It provides one interface and JSON-friendly output for:

- `detect` - project/workspace configuration
- `deps` - outdated dependency reporting with semver classification
- `audit` - RustSec advisory scanning
- `quality` - project health grade and score breakdown
- `tree` - enhanced dependency tree output
- `unused` - unused dependency detection through `cargo-machete`
- `unsafe-code` - unsafe dependency analysis through `cargo-geiger`

Use it inside Rust projects that have `Cargo.toml`. For workspaces, start at the workspace root.

## Install

Check whether the tool is available:

```bash
cargo upkeep --help
```

If missing, install from crates.io:

```bash
cargo install cargo-upkeep
```

`cargo binstall cargo-upkeep` is also acceptable when the project or user already uses `cargo-binstall`.

For commands that depend on optional tools:

```bash
cargo install cargo-machete
cargo install cargo-geiger
```

Only install optional tools when the user asks for unused-dependency or unsafe-code analysis, or when `cargo upkeep quality` reports those metrics as blocked by missing tools.

## First Pass

1. Inspect `Cargo.toml`, `Cargo.lock`, workspace layout, MSRV/rust-version, and existing CI commands.
2. Run project detection:

```bash
cargo upkeep detect --json
```

3. If `Cargo.lock` is missing and security/dependency checks need it, generate one:

```bash
cargo generate-lockfile
```

4. Prefer JSON output for analysis and concise plain-language summaries for the user.

## Dependency Review

Run:

```bash
cargo upkeep deps --json
```

Classify results by update type:

- patch: normally low risk
- minor: medium risk; read release notes for important crates
- major: high risk; plan one major at a time

For actual upgrades:

1. Change one dependency group at a time.
2. Run `cargo build` or the project’s documented build.
3. Run `cargo test`.
4. Run the repo’s quality gate if one exists.
5. Summarize crate, current version, target version, risk, and validation.

Do not batch unrelated major upgrades.

## Security Audit

Run:

```bash
cargo upkeep audit --json
```

For each RustSec finding, report:

- advisory ID
- affected crate/version
- patched version or mitigation
- severity and practical impact
- whether the dependency is direct or transitive when determinable

Prioritize critical/high findings before routine dependency freshness.

## Quality Report

Run:

```bash
cargo upkeep quality --json
```

Explain the grade and score drivers. Convert the result into a short action plan:

1. security findings
2. blocked or missing maintenance signals
3. dependency upgrades
4. clippy/test/doc/CI improvements
5. low-risk cleanup

If the user asks for fixes, implement the top safe item first and validate it.

## Tree, Unused, And Unsafe Analysis

Use dependency tree output to explain why a crate is present:

```bash
cargo upkeep tree --json
```

Use unused dependency detection only after installing `cargo-machete`:

```bash
cargo upkeep unused --json
```

Use unsafe-code analysis only after installing `cargo-geiger`:

```bash
cargo upkeep unsafe-code --json
```

Treat unsafe-code findings as review targets, not automatic failures. Summarize where unsafe code enters the graph and whether it comes from common low-level crates, build tooling, or application dependencies.

## CI Guidance

For advisory reporting, add non-blocking commands first. For enforcement, start with checks the project can pass today.

Common maintenance gate:

```bash
cargo upkeep audit --json
cargo upkeep deps --json
cargo upkeep quality --json
```

Do not add failing CI enforcement unless the user explicitly asks for a hard gate.

## Safety Rules

- Do not use `cargo audit`, `cargo outdated`, `cargo machete`, or `cargo geiger` directly when the same workflow is available through `cargo upkeep`.
- Do not update multiple major dependencies in one change unless the user explicitly asks.
- Do not remove dependencies solely from an unused report; inspect code, features, examples, tests, build scripts, and optional cfg paths first.
- Preserve the project’s MSRV policy. Check `rust-version` before using newer Rust features.
- Do not commit, tag, publish, or release unless the user explicitly asks.
