---
name: adcreator
description: >
  Create Video Ads using AdCreator Studio's Video Ad editor via MCP.
  Trigger: "use adcreator", "make a video ad", "create a video ad about", or any request to produce a short-form video ad.
  Covers the full workflow: questionnaire → script → TTS → B-Roll → overlays → transitions → editor link → render.
  Also handles iterative editing in chat (update overlays, swap transitions, change voice, reorder beats).
  Not for editing existing rendered MP4 files, exporting captions only, single-image static ads, or custom HTML-based video; use HyperFrames for custom HTML compositions.
  Requires AdCreator Studio MCP connection (adcreatorstudio.com).
---

# Video Ads — MCP Workflow

DR Videos are 9:16 short-form video ads built from **beats** (script sections), each with voiceover, B-Roll, and optional overlay blocks. Common length: 15–90s depending on script complexity.

---

## HARD-GATE: Session Questionnaire

<HARD-GATE>
Before calling ANY dr_ tool, collect all REQUIRED fields. Do NOT call dr_video_create with placeholder text.

**REQUIRED:**
1. `product_name` — name of the product
2. `one_liner` — what it does in ≤10 words
3. `target_audience` — who (age/gender/situation) + their primary pain
4. `mechanism` — specific, novel reason the product works (NOT generic benefits)
5. `social_proof` — followers, review count, or verbatim testimonial quote
6. `offer` — CTA offer: discount %, free trial, urgency ("only 48h")
7. `voice_preference` — tone: conversational/authority/energetic/calm; gender preference

**OPTIONAL but improves quality:**
8. `brand_colors` — primary hex, accent hex
9. `product_image_url` — hero product shot (public URL)
10. `own_broll_available` — does user have B-Roll? If yes, get URLs
11. `instagram_handle` + `instagram_followers` — for social proof overlays

If the user provides a description implying most fields, infer and proceed. Ask only for missing REQUIRED fields, in a single message.
</HARD-GATE>

---

## GATE: Script Source

**Does the user already have a script?**

→ **Yes** — read [references/script-input.md](references/script-input.md) to parse it into beats. Skip to Step 2. Do **NOT** rewrite or "improve" the user's copy.

→ **No** — continue to Step 1 to draft the script.

---

## Step 0 — Pick a Visual Palette

Before scripting, pick a palette based on niche:

| Niche | Palette |
|---|---|
| Beauty, skincare, wellness | [palettes/beauty-wellness.md](palettes/beauty-wellness.md) |
| Fitness, supplements, sport | [palettes/fitness-energy.md](palettes/fitness-energy.md) |
| Finance, coaching, high-ticket | [palettes/finance-coaching.md](palettes/finance-coaching.md) |
| Tech, SaaS, productivity | [palettes/tech-saas.md](palettes/tech-saas.md) |
| Youth, gaming, entertainment | [palettes/youth-entertainment.md](palettes/youth-entertainment.md) |

If `brand_colors` were provided, use those as primary + accent. Derive a matching palette.

### Visual Director Contract

Define this **before any overlay or transition calls** and hold it constant through all calls:

```json
{
  "style_name": "Soft Signal | Velvet Standard | Swiss Pulse | Maximalist Type | Deconstructed | Data Drift | Folk Frequency | Shadow Cut",
  "energy": "calm | medium | high",
  "palette": {
    "backgroundColor": "#...",
    "textColor": "#...",
    "mutedTextColor": "#...",
    "accentColor": "#..."
  },
  "overlayStyle": {
    "surface": "solid | glass | dark-card | editorial-label",
    "position": "top | middle | bottom",
    "scale": "0.85-1.25",
    "opacity": "0.80-1.00"
  },
  "transitionLanguage": {
    "primary": "crossfade | blur-through | whip-pan | flash-through-white | cinematic-zoom | glitch",
    "accent": "one transition used once at the emotional climax"
  }
}
```

Design rules:
- One visual identity, repeated. Do not invent new colors per overlay.
- One primary transition family + one accent. Never randomize every boundary.
- Video text readable in two seconds: fewer words, bigger type.
- 9:16 safe zones: text within 64px left/right, avoid top 120px and bottom 180px except for deliberate lower-thirds.
- No AI tells: no generic purple-blue gradients, no centered-everything, no empty cards, no placeholder copy.

---

## Step 1 — Write the Script

**MANDATORY before scripting:** Read [references/beats.md](references/beats.md) and [references/copywriting.md](references/copywriting.md).

**Language:** Match target market. German product = German script, colloquial not formal.

### Templates (starting suggestions — not hard constraints)

Templates seed a default beat structure. **Add or remove beats freely based on script structure.** If the user's script has 9 natural emotional units, use 9 beats. Do not merge beats to fit a template.

| Template | Starting beat sequence |
|---|---|
| `classic_dr` | hook → problem → agitate → why_others_fail → mechanism → proof → cta |
| `problem_solution` | hook → problem → value_prop → proof → objection → cta |
| `ugc_style` | hook → problem → mechanism → proof → cta |
| `short_hook` | hook → mechanism → cta |

### Script Process

1. **Full monologue first** — one continuous story, no beat labels, natural spoken language
2. **Read aloud** — fix anything choppy or announcement-style before proceeding
3. **TTS normalization** — no em-dashes (→ comma), no period stacks (→ conjunctions), spell numbers, spell currency
4. **Assign beats** — label paragraphs by beat type. Do NOT rewrite sentences during this step.
5. **Add audio tags** — 1–2 ElevenLabs v3 audio tags per beat. See [references/voice.md](references/voice.md).

**Show the user the full monologue BEFORE calling any tool. Get confirmation.**

For a complete worked example with beat assignment and audio tags, see [examples/kreatin-ugc-30s.md](examples/kreatin-ugc-30s.md).

### Ad Goal

- **Product page CTA** (to shop/landing page): No social-follow blocks. CTA is "link below / tap here / swipe up."
- **Social follow CTA**: Use only social-follow blocks returned by `dr_blocks_list`. CTA is "follow for more."
- When unsure: ask the user. A discount + shop URL = always product-page CTA.

---

## Step 2 — Select Voice + Create Video

Call `dr_voice_list`. Filter by target language. Priority: cloned > professional > premade. See [references/voice.md](references/voice.md).

Present max 3 options (name, category, preview URL). Wait for user confirmation. Then:

```typescript
dr_video_create({
  title: "Product Name — Hook Variant 1",
  template: "ugc_style",
  beats: [ /* approved beats with audio tags */ ],
  voice_id: "...",
  caption_style: "pop"  // pop (default) | classic | highlight
})
// Returns: videoId, editorUrl, beats[] with IDs — store all for next steps
```

---

## Step 3 — Generate TTS

Call `dr_beat_tts` for each beat **sequentially** (not parallel — rate limits):

```typescript
dr_beat_tts({ video_id: "...", beat_id: "..." })
// Returns: audioUrl, durationMs — total should sum to 15–45s
```

<HARD-GATE>
Before any `dr_overlay_add` or `dr_transition_add`, every beat MUST have TTS (`audio_url` set on the beat).
Run `dr_beat_tts` for each beat ID first. If TTS fails (model bug, quota, voice error), STOP and tell the user. Do NOT add overlays against estimated beat durations; they will drift.
</HARD-GATE>

---

## Step 4 — B-Roll

Read [references/broll.md](references/broll.md) and [references/assets.md](references/assets.md) for strategy and priority.

**Asset gap detection:** Before calling `dr_broll_suggest`, check `dr_assets_list` for user-uploaded footage. For mechanism and proof beats, if no matching product asset exists, offer the user a direct upload link:

```typescript
dr_assets_list({ video_id })  // check what's available

// If no product closeup for mechanism beat:
// "I'd love a closeup of [product] for the mechanism beat. Upload it here, or I'll use Pexels stock."
// dr_assets_upload_link({ project_id, slot_label: "Product closeup", hint: "Mechanism beat — product detail shot" })
```

Priority: user's own library → curated library → Pexels/Unsplash. Never stock for mechanism/proof beats if product assets exist.

```typescript
dr_broll_suggest({ beat_type: "mechanism", vo_text: "...", locale: "de-DE" })
// locale: always pass the VO language locale to get locale-correct Pexels results

dr_beat_broll_assign({ video_id, beat_id, media_url, thumb_url, media_type: "video" })
```

For extra cutaways inside a beat (close-ups, proof inserts, jump-cuts), use `dr_media_clip_add`. These sit above base B-Roll, below captions/overlays. See [references/broll.md](references/broll.md) for `fill_policy` and animation rules.

---

## Step 5 — Overlay Blocks

Read [references/blocks.md](references/blocks.md) before adding overlays. For timing details, read [references/timing.md](references/timing.md). For routing suggestions by beat purpose, read [references/overlays.md](references/overlays.md).

### Two-Step Block Discovery (mandatory)

**Never invent block IDs. Never call `dr_blocks_list()` without `for_beat_type`.**

```typescript
// Step 1: discover blocks ranked for this beat
dr_blocks_list({ for_beat_type: "proof" })
// → slim list with id, label, recommendedFor, moodTags, intensityLevel

// Step 2: for each candidate you want to use — fetch full schema
dr_block_get("dr/instagram-comment")
// → defaultProps, propFields, styleVariants

// Step 3: compose overlay with palette from Visual Director Contract
dr_overlay_add({
  video_id: "...",
  block_id: "dr/instagram-comment",
  props: {
    username: "@customer",
    comment: "This literally changed everything for me.",
    // S3: ALWAYS pass palette from Visual Director Contract:
    accentColor: "#e85d04",
    backgroundColor: "#fff8f2",
    textColor: "#191410",
  },
  timing: { mode: "beat-relative", beat_id: "<proof beat id>", at_ms: 0, duration_ms: 4200 },
  track_index: 1,
})
```

### Visual Director Contract — Color Rule (non-negotiable)

Every `dr_overlay_add` call **MUST** pass `accentColor`, `backgroundColor`, and `textColor` from the active Visual Director Contract palette. Server defaults are fine structurally but won't match the brand palette. Override them every time.

### Word-Anchor Timing

To sync an overlay to a specific spoken word (product name, price, claim word), use word-anchor mode. Word indices come from `caption_words` in `dr_video_get`:

```typescript
dr_overlay_add({
  video_id: "...",
  block_id: "dr/punctuation-pop",
  props: { symbol: "!", accentColor: "#e85d04", backgroundColor: "rgba(10,10,10,.72)", textColor: "#fff" },
  timing: { mode: "word-anchor", beat_id: "<cta beat id>", word_index: 4, duration_ms: 1800 },
  track_index: 2
})
```

### Overlay Variety

Aim for visual variety across beats. Vary block choice per beat purpose — do not repeat the same block more than twice. Check `dr_video_lint` for `low_overlay_variety` warnings and rebalance with alternatives from [references/overlays.md](references/overlays.md).

For full styling props, style presets, position rules, and block catalog: [references/blocks.md](references/blocks.md).

---

## Step 6 — Transitions

Add transitions after overlays. Transitions serve **emotional pivots** — find the real shifts in viewer state, then choose a matching transition. Use the energy routing table in [references/overlays.md](references/overlays.md) to pick the right family.

```typescript
dr_transition_add({
  video_id: "...",
  from_beat_id: "<hook beat id>",
  to_beat_id: "<problem beat id>",
  block_id: "hf/flash-through-white",
  sound_effect: { presetId: "flash-rise", volume: 0.36, offsetMs: -50 }
})
```

| Emotional shift | Suggested transition |
|---|---|
| Hook → Problem ("that's me") | `hf/flash-through-white` |
| Problem → Agitate (pain deepens) | `hf/whip-pan` |
| Stuck → Revelation (the turn) | `hf/cinematic-zoom` — use max **once** as the climax |
| Mechanism → Proof (belief builds) | `hf/crossfade` |
| Proof → CTA (exhale into the ask) | `hf/blur-through` |

Pick ONE primary transition family per video + ONE accent at the emotional climax. Avoid adjacent high-energy transitions (flash + whip-pan in sequence = chaos).

For full transition catalog: [references/blocks.md](references/blocks.md).

---

## Step 7 — Lint

```typescript
dr_video_lint({ video_id: "..." })
```

Resolve all errors before review or render: missing TTS, hidden block, overlay overrun, invalid word_index. Warnings are judgment calls; surface them to user if they affect quality.

---

## Step 8 — Editor Link

After video + TTS + overlays + transitions, give the user the link. **Do NOT auto-render.**

```
Video ready for review: <editorUrl>
Built: [X] beats with voiceover (~Xs) | [X] B-Roll | [X] overlays | [X] transitions
Ready to render? Confirm and I'll start it.
```

---

## Step 9 — Render (confirmed only)

```typescript
dr_video_render({ video_id: "..." })
// Poll dr_video_get every 10s until status = "rendered". Return render_url.
```

---

## Step 10 — Editing in Chat

After creation, users can request changes without opening the web editor. Always call `dr_video_get` or `dr_overlay_list` first to get current IDs.

```typescript
dr_overlay_list({ video_id })                // see existing overlays + IDs
dr_overlay_update({ video_id, overlay_id, props: { headline: "New text" } })
dr_overlay_remove({ video_id, overlay_id })
dr_transition_update({ video_id, transition_id, block_id: "hf/crossfade" })
dr_transition_remove({ video_id, transition_id })
dr_beat_update({ video_id, beat_id, vo_text: "New voiceover..." }) // then call dr_beat_tts
dr_video_update({ video_id, voice_id: "..." })   // then dr_beat_tts for each beat
dr_beats_reorder({ video_id, beat_ids: ["id1","id2","id3"] })
dr_media_clip_update({ video_id, media_clip_id, animation: "zoom_in" })
dr_media_clip_remove({ video_id, media_clip_id })
```

See [references/iterating.md](references/iterating.md) for worked examples of common edit requests.

---

## Non-Negotiable Rules

- Never render without explicit user confirmation
- `hf/cinematic-zoom` max once per ad — it is the climax; use as accent transition only
- `dr/cta-button-pulse` always on CTA beat
- Name the mechanism, never the product, in the mechanism beat
- Mechanism/proof beats: prefer user's own B-Roll or library — stock only as fallback
- `dr_beat_tts` must run for every beat before adding overlays
- TTS calls are sequential — never parallel
- Only use block IDs returned by `dr_blocks_list` — never invent IDs from memory
- Always call `dr_block_get(block_id)` before `dr_overlay_add` to read the full schema
- Every `dr_overlay_add` MUST pass `accentColor`, `backgroundColor`, `textColor` from the Visual Director Contract
- Run `dr_video_lint` before render and fix every error
- Always get IDs from `dr_video_get` or `dr_overlay_list` before update/remove calls
- For images in `dr_media_clip_add`: always set `animation` explicitly (use `ken_burns`, `zoom_in`, `pan_left`, or `zoom_out` — never leave as `none`)
- For short video clips: set `fill_policy` to `loop` or `freeze_last` to prevent dead frames

---

## You Do This Wrong

- Writing English for a German-market product (domain .de or German IG = German script)
- Adding social-follow overlays for product-page CTA ads or weak follower counts
- Adjacent high-energy transitions: flash + whip-pan in sequence = chaos
- Period-stacked fragments "X. Y. Z." — connect with commas
- Em-dashes in VO (TTS restarts at the dash)
- Numbers as digits — spell out before dr_video_create
- Skipping audio tags (every beat: 1–2 ElevenLabs v3 tags)
- Not checking for user's cloned voice before listing other options
- More transitions than the beat count warrants

---

## References (loaded on demand)

- **[references/beats.md](references/beats.md)** — Beat types, niche examples. Read before scripting.
- **[references/copywriting.md](references/copywriting.md)** — DR formulas, hooks, mechanism naming, proof formats. Read "You Write This Wrong" section before starting.
- **[references/blocks.md](references/blocks.md)** — Block catalog, overlay props, style presets, transition catalog. Read before Steps 5–6.
- **[references/overlays.md](references/overlays.md)** — Routing tables: block suggestions by beat purpose + transition energy table. Read before Step 5.
- **[references/timing.md](references/timing.md)** — Beat-relative, word-anchor, absolute timing with examples. Read when setting overlay timing.
- **[references/script-input.md](references/script-input.md)** — How to parse a user-provided script into beats. Read when user already has a script.
- **[references/iterating.md](references/iterating.md)** — Edit loop: update/remove overlays, transitions, beats, voice after creation. Read when user requests changes.
- **[references/broll.md](references/broll.md)** — B-Roll strategy, fill_policy, animation rules, locale search. Read before Step 4.
- **[references/assets.md](references/assets.md)** — Asset library: how to check user uploads, request missing assets, understand auto-tags. Read before Step 4.
- **[references/voice.md](references/voice.md)** — Voice selection, audio tags, TTS settings. Read before Step 2.
- **[palettes/beauty-wellness.md](palettes/beauty-wellness.md)** — Pastel, clean, skin tones.
- **[palettes/fitness-energy.md](palettes/fitness-energy.md)** — Bold, high-contrast, red/black.
- **[palettes/finance-coaching.md](palettes/finance-coaching.md)** — Dark, premium, navy/gold.
- **[palettes/tech-saas.md](palettes/tech-saas.md)** — Dark, minimal, accent neon.
- **[palettes/youth-entertainment.md](palettes/youth-entertainment.md)** — Electric, loud, saturated.
- **[examples/kreatin-ugc-30s.md](examples/kreatin-ugc-30s.md)** — Full worked example: 30s supplement, German UGC style, with script + beat assignment + overlays.
- **[examples/skincare-30s.md](examples/skincare-30s.md)** — Full worked example: 30s skincare, female audience.
- **[examples/fitness-45s.md](examples/fitness-45s.md)** — Full worked example: 45s supplement, male audience.
