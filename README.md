# llbbl-skills

Claude Code plugin marketplace for llbbl skills.

This repository is the public marketplace/index that people add once. A plugin's canonical source can still live elsewhere when it has its own CLI, releases, or distribution workflow.

## Install

```text
/plugin marketplace add llbbl/skills
```

Then install a plugin from the marketplace:

```text
/plugin install lsm@llbbl-skills
/plugin install upkeep@llbbl-skills
/plugin install esm@llbbl-skills
/plugin install uncov@llbbl-skills
/plugin install upkeep-rs@llbbl-skills
/plugin install repjan@llbbl-skills
/plugin install semantic-docs@llbbl-skills
/plugin install logan-logger@llbbl-skills
/plugin install pkglock@llbbl-skills
/plugin install dotfiles-manager@llbbl-skills
```

## Plugins

### `upkeep`

Skills from [upkeep](https://github.com/llbbl/upkeep), the JS/TS maintenance CLI.

- `/upkeep:audit` - review security audit output and prioritize remediation
- `/upkeep:deps` - review dependency updates and plan safe upgrades
- `/upkeep:quality` - score repository maintenance and quality signals

### `esm`

Skills for [enum-state-machine](https://github.com/llbbl/esm), the PHP enum and attribute-driven state machine library.

- `/esm:develop` - work on features, bugs, tests, QA, releases, and API reviews for `llbbl/esm`
- `/esm:integrate` - install and integrate `llbbl/enum-state-machine` into a PHP project with enum states, transitions, guards, hooks, and tests

### `uncov`

Skills for [uncov](https://github.com/llbbl/uncov), the low-coverage reporter for Vitest/Istanbul output.

- `/uncov:improve-coverage` - install uncov, verify coverage setup, rank low-coverage files, and turn the report into a practical testing plan

### `upkeep-rs`

Skills for [cargo-upkeep](https://github.com/llbbl/upkeep-rs), the Rust project maintenance CLI.

- `/upkeep-rs:maintain` - install and use `cargo-upkeep` to audit dependencies, review outdated crates, score project health, inspect dependency trees, and plan Rust maintenance work

### `repjan`

Skills for [repjan](https://github.com/llbbl/repjan), the GitHub repository cleanup TUI.

- `/repjan:clean-repos` - audit GitHub repositories, review archive candidates, export marked repos, and safely archive or unarchive repos

### `semantic-docs`

Skills for [semantic-docs](https://github.com/llbbl/semantic-docs), the Astro documentation theme with semantic vector search.

- `/semantic-docs:launch-docs` - launch or adapt a searchable documentation site with Astro content, local/Turso indexing, and deployment checks

### `logan-logger`

Skills for [logan-logger](https://github.com/llbbl/logan-logger-ts), the universal TypeScript logging library.

- `/logan-logger:integrate` - add runtime-appropriate structured logging to Node.js, browser, Deno, Bun, and Next.js projects

### `pkglock`

Skills for [pkglock](https://github.com/llbbl/pkglock-rust), the package-lock registry URL rewriting CLI.

- `/pkglock:protect-lockfile` - switch `package-lock.json` between local npm registries and the public registry, and install safeguards against committing local URLs

### `dotfiles-manager`

Skills for [dotfiles-manager](https://github.com/llbbl/dotfiles-manager), the `dfm` dotfile backup and improvement CLI.

- `/dotfiles-manager:manage-dotfiles` - initialize private backups, track dotfiles safely, avoid secrets, sync state, and review AI-suggested improvements

### `lsm`

Skills for [lsm](https://github.com/llbbl/lsm), the local secrets manager.

- `/lsm:forensics` - investigate local audit logs with `lsm audit query`

## Layout

```text
.claude-plugin/
  marketplace.json
plugins/
  esm/
    .claude-plugin/plugin.json
    skills/
      develop/
        SKILL.md
      integrate/
        SKILL.md
  uncov/
    .claude-plugin/plugin.json
    skills/
      improve-coverage/
        SKILL.md
  upkeep-rs/
    .claude-plugin/plugin.json
    skills/
      maintain/
        SKILL.md
  repjan/
    .claude-plugin/plugin.json
    skills/
      clean-repos/
        SKILL.md
  semantic-docs/
    .claude-plugin/plugin.json
    skills/
      launch-docs/
        SKILL.md
  logan-logger/
    .claude-plugin/plugin.json
    skills/
      integrate/
        SKILL.md
  pkglock/
    .claude-plugin/plugin.json
    skills/
      protect-lockfile/
        SKILL.md
  dotfiles-manager/
    .claude-plugin/plugin.json
    skills/
      manage-dotfiles/
        SKILL.md
  lsm/
    .claude-plugin/plugin.json
    skills/
      forensics/
        SKILL.md
        examples.md
```

`upkeep` is indexed from `llbbl/upkeep` with a remote GitHub plugin source instead of being vendored here.
