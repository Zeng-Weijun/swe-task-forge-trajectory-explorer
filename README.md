# swe-task-forge · Trajectory Explorer

Live page: **https://zeng-weijun.github.io/swe-task-forge-trajectory-explorer/**

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
docs/                      GitHub Pages site (open index.html)
  trajectory-data.js       the export — window.EMPIRIA_RAW_TRAJECTORIES, 4.2 MB
  trajectory-explorer.js   Empiria UI, patched (see NOTICE.md)
tools/export_explorer_data.py   pack -> explorer schema, re-runnable
FIELD_COVERAGE.md          what made it out of the pack, and what did not
```

Regenerate from a pack checkout:

```bash
python3 tools/export_explorer_data.py ./out
cp out/trajectory-data.js docs/
```

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
