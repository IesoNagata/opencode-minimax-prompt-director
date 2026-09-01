# MiniMax H3 opencode Prompt Director

![License: CC BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-green.svg)
![Model: MiniMax H3](https://img.shields.io/badge/model-MiniMax%20H3-7c6cff.svg)
![Mode: T2VA · I2VA · FL2VA · L2VA · Full-ref](https://img.shields.io/badge/modes-T2VA%20·%20I2VA%20·%20FL2VA%20·%20L2VA%20·%20Full--ref-00d4b0.svg)
![Status: production-validated](https://img.shields.io/badge/status-production--validated-ffd166.svg)
![Docs: EN + PT-BR](https://img.shields.io/badge/docs-EN%20%2B%20PT--BR-ff6b6b.svg)

A universal, production-validated toolkit for creating technically correct
**MiniMax H3** video prompts (T2VA / I2VA / FL2VA / L2VA and **full-reference**),
built around an **opencode** workflow of **chained scenes** with physical
continuity.

> **License:** © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — **CC BY 4.0**.
> Share and adapt freely with attribution. See [`LICENSE`](LICENSE).

## Table of contents

- [What's inside](#whats-inside)
- [Quickstart](#quickstart)
- [Examples](#examples)
- [The chained workflow (core idea)](#the-chained-workflow-core-idea)
- [Suggested usage](#suggested-usage)
- [Provenance](#provenance)
- [Official MiniMax H3 sources referenced](#official-minimax-h3-sources-referenced)
- [Languages](#languages)
- [Resources](#resources)

## What's inside

```
.
├── agent/                       # the prompt-building systems (the core product)
│   ├── prompt-director.md       #   interactive assistant (v6.1)
│   ├── validator.md             #   consistency validator  (+ validator.pt-BR.md)
│   └── craft/                   #   optional craft layers (load under demand)
│       ├── dramaturgy.md
│       ├── creative-enhancement.md
│       └── full-reference-advanced.md
├── docs/                        # step-by-step walkthrough (EN + PT)
│   ├── INSTRUCTIONS.md
│   └── INSTRUCTIONS.pt-BR.md
├── examples/                    # ready-to-copy sample scene prompts (EN + PT)
├── presentations/               # visual slide decks (EN + PT)
├── workflows/                   # runnable ComfyUI workflows (ref stripped)
│   └── minimax-h3-full-reference.scene.json
├── index.html                   # entry redirect (GitHub Pages)
├── README.md                    # this page
├── README.pt-BR.md              # this page (PT)
└── LICENSE
```

| File | Purpose |
|------|---------|
| `agent/prompt-director.md` | **v6.1 prompt director.** An interactive assistant that builds a MiniMax H3 prompt dynamically, embedding parameters (`[PARAM]`), lessons (`[LESSON]`) and the production-detail craft layer (timed audio beats, macro-impact physics, bokeh framing, two-point color temperature). It asks the interview in the user's language, offers possible examples, supports a full-plot file by path, and outputs a ready-to-send prompt. |
| `agent/validator.md` | A **generic validation guide** + checklist. Applies to any story/script. Covers modes, opening line, fields, shots, camera, transitions, full-reference sections, speakers, silhouette vs. face, on-screen text, physical continuity, the production-detail craft checks, and a per-scene quality checklist. PT: `agent/validator.pt-BR.md`. |
| `agent/craft/dramaturgy.md` | The **film-craft layer** (port of the `smixs/visual-skills` dramaturgy, CC BY 4.0): scene formula, three-detail law, three-jobs rule, Murch's Rule of Six, blocking/staging/camera/environment, three-layer storyboard, rhythm, five anchors — mapped onto MiniMax H3 syntax (REF2VA/T2VA) and reconciled with this repo's continuity rules. |
| `agent/craft/full-reference-advanced.md` | The **advanced full-reference layer** (derived from `Square-Zero-Labs/video-prompting-skill`, Apache 2.0): reference-type boundaries (`<Subject>`/`<Picture>`/`<Video>`/`<Audio>`), motion-vs-camera relationship, exact multi-performer facial-performance transfer, cross-style transfer, motion-graphics trailers, audio/silence semantics, asset preparation, and a label-compliance audit. |
| `agent/craft/creative-enhancement.md` | The **creative-enhancement layer** (derived from `benjiyaya/Minimax-H3-Prompt-AgentSkill`, MIT): mode classification, camera identity (incl. imperfections), visual texture, pacing arc, character detail + visual signature, spatial geography, continuity progression, sound design, per-shot quality bar, storyboard patterns, and common pitfalls. |
| `docs/INSTRUCTIONS.md` | Step-by-step usage — load the assistant, provide the story, set duration &amp; VRAM batch, generate, validate, render one scene at a time (+ troubleshooting). PT: `docs/INSTRUCTIONS.pt-BR.md`. |
| `presentations/presentation.en.html` | A visual slide deck (browser) for the project — how it works, modes, VRAM batching, continuity. PT: `presentations/presentation.pt-BR.html`. |
| `examples/` | Ready-to-copy sample scene prompts (EN + PT). |
| `workflows/minimax-h3-full-reference.scene.json` | A stripped **ComfyUI workflow** used for full-reference (REF2VA) scene testing — the `PROMPT` field is blanked and loaded reference images are emptied so it carries no user data. Pair with the prompt built by the assistant for your scene. |
| `README.pt-BR.md` | Portuguese version of this page. |
| `LICENSE` | CC BY 4.0 license and attribution notice. |

## Quickstart

1. Copy [`agent/prompt-director.md`](agent/prompt-director.md) into your
   agent as the system prompt and answer the interview.
2. Provide your story (inline or by file), pick a duration, and confirm the VRAM
   batch split.
3. After generating, ask your agent to run the
   [`agent/validator.md`](agent/validator.md)
   on the directory that holds your prompts before rendering.

See [`docs/INSTRUCTIONS.md`](docs/INSTRUCTIONS.md) for the full walkthrough.

## Examples

Ready-to-copy scene prompts live in [`examples/`](examples/):

- `examples/cena_03_prompt.example.en.txt` — a chained I2VA scene (English).
- `examples/cena_03_prompt.example.pt-BR.txt` — the same scene (Portuguese).
- `examples/cena_prod_detail.example.en.txt` — a production-detail T2VA macro scene
  demonstrating the v6.1 craft layer: timed sound beats, macro/impact physics,
  bokeh-as-framing and two-point color-temperature separation (English).
- `examples/cena_prod_detail.example.pt-BR.txt` — the same scene (Portuguese).

## The chained workflow (core idea)

1. Split the story into **scenes** (e.g. 12–17 scenes, ~12 s each, 16:9, 0.7 MP at
   24 fps as a starting point).
2. For each scene, export the **final frame** — it becomes the **chain frame**
   (`<Picture 2>`) reference of the next scene.
3. Build the prompt with the **assistant**, validate it with the **validator**,
   then render one scene at a time.

> **Note on MP/resolution.** The megapixel/resolution ladder (e.g. `0.7 MP · 1152×640`)
> that appears in scenes is a **suggested reference only** — the same ladder appears in
> downloaded templates (Civitai, HuggingFace, tutorials, YouTube) with varied defaults.
> The user decides the tier in the questionnaire, per preference. It matters **only when
> the workflow is submitted to the queue via comfy-mcp** (it is the queued run's
> requested size); on a manual copy/paste into ComfyUI, the resolution is set in the
> workflow's own dedicated node, not by the scene header.

## Suggested usage

```
assistant (generates a scene prompt)  →  validator (checks it)  →  render
```

## Provenance

Authoring and rules were validated in production on the short film
**"O Fio de Ariadne"**. The documents were refined through hours of interaction
among multiple AI agents — **opencode**, **Gemini**, **ChatGPT** and **Kilo-bin** —
curated by **Ieso Nagata (iesonagata [at] gmail [dot] com)**.

**Watch the film** (generated end-to-end with this toolkit):

<sub>Note: this is the **initial test** — the plot was not written out in detail, which
led to scene/continuity inconsistencies before the enhancements later added to this
toolkit (dramaturgy, continuity supervisor, creative enhancement, advanced
full-reference).</sub>

[![O Fio de Ariadne — a MiniMax H3 short film](https://img.youtube.com/vi/XgA_2ZJTqR4/0.jpg)](https://www.youtube.com/watch?v=XgA_2ZJTqR4)

## Official MiniMax H3 sources referenced

- `docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md` — T2VA / I2VA / FL2VA / L2VA
- `docs/VIDEO_PROMPT_WRITING_GUIDE_ref_en.md` — full-reference (rewrite)

## Languages

This project is available in **English** and **Brazilian Portuguese**:

- **English:** `README.md`, `docs/INSTRUCTIONS.md`, `agent/validator.md`,
  `examples/*.en.txt`, `presentations/presentation.en.html`
- **Português (BR):** `README.pt-BR.md`, `docs/INSTRUCTIONS.pt-BR.md`,
  `agent/validator.pt-BR.md`, `examples/*.pt-BR.txt`,
  `presentations/presentation.pt-BR.html`, and the `agent/prompt-director.md` interviews
  you in your own language.

## Resources

Skill repositories whose content was adapted into this toolkit (with attribution kept):

- [`smixs/visual-skills`](https://github.com/smixs/visual-skills) — **CC BY 4.0**
  source of `agent/craft/dramaturgy.md` (scene formula, Murch's Rule of Six,
  blocking/staging, rhythm, five anchors).
- [`Square-Zero-Labs/video-prompting-skill`](https://github.com/Square-Zero-Labs/video-prompting-skill) — **Apache 2.0**
  source of `agent/craft/full-reference-advanced.md` (reference-type boundaries,
  multi-performer facial transfer, motion-graphics, audio semantics).
- [`benjiyaya/Minimax-H3-Prompt-AgentSkill`](https://github.com/benjiyaya/Minimax-H3-Prompt-AgentSkill) — **MIT**
  source of `agent/craft/creative-enhancement.md` (seven creative dimensions,
  per-shot quality bar, storytelling/action patterns, pitfalls).
