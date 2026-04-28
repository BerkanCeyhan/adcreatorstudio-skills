---
name: adcreator
description: >
  Create Video Ads using AdCreator Studio's Video Ad editor via MCP.
  Trigger: "use adcreator", "make a video ad", "create a video ad about", or any request to produce a short-form video ad.
  Covers the full workflow: questionnaire → script → TTS → B-Roll → overlays → transitions → editor link → render.
  Requires AdCreator Studio MCP connection (adcreatorstudio.com).
---

# Video Ads — MCP Workflow

DR Videos are 9:16 short-form video ads (15–45s) built from **beats** (script sections), each with a voiceover, B-Roll, and optional overlay blocks. Write the complete script before calling any tool. Hand the user an editor link to review before rendering.

---

## HARD-GATE: Session Questionnaire

<HARD-GATE>
Before calling ANY dr_ tool, you MUST collect all REQUIRED fields. Do NOT call dr_video_create with placeholder text.

**REQUIRED:**
1. `product_name` — name of the product
2. `one_liner` — what it does in ≤10 words
3. `target_audience` — who (age/gender/situation) + their primary pain
4. `mechanism` — the specific, novel reason the product works (NOT generic benefits)
5. `social_proof` — followers, review count, or verbatim testimonial quote
6. `offer` — CTA offer: discount %, free trial, urgency ("only 48h")
7. `voice_preference` — tone: conversational/authority/energetic/calm; gender preference

**OPTIONAL but improves quality:**
8. `brand_colors` — primary hex, accent hex
9. `product_image_url` — hero product shot (public URL)
10. `own_broll_available` — does user have B-Roll? If yes, get URLs
11. `instagram_handle` — for social proof overlay
12. `instagram_followers` — follower count to display

If the user provides a product description that implies most fields, infer and proceed — don't block on optional fields. If REQUIRED fields are missing, ask for them in a single message (not one by one).
</HARD-GATE>

---

## Step 0 — Pick a Visual Palette

Before scripting, pick an overlay palette based on the product niche. This controls which blocks look best and how overlays render.

| Niche | Palette |
|---|---|
| Beauty, skincare, wellness | [palettes/beauty-wellness.md](palettes/beauty-wellness.md) |
| Fitness, supplements, sport | [palettes/fitness-energy.md](palettes/fitness-energy.md) |
| Finance, coaching, high-ticket | [palettes/finance-coaching.md](palettes/finance-coaching.md) |
| Tech, SaaS, productivity | [palettes/tech-saas.md](palettes/tech-saas.md) |
| Youth, gaming, entertainment | [palettes/youth-entertainment.md](palettes/youth-entertainment.md) |

If `brand_colors` were provided, use those as the primary + accent. Derive a matching palette.

---

## Step 1 — Write the Script

**MANDATORY before scripting:**
- Read [references/beats.md](references/beats.md) — word counts, beat rules, anti-patterns
- Read [references/copywriting.md](references/copywriting.md) — DR formulas, hook patterns, UGC style, proof formats

**Language:** Match the target market, not the AI default. German product = German script. Colloquial German, not formal. If the brand name, domain, or Instagram handle is German, write in German. See the "UGC / Social Media Style" section in copywriting.md.

**Ad goal awareness — determine BEFORE scripting:**
- **Product page CTA** (driving to a landing page/shop): No social follow blocks. CTA is "link below / tap here / swipe up." Urgency comes from offer + scarcity.
- **Social follow CTA** (driving followers): Use `hf/instagram-follow` or `hf/tiktok-follow`. CTA is "follow for more." These are rare — most DR ads drive to product pages.
- **When unsure:** Ask the user. A 25% discount CTA with a landing page URL is always a product-page CTA.

### Templates

| Template | Duration | Beat sequence |
|---|---|---|
| `classic_dr` | 45s / 7 beats | hook → problem → agitate → why_others_fail → mechanism → proof → cta |
| `problem_solution` | 30s / 6 beats | hook → problem → value_prop → proof → objection → cta |
| `ugc_style` | 25s / 5 beats | hook → proof → mechanism → objection → cta |
| `short_hook` | 15s / 3 beats | hook → mechanism → cta |

### Example — 30s skincare (English)

```json
[
  { "beat_type": "hook",       "vo_text": "Your skincare routine is making it worse." },
  { "beat_type": "problem",    "vo_text": "If you're washing your face twice a day and still breaking out, it's not your fault." },
  { "beat_type": "value_prop", "vo_text": "Balanced Barrier Serum resets your skin's pH in 7 days — no harsh chemicals." },
  { "beat_type": "proof",      "vo_text": "8,400 five-star reviews. Dermatologist-tested. And it shows." },
  { "beat_type": "objection",  "vo_text": "No fragrance. No parabens. 60-day money-back guarantee." },
  { "beat_type": "cta",        "vo_text": "Tap below. 20% off your first order — today only." }
]
```

### Example — 25s German fitness supplement (UGC style)

```json
[
  { "beat_type": "hook",       "vo_text": "Ich hab monatelang trainiert und bin kein Stück weitergekommen." },
  { "beat_type": "problem",    "vo_text": "Jedes Training alles gegeben, Protein getrackt, genug geschlafen. Trotzdem stagniert." },
  { "beat_type": "mechanism",  "vo_text": "Bis mir jemand erklärt hat: du lädst einfach falsch nach. Kreatin HCL lädt direkt in die Zelle — keine Einlagerungen." },
  { "beat_type": "proof",      "vo_text": "Ich hab's einen Monat probiert. Nach zwei Wochen hat sich was verändert. Das ist kein Placebo." },
  { "beat_type": "cta",        "vo_text": "Link im Bio. 25% auf das 3er-Pack — verkauft sich schnell." }
]
```

---

## Step 2 — Create Video

Call `dr_voice_list` first to select the voice. Then:

```typescript
dr_video_create({
  title: "Product Name — Hook Variant 1",
  template: "problem_solution",
  beats: [ /* full script from Step 1 */ ],
  voice_id: "...",       // from dr_voice_list
  caption_style: "pop"   // pop (default) | classic | highlight
})
```

Returns: `videoId`, `editorUrl`, `beats[]` with IDs → store all for next steps.

See [references/voice.md](references/voice.md) for voice selection logic and ElevenLabs model settings.

---

## Step 3 — Generate TTS

Call `dr_beat_tts` for EACH beat **sequentially** (not parallel — rate limits apply):

```typescript
// For each beat:
dr_beat_tts({ video_id: "...", beat_id: "..." })
```

Returns `audioUrl` + `durationMs` per beat. Total should sum to 15–45 seconds. Report the sum to the user.

---

## Step 4 — B-Roll

Call `dr_broll_suggest` per beat for media options. See [references/broll.md](references/broll.md) for strategy by beat type and source priority.

```typescript
dr_broll_suggest({ beat_type: "problem", vo_text: "still breaking out every morning..." })
```

Returns results ranked: library (own B-Roll) → unsplash → pexels. Report URLs to user — they assign in editor.

---

## Step 5 — Overlay Blocks

Call `dr_blocks_list` to see all available blocks. Add with `dr_overlay_add`.

See [references/blocks.md](references/blocks.md) for full block selection guide and props reference.

### Quick reference — always use these

| Beat | Block ID | When to use |
|---|---|---|
| hook | `dr/hook-bigtext-pop` | Always — pattern interrupt |
| mechanism/value_prop | `dr/solution-product-reveal` | Always — benefit bullets |
| cta | `dr/cta-button-pulse` | **ALWAYS on CTA beat** |
| hook + cta | `dr/fx-grain-overlay` | Always — premium texture |
| proof (social) | `hf/instagram-follow` or `hf/tiktok-follow` | **Only** if: (1) ad goal is social follow OR (2) brand has 10K+ followers AND it reinforces trust |
| proof (review) | `dr/social-proof-reviews` | When you have a real verbatim testimonial quote |

```typescript
dr_overlay_add({
  video_id: "...",
  block_id: "hf/instagram-follow",
  props: { handle: "@brand", followers: "47.5K followers", displayName: "Brand Name" },
  timing: { mode: "beat-relative", beat_id: "<proof beat id>", at_ms: 500, duration_ms: 3500 },
  track_index: 1
})
```

Fill ALL props with real brand data from the questionnaire. Never use placeholder values.

---

## Step 6 — Transitions

Add transitions AFTER overlays. See [references/blocks.md](references/blocks.md) for the full transition catalog.

```typescript
dr_transition_add({
  video_id: "...",
  from_beat_id: "<hook beat id>",
  to_beat_id: "<problem beat id>",
  block_id: "hf/flash-through-white"
})
```

**Limit: 2–3 transitions per video.** Don't over-transition. Before choosing, read [references/blocks.md](references/blocks.md) — it has the visual/emotional description of each transition so you can choose what fits the ad's energy.

| Boundary | Block | What it does |
|---|---|---|
| hook → problem | `hf/flash-through-white` | Luminance burst to white — viewer blinks. Shock. Aggression. |
| problem → agitate | `hf/whip-pan` | Motion blur horizontal sweep. Kinetic escalation. |
| agitate → mechanism | `hf/cinematic-zoom` (**max 1x**) | Scale collapse + blur. Revelation. The climax moment. |
| mechanism → proof | `hf/crossfade` | Opacity dissolve, no motion. Continuity. Trust. |
| proof → cta | `hf/blur-through` | Full abstraction then resolve. Soft drift. The exhale before the ask. |

---

## Step 7 — Editor Link

After video + TTS + overlays + transitions, give the user the link. **Do NOT auto-render.**

```
Video ready for review: <editorUrl>

Built:
- [X] beats with voiceover (total ~Xs)
- [X] overlays
- [X] transitions
- B-Roll: needs assignment in editor (paste URLs from step 4)

Ready to render? Confirm and I'll start it.
```

---

## Step 8 — Render (confirmed only)

Call `dr_video_render` only after explicit user confirmation.

```typescript
dr_video_render({ video_id: "..." })
```

Then poll `dr_video_get` every 10s until `status === 'rendered'`. Return `render_url`.

---

## Product Awareness

Every tool call must use real data from the questionnaire:
- **VO text**: mirror the target audience's exact language, name their specific pain
- **Overlays**: real follower counts, real testimonial quotes, real offer text
- **B-Roll**: own B-Roll for product/proof/mechanism beats; stock acceptable for agitate/context
- **CTA**: the specific offer + urgency from the questionnaire

Generic placeholders = bad ad. Specificity = conversion.

---

## You Do This Wrong

- Calling `dr_video_render` without confirmation
- VO text over the beat's word count (timing breaks)
- Using `hf/cinematic-zoom` more than once (it's the climax, use it once)
- Adding `dr/cta-button-pulse` on non-CTA beats
- More than 3 overlays per beat (cluttered)
- Naming the product in the mechanism beat (name the mechanism, not the product)
- Using stock B-Roll for mechanism/proof beats (requires own B-Roll)
- Missing `dr_beat_tts` for a beat (renders silently)
- Calling tools in parallel (TTS calls must be sequential — rate limits)
- Placeholder text in overlay props (handle, displayName, offer)
- Writing English for a German-market product. Brand domain ends in .de or Instagram is in German → write German, colloquial, not formal.
- Adding `hf/instagram-follow` for a product-page CTA ad. The viewer goes to a shop link — showing a follow badge confuses the CTA.
- Adding `hf/instagram-follow` when follower count is under 10K. Weak numbers destroy trust.
- Choosing a transition based on its name rather than its visual/emotional meaning. Read the transition descriptions in blocks.md.

---

## References (loaded on demand)

- **[references/beats.md](references/beats.md)** — Beat word counts, timing rules, niche examples, anti-patterns. Read when scripting.
- **[references/copywriting.md](references/copywriting.md)** — DR formulas, hook patterns, mechanism naming, proof formats, CTA structures. Read when writing hooks or need copy inspiration.
- **[references/blocks.md](references/blocks.md)** — Full block catalog, props reference, timing recommendations. Read when adding overlays or transitions.
- **[references/broll.md](references/broll.md)** — B-Roll strategy per beat, source priority, what to look for. Read when suggesting B-Roll.
- **[references/voice.md](references/voice.md)** — Voice selection by niche, ElevenLabs model settings, speed by beat type. Read when selecting voice or troubleshooting TTS.
- **[palettes/beauty-wellness.md](palettes/beauty-wellness.md)** — Pastel, clean, skin tones.
- **[palettes/fitness-energy.md](palettes/fitness-energy.md)** — Bold, high-contrast, red/black.
- **[palettes/finance-coaching.md](palettes/finance-coaching.md)** — Dark, premium, navy/gold.
- **[palettes/tech-saas.md](palettes/tech-saas.md)** — Dark, minimal, accent neon.
- **[palettes/youth-entertainment.md](palettes/youth-entertainment.md)** — Electric, loud, saturated.
- **[examples/skincare-30s.md](examples/skincare-30s.md)** — Full worked example: 30s skincare, female audience.
- **[examples/fitness-45s.md](examples/fitness-45s.md)** — Full worked example: 45s supplement, male audience.
