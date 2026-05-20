# Proactive Video Benchmark — Gemini Baseline Viewer (v1)

Interactive single-page viewer for a small-scale Gemini 2.5 Pro baseline on a
new **proactive streaming-video benchmark** organized along two task families:

- **SQ (Single Question, Multiple Answers)** — 4 × 3 grid of cells: trigger
  pattern (A1 discrete recurrence / A2 interval / A3 counting / A4 threshold) ×
  response complexity (B-α fixed / B-β variable / B-γ stateful). 12 cells total.
- **MQ (Multiple Questions, Multiple Answers)** — 4 × 4 grid of cells: logical
  relationship between questions (C1 independent / C2 refining / C3
  compositional / C4 causal) × temporal relationship (D1 sequential / D2
  concurrent / D3 lifecycle / D4 triggered). 16 cells total.

Total **28 cells**. For each cell on each video we ask `gemini-2.5-pro` to
produce a **complete sample**: candidate question(s) plus the ground-truth
response stream (trigger timestamps + canonical response texts), following
the per-cell GT-generation rules embedded in the prompt.

Live at **<https://jxb1st.github.io/proactive-video-benchmark-viewer/>**
&nbsp;·&nbsp; Index at [/datasets](https://jxb1st.github.io/datasets/)

## What you see

- **Left** — sticky video player (auto-switches per video section as you scroll).
- **Right** — 28 collapsible cell cards. Each card shows the cell's two axes +
  definitions, Gemini's rationale, the proposed sample(s):
  - **SQ sample** = 1 question string + a GT response table
    (`t (mm:ss)`, `response_window`, `response_text`).
  - **MQ sample** = 2-4 interrelated questions (each with `issue_time`,
    `activation_time`, `deactivation_time`, `depends_on`) + a GT response
    table with `qid` attribution.
  - The full per-cell prompt sent to Gemini (collapsed by default).
  - Run metadata (timing, model, prompt version).
- **Filters** (top bar) — SQ / MQ family, dataset (howto100m / egoschema),
  show-missing toggle, free-text search.

## What's in this repo

```
proactive-video-benchmark-viewer/
├── index.html         # single-page viewer, all sample data embedded
├── _videos/
│   ├── howto100m/     # 3 × source mp4 (from HowTo100M dataset)
│   └── egoschema/     # 3 × source mp4 (from EgoSchema dataset)
├── README.md
├── .nojekyll
└── .gitignore
```

The viewer is **fully static** — the 168 samples (951 GT response entries
across 6 videos × 28 cells) are inlined into `index.html` at build time.
No data.json or backend.

## Scale

| | Pilot (this viewer) | Target full set |
| --- | --- | --- |
| Videos | 6 (3 howto100m + 3 egoschema) | 60 (30 + 30) |
| Cells per video | 28 | 28 |
| Samples per (video × cell) | 1 | 1 |
| Total samples | 168 | 1,680 |
| GT response entries | 951 | ~9,000 (extrapolated) |
| Wall time @ 5 workers | ~20 min | ~3.5 h |
| Estimated cost | ~$18 | ~$200 |

## Pipeline that produced these samples

1. **Cell taxonomy slice + GT-generation rules** are composed per cell from
   `sq_task_taxonomy.md` and `mq_task_taxonomy.md` of the source repo.
2. **For each (video, cell) pair**, the source repo uploads the video to
   Gemini's Files API (once per video, cached ≤ 48 h), then calls
   `client.models.generate_content(..., response_mime_type="application/json",
   response_schema=...)` with the per-cell prompt. The response is validated
   against a family-specific schema and written to disk as JSON.
3. **The viewer** reads those JSONs and inlines them into a static HTML.

Source pipeline lives in the parent project's `src/` directory (not in this
repo). Key files:

- `src/cells.py` — 28 cell IDs + axis definitions + taxonomy-slice builder.
- `src/prompts.py` — per-axis GT-generation rules + per-family response schema.
- `src/generate.py` — Gemini orchestrator with thread-safe upload-once cache,
  per-pair JSON output, and resumable execution.
- `src/build_viewer.py` — the static-site builder.

## Source datasets

- **HowTo100M** — [official site](https://www.di.ens.fr/willow/research/howto100m/).
- **EgoSchema** — [official site](https://egoschema.github.io/),
  derived from [Ego4D](https://ego4d-data.org/).

The 6 videos shown here are sampled deterministically (`seed=42`) from the
respective dataset pools.
