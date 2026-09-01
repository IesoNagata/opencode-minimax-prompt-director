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

## What's inside

| File | Purpose |
|------|---------|
| `opencode_minimax_assistant.md` | **v6 prompt director.** An interactive assistant that builds a MiniMax H3 prompt dynamically, embedding parameters (`[PARAM]`) and lessons (`[LESSON]`) validated in production. It asks the interview in the user's language, offers possible examples, supports a full-plot file by path, and outputs a ready-to-send prompt. |
| `opencode_minimax_prompt_validator.md` | A **generic validation guide** + checklist. Applies to any story/script. Covers modes, opening line, fields, shots, camera, transitions, full-reference sections, speakers, silhouette vs. face, on-screen text, physical continuity, and a per-scene quality checklist. PT: `opencode_minimax_prompt_validator.md.pt-BR`. |
| `opencode_minimax_dramaturgy.md` | The **film-craft layer** (port of the `smixs/visual-skills` dramaturgy, CC BY 4.0): scene formula, three-detail law, three-jobs rule, Murch's Rule of Six, blocking/staging/camera/environment, three-layer storyboard, rhythm, five anchors — mapped onto MiniMax H3 syntax (REF2VA/T2VA) and reconciled with this repo's continuity rules. |
| `opencode_minimax_full_reference_advanced.md` | The **advanced full-reference layer** (derived from `Square-Zero-Labs/video-prompting-skill`, Apache 2.0): reference-type boundaries (`<Subject>`/`<Picture>`/`<Video>`/`<Audio>`), motion-vs-camera relationship, exact multi-performer facial-performance transfer, cross-style transfer, motion-graphics trailers, audio/silence semantics, asset preparation, and a label-compliance audit. |
| `opencode_minimax_creative_enhancement.md` | The **creative-enhancement layer** (derived from `benjiyaya/Minimax-H3-Prompt-AgentSkill`, MIT): mode classification, camera identity (incl. imperfections), visual texture, pacing arc, character detail + visual signature, spatial geography, continuity progression, sound design, per-shot quality bar, storyboard patterns, and common pitfalls. |
| `INSTRUCTIONS.md` | Step-by-step usage — load the assistant, provide the story, set duration &amp; VRAM batch, generate, validate, render one scene at a time (+ troubleshooting). PT: `INSTRUCTIONS.md.pt-BR`. |
| `presentation.html` | A visual slide deck (browser) for the project — how it works, modes, VRAM batching, continuity. PT: `presentation.pt-BR.html`. |
| `examples/` | Ready-to-copy sample scene prompts (EN + PT). |
| `README.md.pt-BR` | Portuguese version of this page. |
| `LICENSE` | CC BY 4.0 license and attribution notice. |

## Quickstart

1. Copy [`opencode_minimax_assistant.md`](opencode_minimax_assistant.md) into your
   agent as the system prompt and answer the interview.
2. Provide your story (inline or by file), pick a duration, and confirm the VRAM
   batch split.
3. After generating, ask your agent to run the
   [`opencode_minimax_prompt_validator.md`](opencode_minimax_prompt_validator.md)
   on the directory that holds your prompts before rendering.

See [`INSTRUCTIONS.md`](INSTRUCTIONS.md) for the full walkthrough.

## Examples

Ready-to-copy scene prompts live in [`examples/`](examples/):

- `examples/cena_03_prompt.example.en.txt` — a chained I2VA scene (English).
- `examples/cena_03_prompt.example.pt-BR.txt` — the same scene (Portuguese).

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

- **English:** `README.md`, `INSTRUCTIONS.md`, `opencode_minimax_prompt_validator.md`,
  `examples/*.en.txt`, `presentation.html`
- **Português (BR):** `README.md.pt-BR`, `INSTRUCTIONS.md.pt-BR`,
  `opencode_minimax_prompt_validator.md.pt-BR`, `examples/*.pt-BR.txt`,
  `presentation.pt-BR.html`, and the `opencode_minimax_assistant.md` interviews you
  in your own language.
