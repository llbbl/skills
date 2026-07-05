# llbbl-skills

Claude Code plugin marketplace for llbbl skills.

## Install

```text
/plugin marketplace add llbbl/skills
```

Then install a plugin from the marketplace:

```text
/plugin install lsm@llbbl-skills
```

## Plugins

### `lsm`

Skills for [lsm](https://github.com/llbbl/lsm), the local secrets manager.

- `/lsm:forensics` - investigate local audit logs with `lsm audit query`

## Layout

```text
.claude-plugin/
  marketplace.json
plugins/
  lsm/
    .claude-plugin/plugin.json
    skills/
      forensics/
        SKILL.md
        examples.md
```
