# MiniMax H3 Prompt Director — Creative Enhancement Layer

> **Purpose.** This file adds the **creative-enhancement** layer of the MiniMax H3
> prompting workflow. Where the base documents enforce *format* and *continuity*, this
> layer raises the *craft bar* of the brief before it is mapped to H3 syntax: every
> generation is enhanced across seven dimensions (camera identity, visual texture,
> pacing, character, spatial geography, continuity, sound) and then every shot is held
> to a per-shot quality bar. Composes with the rest of the toolkit:
> `agent/prompt-director.md` (structure + interview),
> `agent/validator.md` (consistency),
> `agent/craft/dramaturgy.md` (film craft), and
> `agent/craft/full-reference-advanced.md` (advanced full-reference).

> **License & attribution.** This document is a **derivative** of the
> **[Minimax-H3-Prompt-AgentSkill](https://github.com/benjiyaya/Minimax-H3-Prompt-AgentSkill)**
> by **Hermes Agent (benjiyaya)**, licensed under the **MIT License**. Reused and adapted
> with attribution kept. Base document © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** —
> CC BY 4.0.

---

## Contents

1. Classify the mode first (frame-anchor vs reference)
2. Gather the parameters
3. Creative enhancement — seven dimensions
4. The per-shot quality bar
5. Long-form storytelling pattern
6. Action choreography pattern
7. Common pitfalls
8. Creative-enhancement checklist

---

## 1. Classify the mode first

Decide the H3 mode from what the user attached before writing anything:

| User provides | Mode |
|---|---|
| Reference images/videos/audio (character sheets, style refs, voice clips) | **Ref2VA** |
| Nothing but a text idea | **T2VA** |
| 1 image as first frame | **I2VA** |
| 2 images (first + last frame) | **FL2VA** |
| 1 image as last frame only | **L2VA** |

**Key distinction:** an image used as a **first/last frame anchor** = I2VA/FL2VA/L2VA;
an image used as a **character/style reference** (not a frame position) = Ref2VA. When
ambiguous, ask: *"Is this image a frame anchor (first/last frame of the video) or a
reference (character/style/scene)?"*

---

## 2. Gather the parameters

Confirm these before enhancing (ask if missing; proceed when the idea is clear):

- **`duration_s`** — 4–15 seconds (integer). Default **8** if unspecified.
- **Aspect ratio** — 16:9, 9:16, 1:1, 4:3, 21:9. Default 16:9.
- **Shot count** — let the planner decide or respect the user's explicit count. Budget:
  4–6s → **1–2 shots**; 7–10s → **2–3 shots**; 11–15s → **3–5 shots**.
- **Asset inventory** — what each attached file is and its role. Numbering per modality:
  Image k → `<Picture k>` or `<Subject N>`; Video k → `<Video k>`; Audio k → `<Audio k>`.

---

## 3. Creative enhancement — seven dimensions

This is the core value-add. Take the user's idea and enrich it across seven dimensions to
produce the depth of a professional storyboard before mapping to H3.

### 3.1 Camera Identity

Assign a distinct camera aesthetic matching the scene's tone:

- **Physical type:** handheld, tripod, drone, steadicam, dolly, security cam, dashcam,
  POV, arc shot.
- **Imperfections to preserve** (when stylistically appropriate): hand tremor, autofocus
  hunting, exposure fluctuation, lens flare, motion blur, awkward zoom.
- **Format/aesthetic hint:** 16mm film, DV tape, digital clean, anamorphic, vintage
  camcorder, broadcast.

### 3.2 Visual Texture (LOOK)

Define image quality and color science:

- **Grain/noise:** film grain, electronic noise, clean digital, VHS tracking, subtle blur.
- **Color palette:** warm/cool, saturated/desaturated, high/low contrast, natural skin tones.
- **Lighting design:** natural, studio, neon, golden hour, mixed sources, practical lights.
- **Lighting transitions:** if locations change across shots, describe how the light shifts.

### 3.3 Pacing Arc

Plan the energy progression across the full duration:

- **Build patterns:** quiet→energetic, tense→release, slow build→explosive peak→settle,
  steady rhythm.
- **Cut rhythm:** accelerating cuts toward the climax, slow contemplative holds, musical
  cutting on beats.
- Match the pacing arc to the emotional intent of the brief.

### 3.4 Character Detail

Flesh out every on-screen person:

- **Physical:** age range, build, hair color/style, skin tone, distinctive features
  (scars, freckles, heterochromia).
- **Wardrobe:** specific garments with colors, materials, textures, accessories; note
  changes across locations or time.
- **Visual signature:** a recurring color or visual element that makes the character
  instantly recognizable in every shot (e.g. "electric purple energy trails", "glossy
  teal jacket reflections", "always wears a red scarf").
- **Coverage note:** describe outfits fully — avoid implying revealing clothing unless
  the user explicitly requests it.

### 3.5 Spatial Geography

For action sequences or multi-location videos:

- **Screen direction:** who enters from where, movement vectors (Left→Right, Deep→Front,
  foreground↔background).
- **Key action moments:** the 2–3 critical motion beats that define the sequence.
- **Environmental layout:** what's in the space, how it's lit, reflective surfaces, depth.

### 3.6 Continuity Progression

Track what changes across shots so the video stays coherent:

- **Physical state:** damage accumulates, hair gets messier, clothes get wet/torn/dusty.
- **Environmental:** props move, lights flicker, weather shifts, debris scatters.
- **Emotional:** expressions and body language evolve naturally across the timeline.

*(This extends — never overrides — the repo's "never regress" and object-persistence rules.)*

### 3.7 Sound Design Plan

Map the full audio landscape:

- **Ambience:** room tone, environmental atmosphere, chatter, traffic, wind.
- **Physical action sounds:** footsteps, impacts, fabric rustling, door creaks, liquid pouring.
- **Non-diegetic score:** instrumentation, tempo, rhythm, dynamic changes →
  `non_diegetic_music`.
- **Diegetic music:** source-visible (radio, speaker, live performance) → shot description.
- **Dialogue vs voiceover:** mark which is spoken on-camera vs off-screen narration.

---

## 4. The per-shot quality bar

Every shot in the storyboard must specify:

- **Composition** — framing (wide, medium, close-up, extreme close-up, macro), angle
  (eye-level, low, high, overhead, Dutch).
- **Camera motion** — type + amplitude + speed in natural English (e.g. "The camera
  pushes in with small amplitude at slow speed").
- **Subject action** — exactly **ONE dominant action** per shot; never cram multiple.
- **Environment/lighting** — what's visible, how it's lit, time-of-day cues.
- **Sound cue** — what's audible in this specific moment.
- **Reference labels** (Ref2VA only) — where referenced content appears or takes effect.

---

## 5. Long-form storytelling pattern

Montage / day-in-the-life / narrative:

- Explicit **CAMERA / LOOK / STYLE** blocks defining the aesthetic before the storyboard.
- **Outfit and location progression** across time, with lighting transitions.
- **Voiceover** carrying emotional narrative over non-synchronized visuals.
- Pace building from quiet opening to high-energy finale.
- Per-shot storyboard with timing, action, and VO lines.

---

## 6. Action choreography pattern

Combat, chase, sports:

- **Per-character color lock** — each character has a signature color for their
  effects/trails/reflections.
- **Explicit spatial layout** with screen directions (Deep→Front, Left→Right).
- **Action vectors** — the key motion moment that defines each shot.
- **Progressive continuity** — damage accumulates, hair gets windblown, dust/cracks appear.
- **Strict shot-count enforcement** — state the count and hold it.
- **Environmental reactivity** — holograms flicker, floors reflect, walls crack in
  response to action.

---

## 7. Common pitfalls

1. **Treating Ref2VA reference images as frame anchors.** A character sheet or style
   reference is a `<Subject>`, not a `<Picture>`. Only use `<Picture N>` standalone when
   the image IS a concrete frame position. When in doubt, cite the image inside the
   relevant `<Subject N>` line.
2. **Cramming multiple actions into one shot.** One dominant action per shot — a hard H3
   constraint. Split sequential actions across shots with cuts.
3. **Forgetting camera motion.** Every shot needs an explicit camera movement — even
   "Static Shot". Omitting leaves the model guessing.
4. **Inconsistent character identity across shots.** Repeat identity anchors (hair,
   clothing, key props) in every shot, phrased freshly but consistently. If hair gets
   messy in shot 3, it stays messy in shot 4.
5. **Wrong shot count for duration.** Respect the budget (§2): don't plan 5 shots for a
   5-second video.
6. **Mixing diegetic and non-diegetic music.** Music audible to characters goes in the
   shot; audience-only score goes in `non_diegetic_music`. Never put the same music in both.
7. **Inventing reference labels (Ref2VA).** Never create labels beyond those defined in
   `subject_definitions`. `<Subject 3>` means the same thing in every section. Different
   modalities number independently.
8. **Translating or rewriting dialogue.** Preserve the exact words inside `<d>` —
   punctuation, hesitations, and language included.
9. **Skipping the style opener.** `detailed_description` (Ref2VA) /
   `integrated_multimodal_description` (Base) MUST open with 1–2 sentences of overall
   style before `[Shot 1]`.
10. **Flat timestamps.** `[Shot 1]` never has a timestamp; every subsequent shot needs
    `At MM:SS.mmm` with strictly increasing times.
11. **IP leakage.** Avoid named third-party IP, real celebrities, and trademarked
    characters — describe them generically.

---

## 8. Creative-enhancement checklist

Run before delivering the enhanced prompt:

- [ ] Mode correctly detected from attached assets and intent (§1).
- [ ] All seven enhancement dimensions applied: camera, look, pacing, character, spatial,
      continuity, sound (§3).
- [ ] Output contains ONLY the required H3 fields — no preamble, no markdown fences, no
      extra commentary.
- [ ] Timestamps strictly increasing and within `duration_s`; `[Shot 1]` has no timestamp.
- [ ] Exactly **one** dominant action per shot.
- [ ] Camera motion specified for every shot (type + amplitude + speed).
- [ ] Character identity consistent across all shots (repeat anchors each shot; visual
      signature kept).
- [ ] Dialogue preserved verbatim in `<d>[Language] ...</d>`.
- [ ] Style opener present before `[Shot 1]`.
- [ ] Reference labels (Ref2VA) used consistently and never invented beyond
      `subject_definitions`.
- [ ] Duration (4–15 s) and aspect ratio specified or assumed-and-flagged (§2).
- [ ] No named IP, celebrities, or trademarked characters.
- [ ] Diegetic music in shot descriptions; non-diegetic score in `non_diegetic_music`.

---

## 9. Mapping onto this repo's workflow

| This layer (§) | Composes with | Typical use |
|---|---|---|
| Mode classification (§1) | validator §1 (modes) | every request |
| Parameters (§2) | assistant v6 interview (`max_clip`, duration) | shot count + duration |
| Seven dimensions (§3) | `dramaturgy` + `[Shot N]` prose | elevating any brief |
| Per-shot quality bar (§4) | `validator` §13 checklist | every shot |
| Storytelling (§5) / Action (§6) | chained-scenes workflow | multi-scene films |
| Pitfalls (§7) | `validator` + continuity rules | QA pass |
| Checklist (§8) | `validator` §13 | final gate |

The repository's canonical rules still govern: chain frame = exported last non-black
frame of the previous scene (`<Picture 2>`), fade-as-cut unless a varied map was
explicitly approved, progress indicators never regress, face connected only when
recognizable on-screen, and `lips-remain-closed` only on off-screen voice-over.
