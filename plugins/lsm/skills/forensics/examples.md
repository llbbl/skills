# lsm Forensics Examples

These examples use `lsm audit query --format json` and summarize metadata only.
Never print secret values.

## Last Night

Goal: inspect events in a 23:00-07:00 window.

```bash
lsm audit query --since "2026-07-04T23:00:00-05:00" --until "2026-07-05T07:00:00-05:00" --format json
```

Summarize:

- total events
- apps/envs touched
- read-like events
- parent processes
- non-interactive events
- anything involving production

## Weird Parents

Goal: find parent processes in the target window that were not seen in a prior baseline.

Baseline:

```bash
lsm audit query --since 720h --until "2026-07-04T23:00:00-05:00" --format json
```

Target window:

```bash
lsm audit query --since "2026-07-04T23:00:00-05:00" --until "2026-07-05T07:00:00-05:00" --format json
```

Compare `parent_comm` values from the target window against the baseline and report new names.

## Burst Hunt

Goal: group reads by parent process and flag more than N reads per minute.

```bash
lsm audit query --since 1h --format json
```

Group by:

- minute bucket
- `parent_comm`
- app/env

Flag buckets where the count exceeds the threshold the user gave. If no threshold is given, ask for one before declaring a burst.

## Forensic Window

Goal: inspect every event in a user-specified range.

```bash
lsm audit query --since "<start-rfc3339>" --until "<end-rfc3339>" --format json
```

Report:

- timeline summary
- event counts by type
- apps/envs touched
- parent process counts
- TTY present vs absent
- agent marker present vs absent
- notable outliers

## Non-Interactive Access

Goal: identify events without a TTY and without an agent marker.

```bash
lsm audit query --tty absent --since 24h --format json
```

From the JSON results, filter for events where `agent_marker` is empty, missing, or `none`.

Report:

- timestamp
- app/env
- event type
- parent process
- working directory if available
- count by parent process
