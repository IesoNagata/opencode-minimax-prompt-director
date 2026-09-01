# Instructions — MiniMax H3 opencode Prompt Director

Step-by-step guide to load and use the **assistant** and **validator**, following
the chained-scenes workflow. English original; a Portuguese version is available
in [`INSTRUCTIONS.pt-BR.md`](INSTRUCTIONS.pt-BR.md).

---

## 0. What you need

- **MiniMax H3** access (API or local ComfyUI with H3 nodes).
- A text editor / agent runtime that can paste a long system prompt
  (**opencode**, any chat that supports system prompts, or a local model).
- The two companion documents:
  - `agent/prompt-director.md` — the prompt director (v6).
  - `agent/validator.md` — the consistency checker.
- Optional craft layer (recommended for anything dramatic/emotional):
  `agent/craft/dramaturgy.md` — the film-craft rules (scene formula, details,
  montage, staging) mapped onto MiniMax H3 syntax. Load it *before* the interview so the
  director writes with dramaturgy in mind.
- Optional advanced layer (for complex full-reference work):
  `agent/craft/full-reference-advanced.md` — reference-type boundaries, exact
  multi-performer performance/speech transfer, cross-style transfer, motion-graphics
  trailers, and audio/silence semantics. Reach for it when assets carry motion,
  choreography, or dubbed/sung performance.
- Optional creative layer (recommended to elevate any brief):
  `agent/craft/creative-enhancement.md` — seven enhancement dimensions (camera
  identity, visual texture, pacing, character/visual signature, spatial geography,
  continuity, sound), the per-shot quality bar, storyboard patterns, and pitfalls.
- Optional: your **story**, either typed in the interview or as a
  **full-plot file path**.

---

## 1. Start the assistant

1. Copy the **entire** content of `agent/prompt-director.md` into your agent
   as the initial system/instruction message (or into the first chat turn).
2. The assistant begins an **interview**. It follows:
   - Your **language** for the conversation.
   - **Output prompt language** (default: English — press Enter to accept).
   - Speaking lines in another language are marked inline with `<d>[Lang]` — no
     global dialogue setting.
3. Answer the questions; choose by **number**, by a snippet, or with your own text.
   A **⭐** marks the recommended default.

---

## 2. Provide the story

Two ways:

- **Inline:** answer the interview questions about plot, characters, environment,
  action, camera, dialogue, audio.
- **By file (recommended for long scripts):** when asked for input type, choose
  **⑦ a full-plot file path** and give the assistant the path to your story file.
  The assistant still collects the *technical* parameters (language, mode,
  duration, resolution, transition style) through the interview.

---

## 3. Set duration and batch size (VRAM)

- If you pick a **fixed total** (e.g. 60 s), the assistant will **ask how many
  seconds your GPU renders per generation** (VRAM batch capacity).
- The total is then split so every scene ≤ that `max_clip`, i.e. each scene is a
  batch that fits your GPU. Example: 60 s ÷ 12 s/batch → **5 × 12 s** scenes.
- Confirm the **batch plan** before the assistant writes the prompts.

---

## 4. Generate and receive the prompts

- The assistant returns **copy-ready** MiniMax H3 prompts.
- For multi-scene: one labeled code block per `CENA` + the **scene connection map**
  + **chain instructions** (export final frame, attach for next).
- Every scene header carries `Technical settings` and `SCENE CONNECTIONS`.
- **MP/resolution in the header is reference, not a hard value:** it is only used when
  the workflow is submitted to the queue via comfy-mcp (it is the queued run's requested
  size). When you copy/paste the prompt manually into ComfyUI, the resolution is set in a
  dedicated node of the workflow — not by the header. The user chooses the tier in the
  questionnaire (see assistant §1.1, §2.6).

---

## 5. Run the validator (do this before rendering)

1. **Ask your agent to process the validator on the path where the generated
   prompts live** (e.g. `cenas-opencode/`) — no manual copy/paste: the agent applies
   `agent/validator.md` over the whole directory.
2. **Go through the quality checklist (§13)** and the rules it references, plus the
   cross-scene chain (§14).
3. **Confirm:** shot timestamps, reference labels and chain frames, speaker IDs,
   dialogue verbatim, transitions, physical continuity, on-screen text.

**Bonus — batch/path validation (§14):** instead of validating one prompt manually,
point the validator at the **directory** that holds your prompts (e.g.
`cenas-opencode/`) and tell it to "validate all scenes". It will:

- discover and sort the `cena_*.txt` files in scene order,
- run the §13 checks on every scene **plus the cross-scene chain** (final frame of
  N → first of N+1, speaker IDs, object states, environment, wardrobe),
- return a **batch report table** (one row per scene) with a PASS / FLAGS / FAILURES
  conclusion.

**Safety:** when fixes are needed, the agent will **first ask where to write them** —
overwrite in place, or a separate output path (recommended) so your original prompts
stay untouched (§14.3). If you pick a separate output path, the agent copies **every**
prompt there — fixed ones rewritten, passing ones copied as-is — and renames each
with a `valid_` prefix (`valid_cena_03_prompt.txt`), so that directory is a uniform,
complete, self-contained set you don't have to assemble from two places.

The whole folder is "ready to render" only when every scene passes and the chain is
continuous across every adjacent pair.

**Rule:** the validator is the independent second pass. Do not skip it because the
assistant already "checked" internally.

---

## 6. Render one scene at a time

1. **Export the final frame** of the rendered scene — it must match the
   `<Picture 2>` declared in the next scene (§6.3).
2. Run the validator on the **next** prompt before continuing.
3. Keep timeline objects consistent (a progress bar / clock never regresses).
4. Assemble title/credits as pure **T2VA** (no image reference).

---

## 7. Handoff / closing

After each delivery the assistant reminds you to run the validator. The end-to-end
flow is:

```
assistant (generates)  →  validator (reviews consistency)  →  render
```

For dramatic or emotional scenes, add the craft layer up front — the
**dramaturgy** reference (`agent/craft/dramaturgy.md`) runs before the
assistant so every shot is built on the scene formula, three-detail law and the
five anchors, then the validator confirms the chain holds:

```
dramaturgy (craft)  →  assistant (generates)  →  validator (consistency)  →  render
```

Repeat per scene. Validate, render, export the chain frame, and move on.

---

## Troubleshooting

| Symptom | Cause / fix |
|---------|-------------|
| Duplicated / teleported character | Face connected while the character is only a distant silhouette — use the `IDENTITY (silhouette)` block instead (§9). |
| Visible splice at every cut | Uniform fade in every transition (§6.1) — add variety via a planned transition map (§6.2). |
| Out-of-memory during render | Per-scene duration exceeds the GPU's `max_clip` — split into more, shorter scenes (§4 / §3.3). |
| "Jump"/loop at a scene join | Exported final frame ≠ `<Picture 2>` of the next scene — re-export the last non-black frame (§6.3). |
| Background/color drift | Environment not restated per scene — use the connection map to persist objects/lighting (§11). |

---

### License

© 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — **CC BY 4.0**. See [`LICENSE`](LICENSE).
