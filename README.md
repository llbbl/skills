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
  lsm/
    .claude-plugin/plugin.json
    skills/
      forensics/
        SKILL.md
        examples.md
```

`upkeep` is indexed from `llbbl/upkeep` with a remote GitHub plugin source instead of being vendored here.
