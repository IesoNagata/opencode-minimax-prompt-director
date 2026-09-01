# MiniMax H3 Prompt Creation & Formatting Guide — opencode validator

> **License.** © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — licensed under
> **CC BY 4.0** (https://creativecommons.org/licenses/by/4.0/). Share and adapt
> freely with attribution. See `LICENSE`.

> **Authorship & context.** This document was produced and refined over many hours
> of interaction among multiple AI agents — **opencode**, **Gemini**, **ChatGPT** and
> **Kilo-bin** — curated by **Ieso Nagata (iesonagata [at] gmail [dot] com)**. It consolidates
> the MiniMax H3 prompt format and the opencode continuity rules, and now also the
> production-detail craft layer (timed audio beats, macro-impact physics, bokeh
> framing, two-point color-temperature separation) carried by the assistant v6.1.
> **Date:** 2026-08-30.

This guide consolidates the official MiniMax instructions for **MiniMax-H3**
(T2VA / I2VA / FL2VA / L2VA, including the **full-reference** modes), together with
the opencode workflow authoring adaptations: **chained scenes**, exporting the
**final frame as the reference for the next scene**, and the **transition**
strategy (fade / hard cut / varied/planned map).

It applies to **any project/story**, not to one specific script.

**Official sources (MiniMax H3):**
- `docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md` — T2VA / I2VA / FL2VA / L2VA modes
- `docs/VIDEO_PROMPT_WRITING_GUIDE_ref_en.md` — full-reference (rewrite)

---

## 1. Overview of generation modes

| Mode | What it does | Image reference | Main field |
|------|--------------|-----------------|------------|
| **T2VA** | Text → video (establishing screen / titles / credits) | none | `integrated_multimodal_description` |
| **I2VA** | 1st image + text → video advancing from it | `<Picture 1>` (first frame) | `integrated_multimodal_description` |
| **FL2VA** | 1st + last image → motion between them | `<Picture 1>` and `<Picture 2>` | — |
| **L2VA** | Last image → infer previous state and converge | `<Picture 1>` (last frame) | — |
| **Full-reference** | Multiple references (Picture/Subject/Video/Audio) | several | `detailed_description` + 6 sections |

Practical rule: a chained film project is, in practice, a series of **I2VA in
full-reference mode**, where scene `N+1` receives as reference the **final frame
exported from scene `N`**.

---

## 2. Instruction opening (mandatory first line)

The image-alignment instruction must be the **first line of the prompt**, followed
by a blank line before the main fields.

- **I2VA** (initial image):
  ```
  For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
  ```
- **FL2VA** (start + end):
  ```
  How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
  ```
- **L2VA** (end), with `S.SS` = total duration in two decimals:
  ```
  How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
  ```
- **Full-reference**: no such line at the top; anchors enter via
  `subject_definitions` + `retention_analysis` + `detailed_description`.

---

## 3. Main (shared) fields

### 3.1 `integrated_multimodal_description` (T2VA / I2VA)
Describes visuals, actions, shots, speakers, dialogue and diegetic audio over time.
Recommended structure:
**first-frame anchor → action onset → continuous development → result/reaction**.

Open `[Shot 1]` declaring the **style** and initial composition, right after a
style sentence:

```
The target video is in a cinematic, realistic live-action style with soft lighting.
[Shot 1] ...
[Shot 2] At 00:03.500, the shot cuts to ...
```

### 3.2 `detailed_description` (full-reference)
The main body of the full-reference rewrite: describes scene by scene in playback
order and **inserts the reference labels** where they apply
(`<Subject N>`, `<Picture N>`, `<Video N>`, `<Audio N>`).
Style established **before** `[Shot 1]`:

```
The target video is in a cinematic, literary music-video style with soft lighting and a slightly desaturated color palette.
[Shot 1] The scene opens ...
[Shot 2] At 00:09.000, the shot cuts to ...
```

Generation length: typically **350–500 words**; dialogue-dense content prioritizes
fitting the full spoken line over the word count.

### 3.3 `overall_soundscape` (1–4 sentences)
Summary of the atmosphere and physical sounds (wind, rain, footsteps, cloth,
breathing). **Do not** repeat dialogue, singing, or music. Use `N/A` only for
complete silence.

### 3.4 `non_diegetic_music` (1–3 sentences)
Music only the audience hears. Focus on instrumentation, tempo, rhythm and
dynamics — **avoid** abstract emotion adjectives. Use `N/A` if none.

### 3.5 `timed_sound_beats` (optional production-detail layer)
An optional synchronized-and-mixed SFX layer for sound design whose meaning lives in
**timing or relative level** (slow-motion shots, precision SFX, Foley-heavy macro work).
Consistent with the assistant's Part 10-A: one beat per line, instants either
two-decimal seconds or event labels, monotonically forward, optional levels in dB.
It belongs **alongside** `overall_soundscape` (continuous layer) and `non_diegetic_music`
(score) — never replacing them, and never repeating dialogue or music. When the field
is absent, `overall_soundscape` carries the full audio load as before.

```
timed_sound_beats:
00.000–contact: faint room/stadium ambience at a low level
contact: deep resonant low thud
deformation: subtle material creak under compression
release: soft whoosh of the object moving through air
ejecta: faint organic crackle of debris lifting
```

---

## 4. Shots and cuts (`[Shot N]`)

- **`[Shot 1]`** has no timestamp.
- Later shots use `[Shot N] At MM:SS.mmm, ...` with **strictly increasing** times
  **within the clip duration**:
  ```
  [Shot 2] At 00:03.500, the camera cuts to ...
  ```
- Common cut verbs: `the camera cuts to`, `the shot cuts to`,
  `the shot transitions to`, `the shot changes to`, `the shot switches to`.
- Fade, cross-dissolve or wipe may be used **when explicitly requested**
  (see §6 Transitions).
- A cut must introduce new information (subject, space, state, point of view, or
  time). If only distance/angle changes, **prefer camera movement**.

---

## 5. Camera: `[Motion Type] + Amplitude + Speed`

Write the movement as natural English action, with amplitude and speed only when
they make sense (medium amplitude and normal speed can be omitted).

| Dimension | Expressions |
|-----------|-------------|
| Type | `Zoom In/Out`, `Push In/Pull Out`, `Pan Left/Right`, `Truck Left/Right`, `Tilt Up/Down`, `Pedestal Up/Down`, `Arc Shot`, `Tracking Shot`, `Static Shot`, `Shake Slightly/Strongly`, `POV`, `Roll Clockwise/Counterclockwise` |
| Amplitude | `with small amplitude`, `with large amplitude` |
| Speed | `at slow speed`, `at fast speed` |

Examples:
```
The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.
The camera pans right with large amplitude at fast speed, revealing the open doorway.
```

---

## 6. Transitions between scenes — GENERAL STRATEGY

One of the most important points of the chained workflow. The **last frame of a
scene is exported and becomes the next scene's reference** (the chain frame). The
**transition type** chosen defines whether the splices become visible.

### 6.1 Option A — Fade (used in this version)
- Each scene **ends in fade-out to black** and the next **starts with fade-in**.
- The character is already repositioned in the new location after the fade.
- Advantage: signals passage of time/space, removes teleport illusion, covers
  environment changes without crossing walls.
- **Drawback (recorded observation):** fade in every transition makes the
  **splices of the total film easily perceptible** — the viewer sees exactly where
  each scene was joined.

### 6.2 Option B — Planned/varied transition map (recommended for fine-tuning)
Alternate transition types across the film to **avoid revealing the splice
pattern**. Examples:
- **Freeze frame → hard cut** to a frame already containing the start of the next
  action (good for rhythm and motion continuity).
- **Fade** only at real **environment changes** (interior→exterior,
  floor→floor), not in every scene.
- **Match cut / cut-on-motion** (continue a movement across the cut).
- **Dry cut** when the next scene continues in spatial/temporal continuity.

Rule: the "randomness" is **planned** — choose the turnaround type per scene in a
**transition map** defined before rendering, and note the exported final frame of
each one.

### 6.3 Inflexible chain-frame rule
Whatever the transition, the **lived final frame of scene `N`** must be the
`<Picture 2>` / chain frame of scene `N+1`. Do not end a scene on a frame that
does not match the next scene's reference (this causes "jump"/loop). With fade,
export the **last non-black frame** (before the final fade), never pure black.

---

## 7. Full-reference mode — the 6 sections

Full-reference mode organizes output in the following sections, **in this order**:

```
subject_definitions:
summary:
retention_analysis:
detailed_description:
overall_soundscape:
non_diegetic_music:
```

### 7.1 `subject_definitions`
Defines each referenced content and its label. Label types:

| Label | Meaning |
|-------|---------|
| `<Subject N>` | Reusable visible content (person, object, environment, style, pose) |
| `<Picture N>` | Image used as concrete frame / framing anchor |
| `<Video N>` | Source video (edits, continuations, temporal structure) |
| `<Audio N>` | Audio that is copied or referenced |

Each item on its own line, explaining what the label denotes, its role, and the
characteristics to follow.

### 7.2 `summary`
A short paragraph with a bracketed **task-type** prefix:
`[keyframe completion]`, `[reference generation]`, `[video editing]`,
`[video continuation]`, `[audio reuse]`, `[audio reference]` — combinable with `+`.

### 7.3 `retention_analysis`
One line per label, with fixed English markers:
- Visual: `fully_preserved`, `partially_preserved`, `attribute_transfer`,
  `weak_reference`.
- Audio: `fully_copy`, `partially_copy`, `reference`, `weak_reference`.

### 7.4 `detailed_description`
See §3.2. Insert reference labels at the first important appearance and reuse them
afterward. Natural reference story:
```
[Shot 1] ... the shot begins from <Picture 1> ...
[Shot 2] At 00:09.000, the shot ends on <Picture 2> ...
```

### 7.5 `overall_soundscape` and `non_diegetic_music`
See §3.3 and §3.4. Reference audio: report **copy** in `overall_soundscape`
(ambience/effects) and in `non_diegetic_music` (audience-only score).

---

## 8. Speakers, dialogue and IDs `(Sx)`

- Assign stable IDs `(S1)`, `(S2)`, ... to speakers; keep the same ID across
  scenes and shots. Group speech uses compound: `(S1,S2)`.
- The identifying phrase, the ID, the action and the delivery go **outside** `<d>`.
  Inside `<d>` only the language and the spoken content, **verbatim** (no
  translation).
  ```
  The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>
  ```
- **Voice-over:** use the exact phrase `says in an off-screen voiceover` and, right
  after the `<d>` block, state that the on-screen character's lips remain closed:
  ```
  The man (S1) says in an off-screen voiceover: <d>[English] ... </d> while his lips remain completely closed.
  ```
- Dialogue crossing a cut: use `<scenetrans>` and say the audio continues. Speech
  truncated by the end of the clip: use `<cutoff>`.

### 8.1 Global speaker map (recommendation)
In projects with narrator + character, define **global** consistent IDs:
- `(S1)` = off-screen narrator (lips closed, voice-over).
- `(S2)` = on-screen protagonist (normal lip movement).
- `(S3+...)` = secondary characters, in order of vocal events.
- Characters who **never** speak receive no ID.

---

## 9. Face and silhouette — when to connect the face

The face anchor (`<Picture 1>` = portrait) must be connected **only** when the face
is **recognizable in frame**. Connecting the face in a scene where the character is
only a distant silhouette makes the model **inject an unwanted close-up face**
(duplicated/teleported character effect).

When a **distant silhouette** of the character appears (e.g. behind rainy glass,
far away, blurred):
- **Do not** connect the face as an image.
- Declare an `IDENTITY (silhouette)` block in `subject_definitions`:
  the shape is the same character (build, shoulders, posture, short hair), seen
  only as a silhouette, **without rendering the face** or bringing an identifiable
  figure into frame.

This preserves **identity** (the viewer recognizes who it is) without the close-up
bug.

---

## 10. On-screen text and credits (T2VA)

- Any **visible on-screen text** (sign, banner, label, marquee, caption) goes in
  **English double quotation marks**, preserving the original text/punctuation:
  ```
  A red neon sign reading "营业中" glows above the doorway.
  ```
- **Credits / title card** scenes are T2VA: use `integrated_multimodal_description`
  + `overall_soundscape` + `non_diegetic_music`.

---

## 11. Physical continuity — authoring rules (applicable to any story)

If you adopt the physical-continuity cinematic language (as in this version), these
rules prevent the most common defects:

1. **No crossing walls/doors:** an environment change is covered by fade or a
   planned cut; nothing "crosses" an object.
2. **No teleport:** the new scene opens with the character already at the initial
   position of the environment; in-scene movement is continuous and observable.
3. **One locomotion verb per scene**, no returns to spaces already left.
4. **One-way trajectory:** the environment order never goes backward.
5. **Object persistence:** each object keeps its position and state; changes are
   declared (e.g. a clock/progress indicator never regresses).
6. **Consistent lighting:** each environment keeps its light signature.
7. **Continuity labels** between scenes (connection map) to avoid duplicating a
   character or losing timeline objects.

### 11.8 THE CONTINUISTA (continuity supervisor) — checking the prompt text

A dedicated pass that, in the chained-prompt flow, plays the real-world role of a
**continuista / script supervisor**: before any render it verifies — **from the
text files alone** (the frames do not exist yet) — that CENA `N+1` continues CENA
`N` behaviorally and physically. Three mandatory axes:

**(a) Movement screen direction — "keeps walking the same way":**
- If CENA `N` ends with the character (or camera) moving **screen-right**, CENA
  `N+1` must open moving the **same direction**, or explicitly reset (fade/cut to a
  new setup). Reversing with no warning makes the character "turn around" for no
  reason.
- Anchor the direction to a visible landmark declared once per beat (§5/§11) and,
  in Shot 1 of CENA `N+1`, declare the continuity (e.g. "continuing his step to
  the right that ended the previous scene").
- One locomotion verb per scene; never return to a space already left (§11).
- In an environment-change/title-card fade, the cut itself is the reset — the
  direction may diverge only because the scene was recomposed, never mid-traversal.

**(b) Object state / timeline — "never regresses" (reinforces §11 item 5):**
- Any progress indicator (bar, clock, counter, gauge, percentage) at the **end of
  CENA `N`** must be **equal** to the **start of CENA `N+1`**; afterwards it may
  advance. Check every numeric mention in both texts.
- List each persistent object with its state at **scene start and end** (connection
  map) and confirm no value went backward or "jumped" in time.
- A contradiction between the shot text and the `<Picture N>` (chain frame) has
  caused "time going backward" — the prose and the reference must never diverge.

**(c) Wardrobe / body / assets — "same clothes, same build, same refs":**
- The wardrobe and body description must be **textually identical** across every
  CENA in which the character appears; any drift (jacket color, beard, a vanished
  prop) reads as another character.
- `<Picture 1>` (face) is connected **only** when the face is recognizably
  on-screen, and supplies **face only — never appears in-frame** (§9).
- `<Picture 2>` (chain frame of CENA `N`) must be connected into CENA `N+1` and
  match the previous scene's actual final **non-black** frame (§6).

**Output of the pass** — per adjacent pair, one row:
`N→N+1 | screen direction ✓/✗ | objects forward-only ✓/✗ | wardrobe/body identical ✓/✗ | chain frame referenced ✓/✗`.
Any ✗ blocks the batch until corrected (§14.4).

### 11.9 Production-detail craft checks (macro / audio / composition)

Applies the assistant v6.1 craft layer to a scene that uses it (macro/impact shots,
precise SFX, night/depth composition). These are per-scene checks, reported alongside
§13:

- **Macro & impact physics (assistant Part 10-B):** on-entry state stated (action
  already in progress); deformation sequence compression → peak → release → ejecta is
  coherent and readable; ejecta is **sparse and natural unless explosive is the
  intent**; surface interaction (compression, tearing, impressions left behind) is
  recorded as persistent object state. ✗ when the deformation/pulse reads as
  unmotivated, or ejecta is described as explosive without that intent.
- **Bokeh / practical-lights-as-framing (Part 10-C):** when lights appear as framing,
  it is stated as a composition statement (source + quality: soft, large, circles),
  explicitly kept out of focus in the background, with a sharply defined foreground.
  ✗ when bokeh is mixed into the lighting spec as illumination instead of framing.
- **Two-point color-temperature separation (Part 10-D):** in night/contrast shots, a
  cool (high) backlit source + warm (low) practical fill stated as a pair, with the
  subject/background outcome (subject holds warm, background falls cool). ✗ when only a
  single key color is given where the new rule applies.

---

## 12. Project workflow tips (opencode)

1. **Split the story into scenes** (e.g. 12–17 scenes, ~12 s each, 16:9, 0.7 MP at
   24 fps as a starting point).
2. **Write a per-scene connection map:** which face connects, which is the chain
   frame from the previous scene (exported final frame), which objects persist.
3. **Define a transition map** before rendering (§6) — this controls whether the
   splices become visible.
4. **Export the final frame** of each rendered scene; check it matches the
   `<Picture 2>` declared in the next scene.
5. **Render one scene at a time**, validating continuity before moving on.
6. **Title and credits** in pure T2VA (no image reference).

---

## 13. Quality checklist (apply to each scene)

- [ ] Correct alignment/opening instruction for the mode used (§2).
- [ ] Style declared before `[Shot 1]` (full-reference and I2VA).
- [ ] `[Shot N] At MM:SS.mmm` with increasing times within the duration.
- [ ] Camera movements with `Amplitude + Speed` when expressive (§5).
- [ ] `(Sx)` global IDs consistent; `<d>[lang] verbatim content</d>` (§8).
- [ ] Voice-over with "lips remain closed"; on-screen speech with lip movement.
- [ ] Full-reference: 6 sections in the correct order (§7).
- [ ] Reference labels (`<Picture N>`, `<Subject N>`) reused, not redefined.
- [ ] Face connected **only** with a recognizable face; distant silhouette via `IDENTITY` (§9).
- [ ] Exported final frame = `<Picture 2>` of the next scene (§6.3).
- [ ] Transition map defined and respected (§6).
- [ ] Physical continuity: one-way, no teleport, object persistence (§11).
- [ ] **Continuista (§11.8):** screen direction equal or explicitly reset across adjacent pairs; object/timeline states forward-only; wardrobe/body identical; `<Picture 2>` referenced and matching the previous final non-black frame.
- [ ] On-screen text in double quotes, verbatim (§10).
- [ ] **`timed_sound_beats` (when present): instants forward, no regression, no dialogue/music, dB levels when contrast matters, consistent with `overall_soundscape` (§3.5).**
- [ ] **Production-detail craft (§11.9): macro/impact deformation sequence coherent with ejecta sparse (unless explosive intended); bokeh-as-framing stated as composition; two-point color temperature stated as a pair when night/contrast applies.**

---

## 14. BATCH / PATH VALIDATION MODE (agent-driven, whole-directory)

When the operator points the validator at a **directory of prompt files** (e.g.
`cenas-opencode/cena_*.txt`) and orders a validation, the agent runs a **batch
pass** across the whole folder instead of one prompt at a time. Procedure:

### 14.1 Discover the files

1. List the directory the operator specified.
2. Identify the scene prompt files by a recognizable pattern (`cena_*.txt`,
   `scene_*.txt`, or any the operator names). Ignore non-prompt files (README,
   notes, images).
3. Sort them in **scene order** by the scene number in the filename (CENA 1, 2, …),
   never alphabetically when that would reorder (CENA 10 before CENA 2).

### 14.2 Run the per-scene checks

For each file in order, apply the §13 checklist. Within a single batch:

- **Cross-scene continuity:** verify the chain — exported final frame of scene N
  (declared `<Picture 2>`) matches `<Picture 1>`/opening of scene N+1; speaker IDs,
  object states (progress bar / clock), environment, and wardrobe persist.
- **Continuista (§11.8):** for each adjacent pair (N→N+1), run the three axes of
  the output row — screen direction equal or explicitly reset; object/timeline
  states forward-only (end N = start N+1); wardrobe/body and references
  (`<Picture 1>`, `<Picture 2>`) identical/consistent. Any divergence becomes a
  ✗ row in the report.
- **Mechanical checks:** timestamps strictly increasing and within the declared
  duration; every `[Shot N]` present and labeled; `<d>[Lang]` blocks closed and
  verbatim; reference labels not redefined; sections in order; on-screen text in
  double quotes. **When `timed_sound_beats` is used, its instants must also be
  monotonically forward and within the duration, and carry no dialogue/music (§3.5).**
- **Structural:** `Technical settings` and `SCENE CONNECTIONS` header present with
  a duration that matches the `[Shot N]` timeline of that file.
- **Production-detail (§11.9):** for scenes that use the craft layer, run the
  macro/impact, bokeh-as-framing, and two-point color-temperature checks; report any
  ✗ in the per-scene row.

### 14.3 Ask where to write any fixes, then produce a batch report

**Safety — ask before writing anything.** When the batch finds fixes to apply, first
ask the operator where those corrected prompts should go:

- **In-place overwrite** — edit each `cena_*.txt` where it is.
- **Separate output path (default / recommended)** — write copies to a new directory
  (e.g. `cenas-fixed/` or `validated/`) and leave the originals untouched.

Never overwrite the original prompt files without an explicit "yes" from the
operator; the separate output path is the safe default so a bad fix never destroys
the source of truth.

**When the operator chooses a separate output path, the agent copies EVERY prompt
there — fixed or not.** The output directory must be a complete, self-contained
set of the whole scene run, never a mix of "fixed ones here and untouched ones over
in the original path". So: corrected files go to the output path **rewritten**, and
files that already passed go there **copied as-is**. The operator should never have
to hunt across two directories to assemble the final set.

**Naming — prefix every file in the separate output path with `valid_`.** Every
prompt written or copied to the output directory is renamed with the `valid_` prefix
(`cena_03_prompt.txt` → `valid_cena_03_prompt.txt`) — **all of them, fixed or not**,
so the directory reads uniformly and nothing suggests a file was skipped. Scene
order is preserved (the `cena_N`/scene number stays in the name). The per-file
difference (corrected vs. copied as-is) is reported in the summary table, not in the
filename.

Deliver a **summary table** of the whole directory, one row per scene:

| CENA | File | Duration | Shots | Chain OK (prev→next) | Continuista N→N+1 (dir·timeline·wardrobe) | Face/`IDENTITY` | Speaker IDs | Result |
|------|------|----------|-------|----------------------|---------------------------------------------|------------------|-------------|--------|
| CENA 1 | cena_00_prompt.txt | 12 s | 1 | — (start) | — (no previous) | face | S1,S2 | ✅ / ⚠️ / ❌ |
| CENA 2 | cena_01_prompt.txt | 12 s | 2 | ✅ | ✓ · ✓ · ✓ | silhouette | S1,S2 | ✅ / ⚠️ / ❌ |

Conclude with:
- **PASS** — every scene passes the §13 checklist and the cross-scene chain is clean.
- **FLAGS (⚠️)** — deviations that are acceptable but worth noting (e.g. decided
  fade-as-cut, a documented exception).
- **FAILURES (❌)** — items that break continuity or the official format and must be
  fixed before rendering, each with the file, the rule (§), and a one-line fix.

### 14.4 Batch exit criteria

Treat the whole folder as **ready to render** only when every individual scene
passes AND the cross-scene chain (last-frame of N → first of N+1) is continuous
for every adjacent pair. Any `❌` blocks the batch until fixed and re-validated.
