---
name: improve-coverage
version: 0.1.0
description: Install and use uncov to find low-coverage files from Vitest/Istanbul output, prioritize tests, and wire coverage checks into JS/TS projects
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /uncov:improve-coverage

Use this skill when the user wants to add `uncov` to a JavaScript or TypeScript project, inspect low coverage files, improve test coverage, or make coverage checks CI-friendly.

Examples:

- "Add uncov to this repo."
- "Show me what has the worst coverage."
- "Use uncov to plan coverage improvements."
- "Fail CI when files are under 20% coverage."
- "Set up Vitest coverage and low-coverage reporting."

## Project Fit

`uncov` reads Vitest/Istanbul `coverage/coverage-summary.json` and reports files at or below a configurable line coverage threshold.

Use it when the target project:

- runs JS/TS tests with Vitest or produces Istanbul-compatible JSON summary output
- wants a small CLI for finding neglected files
- needs JSON output or exit codes for CI automation

If the project does not use Vitest, first confirm whether its coverage tool can emit `coverage-summary.json`.

## Install Or Run

Prefer the project’s package manager. Detect it from lockfiles and existing scripts.

Global install options:

```bash
pnpm add -g @llbbl/uncov
npm install -g @llbbl/uncov
bun add -g @llbbl/uncov
```

For one-off use, prefer the package runner already used by the project:

```bash
pnpm dlx @llbbl/uncov --help
npx @llbbl/uncov --help
bunx @llbbl/uncov --help
```

If the user wants the standalone binary, use the installer from `llbbl/uncov`:

```bash
curl -fsSL https://raw.githubusercontent.com/llbbl/uncov/main/install.sh | bash
```

## Setup Workflow

1. Inspect `package.json`, lockfiles, test scripts, and `vitest.config.*`.
2. If coverage is not configured, run a dry run first:

```bash
uncov init --dry-run
```

3. If the changes match the project, run:

```bash
uncov init
```

4. Verify setup:

```bash
uncov check
```

5. Run coverage, then report low-coverage files:

```bash
pnpm test:coverage
uncov
```

Adapt `pnpm test:coverage` to the project’s package manager and scripts.

## Reporting Workflow

Use a threshold that matches the question:

```bash
uncov --threshold 0
uncov --threshold 10
uncov --threshold 50
```

Use JSON when ranking or scripting:

```bash
uncov --threshold 50 --json
```

Use `--fail` only when the user wants enforcement:

```bash
uncov --threshold 20 --fail
```

Exit code `1` means low-coverage files were found with `--fail`. Exit code `2` means missing coverage output or a setup/config error.

## Turning Reports Into Tests

When improving coverage:

1. Start with low-coverage files that contain important behavior, not generated files, barrels, or simple constants.
2. Read each target file and nearby tests before writing new tests.
3. Prefer meaningful behavior tests over shallow line-hitting tests.
4. Group recommendations by risk and effort:
   - critical behavior with no coverage
   - error handling and edge cases
   - integration boundaries
   - low-value files to exclude
5. Add or update `uncov.config.json` only for durable project policy.

Example config:

```json
{
  "threshold": 20,
  "exclude": ["**/*.test.ts", "**/index.ts"],
  "failOnLow": false,
  "coveragePath": "coverage/coverage-summary.json"
}
```

## CI Guidance

For advisory reporting, keep `failOnLow` false and publish the JSON/text output in logs.

For enforcement:

```bash
uncov --threshold 20 --fail
```

Introduce enforcement gradually. A useful pattern is:

1. report only at a high threshold to understand the backlog
2. enforce only zero-coverage or very-low-coverage files
3. raise the threshold as tests improve

## Safety Rules

- Do not delete or rewrite existing test configuration without reading it first.
- Avoid adding coverage thresholds that fail CI unless the user asks for enforcement.
- Keep generated files, type-only files, barrel exports, and framework boilerplate out of the improvement plan unless they hide real behavior.
- If coverage output is missing, fix test coverage generation before interpreting `uncov` results.
