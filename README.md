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
