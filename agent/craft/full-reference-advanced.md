# MiniMax H3 Prompt Director — Advanced Full-Reference Layer

> **Purpose.** This file extends the full-reference (REF2VA) coverage of the toolkit with
> the advanced material that the base documents do not reach: precise **reference-type
> boundaries** (`<Subject>` vs `<Picture>` vs `<Video>` vs `<Audio>`), **exact
> performer/facial-performance transfer** (multi-performer ownership, audio mapping,
> cross-style preservation), **motion-graphics trailers**, refined **audio/silence
> semantics**, asset-preparation guidance, and a **label-compliance audit**. It composes
> with the existing files: `agent/prompt-director.md` (structure + interview),
> `agent/validator.md` (consistency), and
> `agent/craft/dramaturgy.md` (film craft).

> **License & attribution.** This document is a **derivative** of the MiniMax H3
> prompting reference in **[Square-Zero-Labs / video-prompting-skill](https://github.com/Square-Zero-Labs/video-prompting-skill)**,
> `video-prompting/references/models/minimax-h3/prompting.md`, which is licensed under
> the **Apache License, Version 2.0**. This work adapts and reorganizes that guidance
> onto this repository's format and continuity rules. Modified from the original.
> Base document © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — CC BY 4.0.
> See `NOTICE`/`LICENSE` in the upstream repo for the Apache attribution notices.

---

## Contents

1. Reference-type boundaries (`<Subject>` · `<Picture>` · `<Video>` · `<Audio>`)
2. Motion vs camera relationship (when to use `<Subject>` vs `<Video>`)
3. Exact performer and facial-performance transfer (multi-performer)
4. Cross-style performance transfer (stylized target / photoreal source)
5. Motion-graphics trailers
6. Audio & silence semantics
7. Asset preparation guidance (external note, not prompt prose)
8. Label-compliance audit (final check)
9. How this maps onto the repo's existing workflow

---

## 1. Reference-type boundaries

Know which label each asset earns. Getting this wrong leaks content into the wrong
layer and breaks retention tracking.

- **`<Subject N>`** — **reusable visible content**: a person, object, environment,
  clothing, style, action, expression, pose, performer/object **motion**, or
  choreography. Cite its source picture/video **in its definition**.
- **`<Picture N>`** — a **concrete frame**: first frame, keyframe, last frame, edited
  keyframe, composition anchor, or storyboard reference. **Do not define a standalone
  picture when it only supplies a subject** — that is a `<Subject>`, not a `<Picture>`.
- **`<Video N>`** — a **whole source video** used for editing, continuation, or
  temporal/editing structure. **Visible content extracted from it remains a
  `<Subject N>`** — e.g. "the dancer whose appearance comes from `<Picture 1>` and
  whose choreography comes from `<Video 1>`."
- **`<Audio N>`** — a **copied or referenced audio signal**: soundtrack, voice timbre,
  dialogue, music style, beat, or sound texture. **Do not create one merely because a
  reference video contains audio.**

Rules:
- Give each separately tracked item **one definition line**; keep labels stable across
  all sections.
- If audio maps to a target speaker, reuse that speaker's eventual ID in the
  definition, e.g. `<Audio 1> is the voice-timbre reference for <Subject 1> (S1).`

---

## 2. Motion vs camera — `<Subject>` or `<Video>`?

This is the boundary the base guide does not draw. Get it wrong and the model
under-transfers the movement or doubles the camera path.

- **Performer / object motion or choreography** (to reproduce, adapt, or transfer) →
  represent it in a **`<Subject N>`** definition sourced from the video. Incorporate it
  into the target performer/object's subject definition when it is an attribute of that
  subject; give it a **separate subject label** only when it must be tracked or
  transferred independently.
- **Whole-video temporal structure** (editing, continuation, camera movement, cuts,
  editing rhythm, shot order) → use **`<Video N>`**.
- **Referenced camera motion** → a `<Video N>` relationship, **not** a
  motion/choreography subject.
- **Do not use `<Video N>` as a substitute for referenced performer/object motion.**

Worked pattern:

```
<Subject 1> is the dancer whose appearance comes from <Picture 1> and whose choreography comes from <Video 1>.
<Video 1> provides the target video's camera movement, cuts, and pacing structure.
```

In `detailed_description`, cite the `<Subject N>` where the referenced movement takes
effect and **let the reference supply the actual timing, sequence, rhythm, body
mechanics, spatial path, and performer interactions** — do not redundantly reconstruct
the choreography beat by beat. Describe only intentional adaptations, target-subject
mapping, or synchronization constraints. For camera from `<Video N>`, cite it where the
move takes effect and describe the target subject/scene/composition and deliberate
adaptations — do not restate the camera move unless the user asks to change it.

---

## 3. Exact performer and facial-performance transfer

Use these rules when the goal is one or more target subjects to follow reference-video
performers as exactly as practical — dialogue, singing, lip sync, facial acting,
reactions, subtle upper-body performance.

### 3.1 Treat the reference video as performance, not identity

- Represent each source performer's reusable performance as a **`<Subject N>`** sourced
  from the reference video.
- That performance subject is **authoritative** for: timing, sequence, rhythm,
  intensity, lip articulation, jaw movement, facial expression, eye/eyelid behavior,
  gaze, blinking, eyebrow motion, breathing-related movement, head motion, and requested
  body micro-gestures.
- **Transfer performance attributes only.** Do not inherit the source performer's
  identity, clothing, hairstyle, body appearance, environment, or rendering style unless
  explicitly requested.
- Keep the target subject's **visual identity in its own `<Subject N>`** sourced from
  the target picture.
- Do not reconstruct the source performance beat by beat — describe the mapping,
  adaptation, synchronization constraints, and intentional exclusions.

### 3.2 Lock performer ownership (multi-performer)

Define **one stable** source-performer → performance-subject → target-subject mapping,
invariant for the whole video:

```
SOURCE PERFORMER A → <performance Subject A> → <target Subject A> (S1)
SOURCE PERFORMER B → <performance Subject B> → <target Subject B> (S2)
```

When screen position is the intentional, stable discriminator:

```
LEFT source performer → <Subject 3> → LEFT target <Subject 1> (S1)
RIGHT source performer → <Subject 4> → RIGHT target <Subject 2> (S2)
```

- Ownership is **unchanged** across speaking turns, pauses, listening reactions,
  interruptions, head turns, expression changes, overlapping dialogue.
- When mapped source A speaks, **only** mapped target A lip-syncs that speech; target B
  reproduces only B's concurrent listening/reaction performance. Symmetric for B.
- Both targets vocalize simultaneously **only** when their mapped sources actually
  overlap.
- When a mapped source is silent, prevent speech-like mouth animation while keeping its
  nonverbal reactions and micro-gestures.
- **Explicitly prohibit** cross-mapping, speaker swapping, and one target mirroring
  another target's speech animation when identity confusion is a material risk.

### 3.3 Map audio ownership explicitly

When source audio must be preserved, define **copied audio separately** from visual
performance:

- Single performer: map the copied singing/dialogue audio directly to that target
  speaker ID.
- Multiple performers: use separately tracked audio references, e.g. `<Audio 1>` for
  S1 and `<Audio 2>` for S2.
- State that each audio reference belongs **exclusively** to its mapped speaker.
- Preserve source timing, pauses, breaths, overlaps, cadence, pronunciation, pitch or
  melody (for singing), and vocal inflection when intended to be copied.
- **Do not invent dialogue from a referenced recording.** If exact words are not
  supplied as text, refer to the copied source audio rather than guessing a transcript.

### 3.4 Restrict who may vocalize

If the frame contains extra characters who are not intended performers:

- Identify the subjects allowed to vocalize; all other visible characters remain
  **non-vocal** unless they have their own mapped reference performer.
- Background subjects must not inherit the primary performer's lip sync,
  facial-performance track, or singing motion.
- For a single singer, say explicitly the singer is the **only visible source** of the
  referenced vocal performance.

### 3.5 Prioritize exact micro-performance

Include the micro-performance attributes that matter instead of generic "lip sync":

- phoneme-driven mouth shapes and coarticulation
- jaw motion and mouth opening/closure; lip compression, rounding, spreading, sustained
  vowel shapes; teeth/tongue only when naturally present
- gaze shifts, eyelid motion, full and partial blinks
- eyebrow motion and cheek tension; tiny smiles, grimaces, expression transitions
- breath-linked facial and posture changes; small head tilts, nods, rotations,
  restrained upper-body adjustments

For singing: preserve note timing, phrasing, breath placement, sustained-note mouth
shapes, vibrato-related facial performance when visible, expressive eye behavior. Do not
convert sustained notes into repeated generic talking cycles.

---

## 4. Cross-style performance transfer (stylized target)

When the target image is illustrated/animated/painted/strongly stylized but the
performance reference uses a different visual medium:

- Define the target visual style concretely: medium, line treatment, stylized facial
  construction, shading method, texture abstraction, hair rendering, palette,
  materials, lighting treatment, environment rendering.
- Mark it `fully_preserved` in `retention_analysis` when it must carry the whole video.
- State the reference video supplies **performance only** and must not transfer its
  source-performer appearance or rendering style.
- When style drift is likely, explicitly exclude incompatible traits (photoreal skin,
  pores, photographic hair strands, live-action lighting, etc.).
- Require transferred facial motion to stay **native to the target style** (stable
  illustrated mouths/eyes/contours rather than drifting photoreal).
- Reassert the style before `[Shot 1]` and once inside the shot when cross-style
  transfer is central; avoid repeating a long style block throughout.

Useful style-lock pattern:

```
<Subject N> is the visual style established by <Picture 1>, including its medium, line treatment, stylized facial construction, shading method, palette, material treatment, and environmental rendering.

<Subject N>: fully_preserved — Preserve this visual language in every frame. <Video 1> supplies performance only and must not transfer its photographic rendering, source-performer appearance, skin treatment, hair treatment, lighting style, or other source-medium traits.
```

---

## 5. Motion-graphics trailers

For fast motion-graphics trailers, treat the prompt as a compact audiovisual style
bible followed by a precisely timed edit:

- Establish the **global design system** at the start of `[Shot 1]`: animation medium,
  line and shape language, rendering treatment, palette (exact color values when
  supplied), tonal qualities, exclusions. Keep these rules active across every shot
  instead of restating the whole style block.
- Define the hero / product / mascot / brand device with stable identifying traits
  before the action; reassert only traits at risk during silhouettes, duplicates,
  transformations, extreme poses, or rapid cuts.
- Give each shot **one legible graphic beat** (entrance, transformation, panel montage,
  emblem reveal, kinetic-type composition, final lockup). Describe incoming
  composition → subject action → graphic response → camera behavior, in that order.
- **Timestamp every cut** and synchronize shape snaps, wipes, typography, impacts,
  lens/logo reveals, and musical accents to the visual action. A regular cut cadence is
  acceptable when it is an intentional part of the trailer's rhythm.
- Treat visible typography as a **whitelist**: quote the exact allowed letters/words and
  explicitly forbid all other readable text when brand fidelity requires it.
- Describe ambiguous motifs by unmistakable visual construction — e.g. define a lens
  through curved glass, concentric optics and aperture blades, then exclude reticles or
  targeting marks when they'd misread.
- Keep `overall_soundscape` focused on shot-synchronized whooshes, impacts, mechanical
  cues, character sounds, transition effects. Use `non_diegetic_music` for the trailer's
  rhythmic engine, instrumentation, build, and final hit.

---

## 6. Audio & silence semantics

Refine the plain `N/A` rule with layer awareness:

- **Complete silence** (explicit: "no audio of any kind") → `overall_soundscape: N/A`
  **and** `non_diegetic_music: N/A`; add no ambience/dialogue/SFX/music anywhere.
- **No music only** → keep requested ambience and physical sounds in
  `overall_soundscape`, `non_diegetic_music: N/A`.
- Treat "no music" / "no ambience" / "no sound effects" as **layer-specific**, not as
  complete silence.
- `overall_soundscape`: one continuous paragraph of 1–4 sentences: ambience, physical
  action sounds, non-verbal human sounds. Do **not** repeat dialogue/singing/diegetic
  music. `N/A` only for complete silence.
- `non_diegetic_music`: 1–3 sentences, audience-only score via instrumentation,
  tempo/rhythm, dynamic development. Music audible to characters goes in the timeline.
  `N/A` when there is no audience-only score.
- State copied/referenced audio in the **matching audible layer**: ambience/effects in
  `overall_soundscape`, audience-only score in `non_diegetic_music`, dialogue and
  shot-synchronized audio in `detailed_description`.

For words heard in a directly reused soundtrack/BGM without a visible vocalizing
character, use `<Audio N>` without a speaker ID. Write `[unclear]` rather than guessing
unintelligible source speech.

---

## 7. Asset preparation guidance

A **standalone note for the operator**, not prompt prose. Do not insert reference-quality
commentary into the prompt, and do not reject a usable asset; mention it only when asked
how to improve inputs or a clear mismatch will likely prevent the transfer:

- Prefer clean, well-lit reference footage with **one unambiguous action** when motion,
  choreography, or performance transfer matters.
- Match framing to the transfer: full-body source + full-body target for body motion;
  tight facial views for expression/acting transfer.
- Prefer source and target subjects with compatible body plans and proportions; expect
  drift when transferring precise motion between substantially different shapes.

---

## 8. Label-compliance audit

Before delivering any full-reference prompt:

- Preserve the exact schema, field order, line breaks, labels, and required blank lines.
- Keep model name/version, resolution, aspect ratio, and API/control parameter names
  **out of prompt prose** (they may live in the `Technical settings` header, which is
  not the model-bound body).
- Include duration only in required alignment instructions and shot timestamps.
- Shot times strictly increasing and within the duration.
- Keyframe paths actually begin from / connect to / land on the supplied frame as the
  selected mode requires.
- Every separately tracked label: **exactly one** definition, **one**
  `retention_analysis` entry, **≥1 use** where its role takes effect. Remove inactive
  labels; resolve conflicting roles without changing a label's meaning between sections.
- A source-only picture/video cited inside another item's definition needs **no**
  standalone definition or retention entry — do not mistake that compact citation for an
  unresolved label.
- Exact performer transfer: one stable target mapping per source performer; speaker
  ownership never swaps; silent targets show no speech-like mouth motion unless their
  mapped source is actually speaking.
- Cross-style transfer: target medium explicitly preserved; source video contributes
  performance, not unintended identity/rendering traits.
- Dialogue, lyrics, and visible text preserved exactly in their original language; all
  other content in English.

---

## 9. Mapping onto this repo's workflow

Where each section slots into the existing chain:

| This layer (§) | Composes with | Typical use |
|---|---|---|
| Reference boundaries (§1) | `subject_definitions` (§7.1 of validator) | any full-reference scene |
| Motion vs camera (§2) | `subject_definitions` + `detailed_description` | dancing, choreography, camera-from-video |
| Performance transfer (§3) | speaker IDs `(Sx)` + dialogue (§8 of validator) | dubbing, lip-sync, multi-actor dialogue |
| Cross-style (§4) | `IDENTITY (silhouette)` + style line before `[Shot 1]` | illustrated/animated targets |
| Motion graphics (§5) | T2VA `integrated_multimodal_description` | trailers, kinetic-type, brand spots |
| Audio/silence (§6) | `overall_soundscape` / `non_diegetic_music` | any audio policy |
| Asset prep (§7) | pre-render asset selection | choosing references |
| Compliance audit (§8) | §13 checklist of the validator | final pass before render |

The chain rule still holds: the exported last non-black frame of scene `N` is `<Picture 2>`
of scene `N+1`, fade-as-cut unless a varied map was explicitly approved, and continuity
(progress indicators, wardrobe, direction) never regresses.
