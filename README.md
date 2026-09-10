# swe-task-forge · Trajectory Explorer

Two viewers over the same corpus, no build step:

- **Linear explorer** — <https://zeng-weijun.github.io/swe-task-forge-trajectory-explorer/>
- **Two-lane reader** — <https://zeng-weijun.github.io/swe-task-forge-trajectory-explorer/trajectory-dialogue.html>
  (also embedded behind the explorer's *Two-lane reader* button; deep link with `?run=<shortId>`)

83 agent rollouts (`codex:glm-5.3-flash`) over 47 SWE tasks from
`minions_v148_pack_20260911`, rendered in the Empiria Labs *Trajectory Explorer*
UI: reasoning, shell calls, captured stdout, verifier logs, two-arm gate evidence
and full commit-level mining provenance.

| | |
|---|---|
| Tasks | 47 (`*-mine-*`, shape `suite_flip`) |
| Repos | HIPS/autograd, wireservice/csvkit, vitalik/django-ninja, spesmilo/electrum, hugapi/hug, Kaggle/kaggle-cli, lmcinnes/umap |
| Rollouts | 83 · 33 SOLVED · 42 NOT SOLVED · 8 infra failures |
| Tool calls | 1,039 |
| Timeline events | 2,929 (83 user · 393 thinking · 258 assistant · 1,039 tool calls · 1,073 results · 76 verifier logs · 7 system) |
| Source pack | `minions_v148_pack_20260911` (14 GB incl. 7 image tarballs; only the trace/metadata layer is published here) |

## What is in this repo

```
docs/
  index.html                  linear explorer
  trajectory-dialogue.*       two-lane reader (from Zeng-Weijun/trajectory-viewer)
  trajectory-data.js          the export — window.EMPIRIA_RAW_TRAJECTORIES, 4.8 MB
  swe-fork.css                fork-only styling (capture-gap events)
schema/swe-trajectory.schema.json   the field interface, machine-readable
tools/export_explorer_data.py       pack -> schema, re-runnable
SCHEMA.md                  field interface: what is filled, what is reserved
FIELD_COVERAGE.md          what made it out of the pack, and what did not
```

Regenerate from a pack checkout:

```bash
python3 tools/export_explorer_data.py ./out
cp out/trajectory-data.js docs/
```

## The field interface

`SCHEMA.md` + `schema/swe-trajectory.schema.json` define `swe-trajectory/1.0`.
Every field is optional and a `null` renders as an omitted row, so a producer can
start filling a reserved field at any time and it appears with no viewer change.
Fields are tiered **A** (filled, read today), **B** (filled from pack files that
had no field before), **C** (interface written, nothing emits it yet).

The Tier-C list, in order of visualisation value: per-step token usage ·
full stdout per command · structured codex rollout · the agent's final diff ·
per-test results · relay envelope · sampling params · timestamps on every event ·
per-call file mutations · setup durations. Each already has a field waiting.

Each run also carries a `capture` block declaring what its producer recorded, so
a viewer can tell *"this run had no reasoning"* from *"this producer does not
record reasoning"*.

## Read this before trusting a number

`FIELD_COVERAGE.md` is the important document. Short version: the explorer now
shows everything the pack actually contains, but **the pack itself already lost
data at capture time** — the codex transcript is truncated to its last ~59 KB and
`command_result` events store the command but never its output. So only
**395 of 1,039 tool calls (38 %) have any captured stdout**; the rest are shown
with an explicit `output not captured` marker rather than being silently hidden.

## Local preview

```bash
cd docs && python3 -m http.server 8795   # then open http://127.0.0.1:8795/
```

Purely static — no build step, no backend.
