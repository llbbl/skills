---
name: develop
version: 0.1.0
description: Work on llbbl/esm, a PHP enum-state-machine library, with its local quality gates and API constraints
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /esm:develop

Use this skill when the user asks to work on `llbbl/esm`, `enum-state-machine`, or the PHP enum state machine library, including feature work, bug fixes, API review, tests, releases, or documentation updates.

## Project Model

`llbbl/esm` is a framework-agnostic PHP library: `llbbl/enum-state-machine`.

- Runtime floor: PHP `>=8.1`.
- Core idea: enum cases declare transitions with attributes; the enum is the state-machine definition.
- Namespace: `EnumStateMachine\`.
- Engine: `EnumStateMachine\StateMachine` with `can()`, `transitionTo()`, and `getCurrentState()`.
- Transition metadata lives in `#[Transition]` attributes on enum cases or the enum class.
- `#[StateMachineConfig]` configures optional PSR-14 event dispatch.
- Optional integrations: PSR-11 container for resolving guards/hooks and PSR-14 event dispatcher for transition events.
- Guards implement `GuardInterface` and must stay side-effect-free because `can()` runs them.
- Hooks implement `StateHookInterface`; `before` hooks run before state changes and `after` hooks run after state changes.

## Safety Rules

- Preserve PHP 8.1 syntax compatibility in runtime code under `src/`.
- Do not add runtime framework dependencies.
- Do not add a Composer `version` field; releases are tag-driven.
- Treat `composer.lock` as the dev-toolchain lock for current supported PHP, not proof that PHP 8.1 runtime support was dropped.
- Avoid broad API changes unless the user explicitly asks for a breaking change.
- Keep guards side-effect-free in examples and tests.
- Keep hook failure behavior explicit: before-hook failures leave state unchanged; after-hook failures leave state changed and throw `HookExecutionException`.

## Workflow

1. Inspect the current branch and user changes before editing.
2. Read the relevant source and tests before changing API behavior.
3. Put runtime changes in `src/` and behavioral coverage in `tests/Unit/`.
4. Use fixtures under `tests/Fixtures/` for reusable enum, guard, hook, dispatcher, and container cases.
5. Update README examples only when the public API or recommended usage changes.
6. Run the focused Pest test first, then the full quality gate when practical.

## Commands

Install dependencies:

```bash
just install
```

Run focused tests:

```bash
just test tests/Unit/StateMachineTest.php
```

Run static analysis:

```bash
just stan
```

Run the main quality gate:

```bash
just qa
```

Check legacy PHP compatibility:

```bash
just legacy all
```

Use `just legacy run 8.2` for a single legacy target. PHP 8.1 legacy coverage is syntax-only because Pest 3 cannot install there.

## Release Notes

Packagist publishes from git tags. The repo's `justfile` provides:

```bash
just release-patch
just release-minor
just release-major
```

These run `just qa`, create an annotated tag, and push the tag. Only use them when the user explicitly asks to cut a release.
