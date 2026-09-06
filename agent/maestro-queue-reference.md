# Maestro `_director_queue.json` — Schema Reference & Practical Guide

> **License.** © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — licensed under
> **CC BY 4.0** (https://creativecommons.org/licenses/by/4.0/). Share and adapt freely with
> attribution. See [`LICENSE`](LICENSE).

> **Purpose.** A skill document for the opencode director — two roles in one: (1) an
> **operational reference** for reading the live queue state that **Maestro** writes while
> rendering a music video with **MiniMax H3 ref2va** (track progress, predict render times,
> audit prompts, map MP4s to clips); (2) a **creative + technical corpus source** for the
> prompt-director — mining a queue as ground truth for structure, invariants and inference
> settings (§9).
>
> **Genericity.** This schema is hardware-agnostic. Example values ("value observed") come
> from a production run on the author's workstation — treat them as illustration, never as a
> performance specification. Timing guidance is given as *scaling rules*, not absolute
> numbers.
>
> **Source.** The `_director_queue.json` format documented here is the live queue of
> [`Blizaine/Maestro`](https://github.com/Blizaine/Maestro) (WanGP Non-Commercial
> Evaluation License 1.1; upstream `deepbeepmeep/Wan2GP`). This document describes the
> schema as factual reference and does not redistribute Maestro code or weights.
>
> **Date:** 06/09/2026.

---

## 1. What this file is (and is not)

`_director_queue.json` is the **live state file** Maestro writes to disk while a job runs:

- Path: `app/outputs/_director_queue.json` (Maestro workspace).
- It is **not** a ComfyUI workflow, **not** consumable by comfy-mcp's `run_workflow` /
  `run_template`. Maestro renders natively via `app/models/minimax_h3/ref2va.py` and the
  director services in `app/services/`.
- Its content **changes while rendering** (status, `message`, timestamps). Read it as a
  snapshot, not a static document.
- Each clip's compiled video prompt lives in `prepared_clip_plans[]._director_h3_compiled_prompt`
  — that is the actual prompt sent to the model, and the single most useful field for
  auditing what the model received.

---

## 2. Top-level structure

```json
{
  "version": 1,
  "paused": false,
  "running": true,
  "entries": [ { ... } ]
}
```

| Field | Type | Meaning |
|-------|------|---------|
| `version` | int | Format version (currently 1). |
| `paused` | bool | Queue paused (operator action). |
| `running` | bool | Whether the queue is processing. |
| `entries` | array | Batch jobs in the queue. One entry carries the whole 45-clip plan. |

---

## 3. Entry — job-level fields

| Field | Meaning |
|-------|---------|
| `id` | Entry id (also names the asset dir under `_director_queue_assets/<id>/`). |
| `status` | `pending` / `running` / `completed` / `error`. |
| `message` | **Live progress.** e.g. `"Clip 22/45: Prompt 22/45 - Denoising \| 1m 11s"` — gives the current clip index/total, the stage (Encoding Prompt / Denoising / ...), and elapsed time. |
| `created_at` / `started_at` / `completed_at` | Unix timestamps (float). |
| `pipeline_id` | The pipeline run id; `_director_parent_pipeline_id` points at the director entry that originated it. |
| `error` | Error snapshot on failure, else `null`. |
| `params` | The full director decision state (below). |

---

## 4. `params` — the director's full decision record

### 4.1 Identity & pipeline

| Field | Value observed | Meaning |
|-------|----------------|---------|
| `pipeline_type` | `music_video` | Pipeline driving the render. |
| `auto_mode` | `true` | Director ran without per-clip manual edits. |
| `_director_project_id` | `1916f9b3` | The originating director project. |
| `use_director_v2` | `true` | Uses the V2 planner path (48-step director flow). |
| `seamless` | `false` | No cross-clip seamless blending. |
| `shot_image_guidance` | `auto` | Shot image guidance policy. |

### 4.2 Song & LLM

| Field | Value observed | Meaning |
|-------|----------------|---------|
| `scene_description` | text | The user's brief for the whole video. |
| `bpm` | `161.5` | Track tempo; drives clip `beat_count`/energy segmentation. |
| `lyrics` | array (22) | Lyric sections/lines used for lipsync and verse structure. |
| `target_duration` | `30` | Target clip duration in seconds (budget per clip, not total). |
| `llm_model_id` | any LLM GGUF (e.g. `my-llm-GGUF`) | LLM that planned the clips, run locally. |
| `llm_device` | `cpu` | Where that LLM ran. |

### 4.3 Model choices

| Field | Value observed | Meaning |
|-------|----------------|---------|
| `image_model` | `flux2_klein_9b` | Model for shot/preview images (`image_params`). |
| `video_model` | `minimax_h3_ref2va` | The video model actually rendering. |
| `director_resolution_preset` | `540p` | Chosen tier. |
| `director_aspect_ratio` | `16:9` | Aspect. |
| `fps` | `24` | Framerate. |
| `director_h3_reference_detail` | `max` | Reference conditioning level (matches workflow `minimax_h3_reference_detail`). |

### 4.4 `video_params` — the effective inference settings

| Field | Value observed | Meaning |
|-------|----------------|---------|
| `resolution` | `960x544` | Rendered size. |
| `num_inference_steps` | `8` | Denoising steps (constant across the batch). |
| `video_length` | `124` | Base clip length in frames (`frames_minimum`). |
| `sliding_window_size` | `345` | Context window for the attention pass. |
| `sliding_window_overlap` | `18` | Frame overlap between windows. |
| `override_attention` | `sol` | `sol` = linear softened attention (the `sol` in log lines). |
| `minimax_h3_turbo_mode` | `true` | Fast preset enabled. |
| `minimax_h3_turbo_preset` | `alibaba-pai-ref2va-pdd-8step` | The exact turbo preset (8-step PDD). |
| `skip_steps_multiplier` | `0.08` | Distilled-step cache from this fraction of total steps. |
| `minimax_h3_reference_detail` | `match` | Per-clip reference detail override. |

### 4.5 `prepared_clip_plans` vs `planned_clips` (both length 45)

| Array | Contents |
|-------|----------|
| `planned_clips` | The **creative plan** — per clip: timing, section, energy, subjects, blocking, audio plan, source prompt. |
| `prepared_clip_plans` | The **render-ready plan** — `prepared_clip_plans[i]` = `planned_clips[i]` compiled into a final `video_prompt` / `_director_h3_compiled_prompt` plus the vocal contract and context anchors. |
| `prepared_planned_clips` | Mirror of the prepared plan (same length, same order). |

Rule of thumb: `planned_clips[i]` tells you *what the director meant*; `prepared_clip_plans[i]`
tells you *what the model actually got*.

---

## 5. `planned_clips` — clip schema

Each clip i aligns exactly with `prepared_clip_plans[i]`.

| Field | Type | Meaning |
|-------|------|---------|
| `start` / `end` | int | Beat range in the song (beats). |
| `beat_count` | int | Beats spanned by this clip. |
| `section_label` | str | `intro` / `verse` / `bridge` / `chorus` / `outro`. |
| `energy` | float | Normalized energy 0–1 (drives pacing; `suggested_prompt_hint` summarizes). |
| `suggested_prompt_hint` | str | e.g. `"intro, low energy"`. |
| `duration_frames` | int | Frame count. **This determines render cost**: `243` ≈ 10.1 s, `192` ≈ 8 s, `175` ≈ 7.3 s, `226` ≈ 9.4 s, `141` ≈ 5.9 s. |
| `duration_sec` | int | Seconds (rounds from frames @ 24 fps). |
| `dominant_speaker` | str \| null | Speaker id when the clip has dialogue. |
| `_director_segment_index` / `_director_segment_count` | int | Which segment of the song this clip is. |
| `_director_source_clip_indices` | array | Origin clip(s) when this clip was sliced from a longer plan. |
| `_director_continuity_strategy` | str | `independent` (each clip self-contained, no chain frame) — the observed value; other values may appear for chained runs. |
| `_director_continuity_group` | str | Continuity group id when clips are threaded; `''` when independent. |
| `_director_opening_blocking` / `_director_closing_blocking` | str | Camera/action state at clip start and end. |
| `_director_dialogue_beats` | array | Timed dialogue/vocal beats for the clip. |
| `_director_subjects_on_screen` | array | `{visual_description, position_or_relation}` per subject on screen. |
| `_director_h3_source_prompt` | str | The director's narrative prompt for the clip. |
| `_director_h3_prompt_mode` / `_director_h3_model_family` | str | Both `ref2va` here. |
| `_director_project_context` | str | Cross-clip invariants (character identity, mic model, costume). |
| `_director_environment` | str | Persistent environment restated per clip. |
| `_director_audio_plan` | obj | `{mode: "music_driven", timing_anchor: "audio", lip_sync_critical: bool}`. |

---

## 6. `prepared_clip_plans[i]` — the compiled, render-ready prompt

Extra fields beyond the `planned_clips[i]` set:

| Field | Meaning |
|-------|---------|
| `image_prompt` | Shot-image prompt (`''` when ref2va uses the reference images). |
| `video_prompt` | Final assembled prompt: `subject_definitions:` block + action + camera/audience. |
| `visual_changes` | Array of per-time visual deltas for the clip. |
| `keyframe_prompts` / `window_prompts` / `window_count` | Multi-window breakdown when the clip exceeds the model window. |
| `image_source` | `original` = uses the uploaded reference(s) directly. |
| `_director_vocal_contract` | Fixed rule forcing any audible voice to come from the driving audio only (no generated gibberish). |
| `_director_h3_compiled_prompt` | **The exact prompt string sent to the model** — the field to audit. |
| `_director_speaker_registry` | Mapped speakers for dialogue (empty when no dialogue mapped). |
| `_director_required_context_anchors` | Required reference anchors for the clip. |

---

## 7. `_director_asset_manifest` — the inputs

Under `params._director_asset_manifest`:

| Field | Meaning |
|-------|---------|
| `audio_path` | `{path, serve_path, original_name}` — full mix WAV (here 48 kHz stereo, ~385 s). |
| `audio_vocals_path` | Isolated vocals WAV (same length, 44.1 kHz) used for music_driven sync/lipsync. |
| `minimax_h3_references` | The reference images/videos (the subjects, the environment) plus their labels. |

Files live in `app/outputs/_director_queue_assets/<entry_id>/`.

---

## 8. Practical guide — reading the live render

### 8.1 Track progress

- `entry.message` is the live cursor: `"Clip 22/45: Prompt 22/45 - Denoising | 1m 11s"`.
- Rendered files: `app/outputs/*.mp4`, named `YYYY-MM-DD-HHhMMmSSs_seed<seed>_subject_definitions....mp4`.
  Sorting by name = completion order; the **timestamps are local wall-clock of finish**.
- **Clips done = count of `.mp4`** in the outputs dir (one MP4 per completed clip).

### 8.2 Predict render time

The heavy costs, per clip `i`:

1. **Denoising** ≈ `num_inference_steps × s/step`. `s/step` scales with `duration_frames`
   (packed rows per latent): a 243-frame clip costs roughly 20–80 % more per step than a
   192-frame clip, depending on resolution and model precision. GPU generation speed (VRAM,
   clocks) and model quantization dominate the absolute number.
2. **Post-processing** (full VAE decode + audio + mux): a roughly constant few minutes per
   clip, approximately proportional to frame count.

The frame-count *ratio* is the portable part: measure one clip on your own hardware, then
scale the remaining clips by `duration_frames` for a coarse ETA. Sum
`duration_frames`/`duration_sec` of the remaining clips for a first estimate.

### 8.3 Audit what the model received

For any clip, read `prepared_clip_plans[i]._director_h3_compiled_prompt` and check against
`validator.md`:

- **Continuity**: `_director_continuity_strategy: independent` means each clip is
  self-contained — the compiled prompt must restate environment + subject identity every clip
  (`_director_environment`, `_director_project_context`, `subject_definitions:`), which this
  pipeline does.
- **Lipsync**: `_director_audio_plan.mode = music_driven` + `_director_vocal_contract` — voices
  must come only from `audio_vocals_path`.
- **Subject placement**: `_director_subjects_on_screen` gives declared positions; opening/
  closing blocking declare motion continuity at cut points.

### 8.4 Sanity checks

- `planned_clips` and `prepared_clip_plans` must both be length 45 and ordered identically.
- `duration_frames` must land on the model's frame grid; `frames_minimum` (124) is the floor.
- `energy` should track the song shape (low intro/outro, peaks in chorus) — a flat `energy`
  array with identical `section_label` everywhere suggests the planner collapsed.
- Sum of `duration_sec` ≈ song length (~384.8 s across 45 clips here).

---
 
## 9. Using the queue as a creative + technical source for generation

Beyond tracking a live render, the queue is a **ground-truth corpus**: it contains the full
decision record of a director pass — what it planned (`planned_clips`), what it compiled into
actual prompts (`prepared_clip_plans`), and what inference settings it used. That makes it a
first-class input to the *prompt-director* workflow, not just an ops log.

### 9.1 What to mine, and why

| Source field | Creative value | Use it as... |
|--------------|----------------|--------------|
| `prepared_clip_plans[i]._director_h3_compiled_prompt` | The **exact prompt the model executed** — structure, subject_definitions phrasing, camera/audience language, pacing. | The reference for what a "working" complex clip prompt looks like; copy its *structure*, not its content. |
| `planned_clips[i]_director_subjects_on_screen` | The declarative `visual_description` + `position_or_relation` idiom for placing multiple subjects. | A template for multi-subject composition in your own scenes. |
| `planned_clips[i]._director_environment` | How a persistent environment is restated per clip. | The pattern to keep a location stable across cuts. |
| `planned_clips[i]._director_project_context` | Cross-clip invariants (identity, props, costume) carried verbatim. | A checklist of what *your* prompts must restate every scene. |
| `planned_clips[i]._director_opening_blocking` / `_director_closing_blocking` | Camera/action continuity declared at the exact cut points. | The model for writing seamless hard cuts. |
| `planned_clips[i]._director_audio_plan` + `_director_vocal_contract` | The music_driven sync rule and the "no generated gibberish" contract. | Boilerplate for any music-driven / lipsync clip. |
| `planned_clips[i].section_label` + `.energy` | The song-structure vocabulary (intro/verse/bridge/chorus/outro) with 0–1 energy. | A pacing vocabulary; re-map `energy` to your track's own shape. |
| `video_params` (steps, `override_attention`, turbo preset, resolution) | Working inference settings for ref2va 8-step. | Technical defaults for your own equivalent runs. |
| `duration_frames` | How long each section type actually rendered. | A sanity floor/ceiling for clip length planning. |

### 9.2 The three harvest modes

1. **Structural** — read 3–5 compiled prompts of *different* sections (an `intro`, a `verse`,
   a `chorus`) to absorb how the director organizes subject_definitions → action → camera →
   audience for each energy tier. Then write your own prompt following that skeleton.
2. **Restatement** — reuse `_director_project_context` / `_director_environment` /
   `_director_vocal_contract` almost verbatim: they are copy-paste-safe invariants that hold
   for the whole queue and remain valid for your own project with your own subjects swapped in.
3. **Calibration** — for a *new* song, collect the `energy`/`section_label`/`duration_frame`
   tuples of a prior music_video queue and ask the prompt-director to mirror that pacing.
   A flat `energy` array (all clips the same value) is a red flag to override.

### 9.3 Wiring it into the prompt-director workflow

```
queue (reference corpus)
  → prompt-director.md generates your scene, borrowing structure + invariants from §9.1
  → validator.md checks continuity (§8.3 idioms)
  → render
```

Load `maestro-queue-reference.md` *before* the interview when the brief includes any of:
"render a music video / ref2va batch / 45-clip queue", or when you have a Maestro queue from a
previous run you want to mine. Point the director at the queue path and at the clip indices
that best illustrate the section type you are about to write.

---

## 10. Gotchas

- **Do not edit** `_director_queue.json` while `running: true` — Maestro owns the file.
- The queue JSON does **not** hold the rendered MP4; outputs are written to
  `app/outputs/` and keyed by clip index only via order/naming, not an explicit
  `clip_index` field.
- `video_params.video_length` (124) is the **base** length; per-clip length is
  `planned_clips[i].duration_frames` and can exceed it (up to 243).
- `comfy-mcp` cannot render this plan directly. To render outside Maestro, translate a
  compiled prompt (`prepared_clip_plans[i]._director_h3_compiled_prompt`) into the
  `muse_minimax_h3_director_V1.4.json` workflow slots.
- The queue is **one director pass over one song**: copy its *structure and invariants*, never
  its clip *contents* (subjects/venue/lyrics are that project's own).