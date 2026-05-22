# Streaming Visual Assistant Benchmark — Gemini Baseline Viewer (v3)

Interactive single-page viewer for a small-scale Gemini 2.5 Pro baseline on a
**streaming visual-assistant benchmark**: the questions a person wearing smart
glasses would ask their always-on AI assistant.

The taxonomy has **10 task types in 3 families**:

- **Proactive (P)** — the assistant speaks up on its own: P1 event detection,
  P2 interval tracking, P3 threshold alert.
- **Reactive (R)** — the person asks, the assistant answers: R1 localization,
  R2 recall, R3 diagnosis & guidance, R4 verification & quality check.
- **Interaction (M)** — multi-instruction dynamics: M1 concurrent tracking,
  M2 instruction update, M3 causal chain.

For each (video, task type) Gemini 2.5 Pro role-plays the person in the
egocentric video and writes the natural-language question(s) they would ask,
with the timestamp they would ask. This stage generates **questions only** —
ground-truth answers are a separate downstream stage.

Live at **<https://jxb1st.github.io/proactive-video-benchmark-viewer/>**
&nbsp;·&nbsp; Index at [/datasets](https://jxb1st.github.io/datasets/)

## What you see

- **Left** — sticky video player (auto-switches per video section as you scroll).
- **Right** — 10 collapsible task cards per video. Each shows the task
  definition, Gemini's rationale, the proposed question(s) with `ask_time`,
  and the full per-cell prompt (collapsed). A card may be marked **N/A** when
  the video has no natural question of that kind.
- **Filters** — Proactive / Reactive / Interaction family, dataset,
  show-missing/N/A toggle, free-text search.

## What's in this repo

```
proactive-video-benchmark-viewer/
├── index.html         # single-page viewer, all question data embedded
├── _videos/
│   ├── howto100m/     # 3 x source mp4 (from HowTo100M)
│   └── egoschema/     # 3 x source mp4 (from EgoSchema)
├── README.md
├── .nojekyll
└── .gitignore
```

The viewer is **fully static** — the 82 questions across 6 videos x 10 task
types are inlined into `index.html` at build time.

## Scale

| | Pilot (this viewer) | Target full set |
| --- | --- | --- |
| Videos | 6 (3 howto100m + 3 egoschema) | 60 (30 + 30) |
| Task types per video | 10 | 10 |
| Total questions | 82 | ~800 (extrapolated) |
| Estimated cost | ~$6 | ~$55 |

## Source datasets

- **HowTo100M** — <https://www.di.ens.fr/willow/research/howto100m/>.
- **EgoSchema** — <https://egoschema.github.io/>, derived from
  [Ego4D](https://ego4d-data.org/).

The 6 videos shown here are sampled deterministically (`seed=42`).
