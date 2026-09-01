# MiniMax H3 Prompt Director — Dramaturgy Layer (film-craft, model-agnostic)

> **Purpose.** This file ports the **dramaturgy and universal-rules layer** from the
> `smixs/visual-skills` project (an AI film-director skill set for agents) and maps it
> onto the **MiniMax H3** prompt format this repository already uses (T2VA / I2VA /
> FL2VA / L2VA / REF2VA chained scenes). It teaches the *craft* — how films are actually
> built — that the existing `agent/prompt-director.md` (structure) and
> `agent/validator.md` (consistency) do not cover. Read this file
> together with those two: the craft here, the format there.
>
> **What it does not replace.** The model-specific syntax lives in
> `agent/prompt-director.md` (the six REF2VA sections, `subject_definitions`,
> `retention_analysis`, `<Picture N>` tags, dialogue `<d>[Lang]</d>`, `overall_soundscape`,
> `non_diegetic_music`, the chain-frames discipline, fade-as-cut). This file is the
> layer that runs *before* any of that — first decide the story is cinematic, then
> encode it in MiniMax H3 syntax.

> **License.** This document is a **derivative** of
> **[Serge Shima — smixs/visual-skills](https://github.com/smixs/visual-skills)**,
> licensed **CC BY 4.0**. Reuse and adaptation kept the attribution line; see `LICENSE`
> and the source repo. Base document © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** —
> CC BY 4.0.

---

## Contents

1. Core law — the scene formula
2. The Details Law — three physical facts per shot
3. The three-jobs rule
4. Walter Murch's Rule of Six
5. Blocking as choreography of desire
6. Staging controls subtext
7. The camera must have a reason (Fincher)
8. Spatial clarity beats montage hysteria (Spielberg)
9. The environment plays (Kurosawa)
10. Three-layer storyboard method
11. The shot card (14 fields)
12. The rhythm ladder
13. One-anchor principle (five anchors)
14. Mapping craft → MiniMax H3 syntax
15. Divergences from the visual-skills originals (MiniMax-specific)
16. Dramaturgy + continuity check before delivery

---

## 1. Core law — the scene formula

A scene exists only when all five elements are present:

```text
Scene = hero's desire + obstacle + space geometry + controlled gaze + editing rhythm
```

- **Desire** — what the character wants right now, in this specific second.
- **Obstacle** — what blocks them: object, person, fear, distance, rule.
- **Space geometry** — who stands where, who holds the power position, which direction is
  the threat, which is the escape.
- **Controlled gaze** — where the viewer's eye is forced to look; one focal point per frame.
- **Editing rhythm** — how long each shot lives, where the pause lands, where the cut bites.

Before writing a MiniMax H3 prompt, name each of these in one sentence. If you cannot, the
scene is not ready — and no amount of correct `subject_definitions` or `<Picture N>` tags
will save it.

**MiniMax H3 note.** MiniMax's own guides reward this same pre-work. The `[Shot N]` lines in
`detailed_description:` are where the formula lands: `desire/obstacle` becomes the action beat,
`geometry` the blocking, `gaze` the "the eye is drawn to <X>" clause, `rhythm` the timestamp
spacing and fade boundaries.

---

## 2. The Details Law — three physical facts per shot

> Every shot owns three concrete physical details: one **environmental pressure**, one
> **physical micro-action on the body**, one **sound anchor or visual motif**.

1. **Environmental pressure** — a physical fact about the space that carries emotion.
   Cold refrigerator light · wet asphalt · flickering ceiling tube · steam from a kettle ·
   rain on one windowpane · a buzzing AC unit · tight corridor walls.
2. **Physical micro-action on the body** — the emotion translated into the actor's body.
   Jaw locks · knuckles whiten · lips press flat · eyes drop a quarter-inch · swallows hard ·
   fingers curl against the doorframe. *The body is the only place feelings render; names of
   feelings do not.*
3. **Sound anchor or visual motif** — a recurring perceptual hook tied to the spine of the
   piece. A stomach growl repeated three times · a reflection in dark glass on every
   transition · the same musical sting at every Crack beat · the clock's second hand.

A shot with zero of these is filler; one is thin; strong shots carry all three. The writer
does this work — the model cannot infer it.

**Banned lazy words** (placeholders for absent detail): `cinematic`, `professional`,
`high quality`, `masterpiece`, `stunning`, `epic`, `amazing`; `beautiful lighting`,
`dynamic camera`, `intense moment`, `powerful scene`; and emotions named without a body —
`he is sad`, `she is angry`, `he is afraid`. Replace each with three concrete facts.

**MiniMax H3 note.** This maps naturally to `overall_soundscape` (sound anchor) and the
`[Shot N]` prose (environmental pressure + micro-action). Audio here is real model output
(not just rhythm planning): put the diegetic sound anchor in the shot, the ambient in
`overall_soundscape`, and the score in `non_diegetic_music` — never overlap them.

---

## 3. The three-jobs rule

Every shot must do at least one of three things. If it does none, delete it:

- **Change emotion** — in the hero, the viewer, or the dynamic between characters.
- **Advance action** — a new physical event, new information, new position.
- **Increase pressure** — stakes rise, the clock ticks, the space tightens, a witness appears.

"Beautiful establishing shot" is not a job. Either the frame serves one of these three, or it
is a wrapper without candy.

---

## 4. Walter Murch's Rule of Six

Priority order when deciding where to cut. Each item outweighs the sum of everything below it:

1. **Emotion (51%)** — does the cut honor the emotional truth of the moment.
2. **Story (23%)** — does the cut advance story or reveal character.
3. **Rhythm (10%)** — does the cut fall on a musical beat of the scene.
4. **Eye-trace (7%)** — where is the gaze at the moment of the cut; does the next shot receive it.
5. **2D plane (5%)** — does the cut respect the screen-direction axis.
6. **3D space (4%)** — does the cut respect the real location's geometry.

Cutting "for pace" sits at item 3. Serving item 3 ahead of emotion and story produces
attention-deficit mush. In MiniMax H3, your cuts are the `[Shot N]` boundaries and fades —
make each one earn emotion or story first, rhythm second.

---

## 5. Blocking as choreography of desire

Blocking is not "where the actor stands." It is a visual answer to *what the character wants
and from whom*. For every character, name: what they want now, who/what they move toward,
who/what they move away from, whom they corner, to whom they yield space, and what gesture
reveals the hidden desire.

- **Bad.** "He stands near the window."
- **Good.** "He edges toward the window but his shoulder stays angled back toward her, as if
  the conversation still holds him."

**MiniMax H3 note.** One locomotion verb per scene (your v6 action-direction rule already
says this). Blocking language from this layer strengthens it: give the start point, end
point, the object touched, the gaze, and what the body reveals.

---

## 6. Staging controls subtext

The arrangement of people, objects, and camera inside the frame tells the conflict before
dialogue. Power signals:

- The standing character dominates the seated one.
- The character in the doorway controls the room.
- The character behind glass or in reflection is psychologically distant.
- The character in shadow carries threat or grief.
- Negative space around a character signals isolation.
- Tight framing signals suffocation.
- Shared frame without eye contact signals broken intimacy.

Before writing, name the power dynamic the staging reveals. This is your chance to make a
chained scene *mean* something even when the camera barely moves.

---

## 7. The camera must have a reason (Fincher)

Every camera movement answers **"what changed?"** If the answer is nothing, the camera is
static. Reasons to move: a character made a decision and the camera follows the shift; new
information arrived in frame; pressure escalated and the camera tightens; the character
looked and the camera reveals what they saw; a gesture pulled focus (rack focus); the space
changed.

- **Bad.** "Cinematic gliding camera movement."
- **Good.** "Push-in starts on 'I don't know' and stops on her jaw locking."

**MiniMax H3 note.** Your v6 camera language (motion type + amplitude + speed, one dominant
move per scene, state continuation like "continuing the slow push in") is exactly the vehicle
for the Fincher rule. Every `[Shot N]` camera verb must carry its "because X changed."

---

## 8. Spatial clarity beats montage hysteria (Spielberg)

Even in chaos, the viewer must know: where the hero is, where the threat is, which direction
is escape, which direction is decision. High craft is fast, nervous, *and* still readable.
Before a fast-cut sequence, sketch the geography in one sentence: "Hero moves left-to-right;
threat enters from the top of the frame; exit is off-camera right."

**MiniMax H3 note.** This reinforces your v6 rule that an environment change is covered by a
fade/cut (no wall traversal) and the new scene opens with the character already at the start
position. Restate the geometry once per scene (your persistence block).

---

## 9. The environment plays (Kurosawa)

Weather and environment are characters. For a short clip, lean on **one environmental
pressure** per scene: flickering fluorescent (decay, bureaucracy, dread) · rain on a window
(grief withheld) · steam from a kettle (suppressed anger) · buzzing AC (dissociation) · wet
asphalt at night (guilt) · a tight corridor (walls closing in) · mirror/glass (self-reckoning)
· cold overhead office light (judgment).

Your v6 persistence already restates a lighting signature per scene — this layer gives that
signature an emotional job, not just a visual one.

---

## 10. Three-layer storyboard method

Build in three layers, in order. Skipping one produces pretty-but-empty output.

### Layer 1 — Dramatic beats (60–90 s reference)

```text
0-5s    Hook        Hero already in tension. Problem on screen.
5-15s   Context     Where we are, who is near, what is at stake.
15-30s  Pressure    Hero tries to hold control.
30-45s  Crack       A detail appears that breaks the hero's position.
45-60s  Acceleration Cuts shorten, breath tightens.
60-75s  Impact      Decision, break, confession, or action.
75-90s  Aftermath   Brief silence or visual residue.
```

Compress proportionally for 30 s / 15 s. Never skip the Crack or the Impact.

### Layer 2 — Shot functions

Tag every shot: **Establish** · **Power** · **Pressure** · **Detail** (object/hand/eye
macro) · **Reaction** (face after the event) · **Shift** (inner change made visible) ·
**Impact** (decisive frame) · **Aftermath** (emptiness) · **Exit** (final image carried out).

### Layer 3 — Editing rhythm

A rhythmic staircase, not random mincing:

```text
long - shorter - shorter - pause - impact
```

The pause before the impact matters more than the speed of the cuts. Without a pause, speed
becomes a visual meat grinder.

**MiniMax H3 note.** In a chained film split by camera cuts and fade-as-cut transitions, map
Layer 3 onto the v6 rhythm discipline: timestamps strictly increasing, restart per CENA,
final shot fits the duration, fade-out to black (except the final hard cut), one dominant
camera move per scene in fade-as-cut mode.

---

## 11. The shot card (14 fields)

For each shot in a storyboard, fill every field — an empty field is missing direction:

- **Shot ID** · **Beat** (what changes here) · **Emotion** · **Frame** (wide/medium/CU/macro)
- **Composition** (center, edge, negative space, reflection, silhouette, obstruction)
- **Camera** (static, push-in, handheld, tracking, whip-pan)
- **Movement reason** (answer "what changed?")
- **Action** (exact physical event) · **Eye trace** (where gaze lands first 0.3 s)
- **Duration** · **Cut type** (match cut, smash cut, cut on action)
- **Sound** · **Light/color** · **Production note**

In MiniMax H3 the shot card condenses into each `[Shot N]` line of `detailed_description:`.
Use the card as the pre-write; compress into syntax for the final prompt.

---

## 12. The rhythm ladder

- **Slow-burn drama:** 4s, 4s, 3s, 2s, 1s, pause, 2s.
- **Commercial product arc:** 3s, 2s, 1.5s, 1s, 0.5s (product macro), 2s (hero shot).
- **Anxiety build:** 2s, 1s, 1s, 0.5s, 0.5s, 0.3s, pause, 1s.
- **Impact scene:** pause, 0.2s (flash), 2s (aftermath in stillness).

Always insert at least one pause before the biggest cut.

---

## 13. One-anchor principle (five anchors)

For any short dramatic piece (or single scene), commit to exactly five anchors:

- **One main emotion.**
- **One visual motif.**
- **One anchor object.**
- **One break** (the moment the object/emotion changes).
- **One final image.**

Example: emotion *guilt* · motif *reflected in glass* · anchor object *phone with one
unread message* · break *he deletes it* · final image *his face ghosted on the dark phone
screen*. This set carries weight; stacking "cinematic, professional, high quality" forever
does not.

**MiniMax H3 note.** The **final image** = the last non-black frame you export as the next
scene's chain frame (`<Picture 2>`). Design it (§13 here, your v6 §4.10) so it is both a
satisfying anchor and a clean handoff.

---

## 14. Mapping craft → MiniMax H3 syntax

How each craft layer becomes a concrete field of your existing REF2VA / T2VA format:

| Craft layer (§) | Where it lands in the MiniMax H3 prompt |
|---|---|
| Scene formula (§1) | `summary:` one line naming desire/obstacle; `[Shot N]` beats carry it |
| Three details (§2) | `[Shot N]` prose (env pressure + micro-action) + `overall_soundscape` (sound anchor) |
| Three-jobs (§3) | every `[Shot N]` must do one of: change emotion / advance action / raise pressure |
| Murch rule (§4) | the `[Shot N]` cut points and fade boundaries — emotion & story first |
| Blocking (§5) | `[Shot N]` blocking prose: start, end, object touched, gaze, body reveal |
| Staging (§6) | `[Shot N]` composition (power position, shadow, negative space, eye contact) |
| Camera reason (§7) | every camera verb in `[Shot N]` carries "because X changed" |
| Geography (§8) | restate the spatial map once per scene (your persistence block) |
| Environment (§9) | the `[Shot N]` lighting signature + `overall_soundscape` ambient |
| Storyboard (§10) | the `[Shot N]` list itself, tagged with shot functions & a rhythm |
| Shot card (§11) | one card per `[Shot N]`, compressed into its line |
| Rhythm (§12) | `[Shot N]` timestamp spacing, pause before the impact, fade cadence |
| Anchors (§13) | `summary:` + `SCENE CONNECTIONS` header + final-frame design |

---

## 15. Divergences from the visual-skills originals (MiniMax-specific)

These are deliberate adaptations — do **not** import the original mechanism wholesale:

- **Transitions.** visual-skills offers a wide transition vocabulary (dissolve, wipe,
  match cut, occlusion mask, zoom-through). Your project deliberately **locks one mode**:
  **fade-as-cut** (every scene fades out; the final hard cut), chosen for spatial reset
  across independent renders. Keep your locked map; only borrow the vocabulary if you move
  to Option B (varied map) with explicit user approval.
- **Character identity.** visual-skills repeats a full identity block in every prompt.
  You already do this via `<Picture N>` face references + the `IDENTITY (silhouette)` block
  (face connected **only** when on-screen recognizable). Keep the reference discipline;
  the repeated *textual* identity block is a fallback for when no reference is connected.
- **Audio.** visual-skills treats sound partly as rhythm planning (some models don't render
  audio). MiniMax H3 **does** generate audio — so `overall_soundscape` and
  `non_diegetic_music` are real, with your strict separation rules. Do not flatten them into
  "rhythm notes."
- **Multi-clip/final-frame.** visual-skills stitches clips in the editor (U11, U10 splits).
  You chain scenes directly through the **exported final frame → `<Picture 2>`** mechanism.
  The five-anchor "final image" and the chain frame are the same asset — design once.
- **Duration splitting.** visual-skills defaults to 5 s clips; your baseline is 12 s per
  scene / `max_clip` from the user's VRAM answer. Keep your VRAM batch-capacity inquiry
  (v6 §3.3) — it replaces the fixed 5 s default.
- **Progress indicators.** Your canonical "never regress" rule (end state of CENA N = start
  state of CENA N+1) is stronger than anything in visual-skills. The dramaturgy layer
  reinforces it but never supersedes it.

---

## 16. Dramaturgy + continuity check before delivery

Combined gate — run this **after** the assistant writes a prompt and **before** the model
renders. It layers the craft check on top of your validator's consistency pass.

**Dramaturgy (six-point, adapted):**
1. Is the scene formula complete? (desire + obstacle + geometry + gaze + rhythm)
2. Does every shot pass the three-detail check? (env pressure + micro-action + sound/motif)
3. Does every shot do one of the three jobs? (change emotion / advance action / raise pressure)
4. Is there a motivated reason for every camera move?
5. Is the spatial geometry readable?
6. Are the five anchors named? (emotion, motif, object, break, final image)

**MiniMax continuity (from this repo's validator + v6):**
- Chain frame `<Picture 2>` equals the previous scene's actual last non-black frame.
- Progress indicators never regress; end state of N = start state of N+1.
- Face connected only when recognizable on-screen; distant silhouette uses the `IDENTITY` block.
- `lips-remain-closed` only on off-screen voice-over; on-screen lines use normal lip movement.
- Transition locked to the scene connection map (fade-as-cut, except final hard cut).
- One dominant camera move per scene in fade-as-cut mode; timestamps strictly increasing.
- `overall_soundscape` / `non_diegetic_music` separated; no overlap; ≤ 7000 chars.

If any dramaturgy point is **no**, fix the *story* before rendering — a correct-format prompt
with thin dramaturgy produces wallpaper. If any continuity point is **no**, fix the *chain*
before rendering — a broken chain breaks the film regardless of craft.
