# Streaming Visual Assistant Benchmark — Gemini Baseline Viewer (v3 + v3a)

Interactive single-page viewer for a small-scale Gemini 2.5 Pro baseline on a
**streaming visual-assistant benchmark**: the questions a person wearing smart
glasses would ask their always-on AI assistant — and the spoken responses
Gemini emits when asked to role-play the assistant.

The taxonomy has **10 task types in 3 families**:

- **Proactive (P)** — the assistant speaks up on its own: P1 event detection,
  P2 interval tracking, P3 threshold alert.
- **Reactive (R)** — the person asks, the assistant answers: R1 localization,
  R2 recall, R3 diagnosis & guidance, R4 verification & quality check.
- **Interaction (M)** — multi-instruction dynamics: M1 concurrent tracking,
  M2 instruction update, M3 causal chain.

The viewer shows **two Gemini stages** stacked under each task card:

1. **v3 — Question generation.** Gemini role-plays the person in the
   egocentric video and writes the natural-language question(s) they would
   ask, with the `ask_time` they would ask.
2. **v3a — Answer generation.** For each question, Gemini now role-plays the
   AI visual assistant and emits the spoken `responses` it would deliver,
   each tagged with a `response_time`. Whole video is uploaded; causality is
   enforced by a prompt instruction ("don't reference events after each
   `response_time`"). Each question / sub-instruction carries a
   `can_answer` escape hatch — if the video genuinely doesn't contain the
   signal, the assistant declines instead of confabulating.

Live at **<https://jxb1st.github.io/proactive-video-benchmark-viewer/>**
&nbsp;·&nbsp; Index at [/datasets](https://jxb1st.github.io/datasets/)

## What you see

- **Left** — sticky video player (auto-switches per video section as you scroll).
- **Right** — 10 collapsible task cards per video. Each card shows:
    - the task definition,
    - Gemini's question rationale,
    - the proposed question(s) with `ask_time`,
    - **the Gemini answer block** — rationale + a chronological response list
      (timestamp + spoken text); for M cells, one response list per
      `question_id`,
    - the full per-cell prompt sent for the question stage (collapsed).
  A card may be marked **N/A** when the video has no natural question of
  that kind.
- **Filters** — Proactive / Reactive / Interaction family, dataset,
  show-missing/N/A toggle, free-text search.

## What's in this repo

```
proactive-video-benchmark-viewer/
├── index.html         # single-page viewer, all question + answer data embedded
├── _videos/
│   ├── howto100m/     # 3 x source mp4 (from HowTo100M)
│   └── egoschema/     # 3 x source mp4 (from EgoSchema)
├── README.md
├── .nojekyll
└── .gitignore
```

The viewer is **fully static** — the 82 questions and 171 response
events across 6 videos × 10 task types are inlined into `index.html` at build
time.

## Scale

| | Pilot (this viewer) | Target full set |
| --- | --- | --- |
| Videos | 6 (3 howto100m + 3 egoschema) | 60 (30 + 30) |
| Task types per video | 10 | 10 |
| Total questions (v3) | 82 | ~800 (extrapolated) |
| Total responses (v3a) | 171 | ~1710 |
| Can't-answer declines | 1 | extrapolates |
| Estimated cost | ~$10 | ~$110 |

## Source datasets

- **HowTo100M** — <https://www.di.ens.fr/willow/research/howto100m/>.
- **EgoSchema** — <https://egoschema.github.io/>, derived from
  [Ego4D](https://ego4d-data.org/).

The 6 videos shown here are sampled deterministically (`seed=42`).
