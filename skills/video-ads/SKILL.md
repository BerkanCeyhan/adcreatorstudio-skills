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

### Script Process — Full Monologue First

**DO NOT write beat-by-beat.** Write a single continuous monologue, then assign beats after.

**Phase 1 — Write the full monologue:**
Write the entire script as one person telling one story. No beat labels, no transitions announced. Natural spoken language. Paragraphs mark where the emotional focus shifts.

- Write as if the speaker is telling a friend — not reading a list
- Each paragraph flows from the previous one without announcing the topic change
- The CTA should feel like a natural conclusion, not a sales pitch

**Phase 2 — Read it aloud (mandatory):**
Read the full monologue aloud. If any sentence sounds choppy, disconnected, or announcement-style, rewrite it before proceeding. Fix issues now — TTS will make them worse.

**Phase 3 — TTS normalization (mandatory):**
Before assigning beats, clean the text:
- No em-dashes (—) — replace with comma or restructure
- No period-stacked fragments — connect with conjunctions
- Spell out numbers: "70%" → "siebzig Prozent" (DE) / "seventy percent" (EN)
- Spell out currency: "25€" → "fünfundzwanzig Euro"

**Phase 4 — Assign paragraphs to beat types:**
Label each paragraph with its beat type. Do NOT rewrite sentences during this step — just assign. The monologue must remain identical after assignment.

**Phase 5 — Add audio tags:**
Add 1–2 ElevenLabs v3 audio tags per beat. See [references/voice.md](references/voice.md) for which tags fit which beats. Place tag at the start of the beat's vo_text. Do not change the spoken text.

**Show the user the full monologue BEFORE calling any tool.** Get confirmation or iterate. Only call `dr_voice_list` + `dr_video_create` after the script is approved.

### Templates

| Template | Duration | Beat sequence |
|---|---|---|
| `classic_dr` | 45s / 7 beats | hook → problem → agitate → why_others_fail → mechanism → proof → cta |
| `problem_solution` | 30s / 6 beats | hook → problem → value_prop → proof → objection → cta |
| `ugc_style` | 25s / 5 beats | hook → problem → mechanism → proof → cta |
| `short_hook` | 15s / 3 beats | hook → mechanism → cta |

### Full example — German fitness supplement (UGC style, 30s)

**Phase 1 — Full monologue:**

> Ich hab Kreatin genommen und hab ausgesehen als hätte mich jemand mit einer Fahrradpumpe aufgepumpt. Gesicht aufgedunsen, Bauch gebläht, und irgendwann hab ich's einfach sein lassen.
>
> Dabei hab ich echt alles probiert. Mehr Wasser trinken, mit dem Essen nehmen... Hat alles nix gebracht. Weil keiner dieser Tipps das eigentliche Problem angeht.
>
> Normales Monohydrat löst sich kaum auf. Es liegt im Magen, zieht Wasser, und ein Großteil verlässt deinen Körper bevor überhaupt was in der Muskulatur ankommt. Kreatin HCL dagegen löst sich komplett auf, direkte Aufnahme, kein Blähbauch, kein aufgedunsenes Gesicht. Und statt fünf Gramm reichen vier Kapseln.
>
> Über tausend zweihundert Leute berichten genau dasselbe und sehen jetzt zum ersten Mal wirklich Ergebnisse.
>
> Wenn du Kreatin schon mal aufgegeben hast, dann ist das der Grund. Gerade gibt's das Dreier-Bundle mit fünfundzwanzig Prozent Rabatt. Link unten.

**Phase 4 — Beat assignment:**

```json
[
  { "beat_type": "hook",      "vo_text": "Ich hab Kreatin genommen und hab ausgesehen als hätte mich jemand mit einer Fahrradpumpe aufgepumpt. Gesicht aufgedunsen, Bauch gebläht, und irgendwann hab ich's einfach sein lassen." },
  { "beat_type": "problem",   "vo_text": "Dabei hab ich echt alles probiert. Mehr Wasser trinken, mit dem Essen nehmen... Hat alles nix gebracht. Weil keiner dieser Tipps das eigentliche Problem angeht." },
  { "beat_type": "mechanism", "vo_text": "Normales Monohydrat löst sich kaum auf. Es liegt im Magen, zieht Wasser, und ein Großteil verlässt deinen Körper bevor überhaupt was in der Muskulatur ankommt. Kreatin HCL dagegen löst sich komplett auf, direkte Aufnahme, kein Blähbauch, kein aufgedunsenes Gesicht. Und statt fünf Gramm reichen vier Kapseln." },
  { "beat_type": "proof",     "vo_text": "Über tausend zweihundert Leute berichten genau dasselbe und sehen jetzt zum ersten Mal wirklich Ergebnisse." },
  { "beat_type": "cta",       "vo_text": "Wenn du Kreatin schon mal aufgegeben hast, dann ist das der Grund. Gerade gibt's das Dreier-Bundle mit fünfundzwanzig Prozent Rabatt. Link unten." }
]
```

**Phase 5 — Audio tags added (final vo_text):**

```json
[
  { "beat_type": "hook",      "vo_text": "[direct] Ich hab Kreatin genommen und hab ausgesehen als hätte mich jemand mit einer Fahrradpumpe aufgepumpt. Gesicht aufgedunsen, Bauch gebläht, und irgendwann hab ich's einfach sein lassen." },
  { "beat_type": "problem",   "vo_text": "[empathetic] Dabei hab ich echt alles probiert. Mehr Wasser trinken, mit dem Essen nehmen... Hat alles nix gebracht. Weil keiner dieser Tipps das eigentliche Problem angeht." },
  { "beat_type": "mechanism", "vo_text": "[confident] Normales Monohydrat löst sich kaum auf. Es liegt im Magen, zieht Wasser, und ein Großteil verlässt deinen Körper bevor überhaupt was in der Muskulatur ankommt. Kreatin HCL dagegen löst sich komplett auf, direkte Aufnahme, kein Blähbauch, kein aufgedunsenes Gesicht. Und statt fünf Gramm reichen vier Kapseln." },
  { "beat_type": "proof",     "vo_text": "[warm] Über tausend zweihundert Leute berichten genau dasselbe und sehen jetzt zum ersten Mal wirklich Ergebnisse." },
  { "beat_type": "cta",       "vo_text": "[excited] Wenn du Kreatin schon mal aufgegeben hast, dann ist das der Grund. Gerade gibt's das Dreier-Bundle mit fünfundzwanzig Prozent Rabatt. Link unten." }
]
```

---

## Step 2 — Select Voice + Create Video

Call `dr_voice_list`. Filter by target language first. Then apply priority order from [references/voice.md](references/voice.md): cloned > professional > premade.

**Present max 3 options to the user** with name, category, and preview link. Format:

```
Voice options for this ad:

1. **[Name]** (cloned) — your own voice, most authentic for UGC
   Preview: [preview_url]

2. **[Name]** (professional, female, conversational) — warm lifestyle tone
   Preview: [preview_url]

3. **[Name]** (professional, male, energetic) — fitness/supplement energy
   Preview: [preview_url]

Which would you like? (reply with 1/2/3 or voice name)
```

Wait for user confirmation before calling `dr_video_create`.

Then:

```typescript
dr_video_create({
  title: "Product Name — Hook Variant 1",
  template: "ugc_style",
  beats: [ /* approved script from Step 1 with audio tags */ ],
  voice_id: "...",       // confirmed by user
  caption_style: "pop"   // pop (default) | classic | highlight
})
```

Returns: `videoId`, `editorUrl`, `beats[]` with IDs → store all for next steps.

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

**Priority order:**
1. User's own uploaded assets (`category: "library"`) — always first. If the user provided B-Roll URLs or answered `own_broll_available: true` in the questionnaire, assign those beats before considering stock.
2. Unsplash / Pexels stock — acceptable for agitate/context beats only.

Never use stock for mechanism or proof beats — those require real product footage. Report URLs to user — they assign in editor.

---

## Step 5 — Overlay Blocks

Call `dr_blocks_list` to see all available blocks. Add with `dr_overlay_add`.

See [references/blocks.md](references/blocks.md) for full block selection guide and props reference.

### Purpose-first thinking

Each overlay must answer: **"What does this block DO for this specific moment?"** If you can't answer clearly, don't add it.

| Moment | Purpose | Block | Condition |
|---|---|---|---|
| Hook opens | Pattern interrupt — force attention | `dr/hook-bigtext-pop` | Always |
| Mechanism/value_prop | Amplify the solution — make benefit visual | `dr/solution-product-reveal` | When product has clear bullet benefits |
| CTA | Drive the action — make clicking feel urgent | `dr/cta-button-pulse` | Always on CTA beat |
| Hook or CTA | Premium cinematic feel | `dr/fx-grain-overlay` | When the ad has a premium/lifestyle tone |
| Proof (social follow) | Show community size as trust signal | `hf/instagram-follow` or `hf/tiktok-follow` | **Only** if goal = social follow OR 10K+ followers |
| Proof (testimonial) | Show specific social proof | `dr/social-proof-reviews` | Only with a real verbatim quote |

**Overlay planning process:**
1. List each beat
2. For each beat, ask: "Is there a specific visual amplification this beat needs?"
3. If yes: which block serves that purpose?
4. If no: add nothing — a clean B-Roll beat is better than a cluttered overlay beat

Max 3 overlays per beat. But most beats should have 0–1.

```typescript
dr_overlay_add({
  video_id: "...",
  block_id: "hf/instagram-follow",
  props: { handle: "@brand", followers: "47.5K followers", displayName: "Brand Name" },
  timing: { mode: "beat-relative", beat_id: "<proof beat id>", at_ms: 500, duration_ms: 3500 },
  track_index: 1
})
```

Fill ALL props with real brand data from the questionnaire. Never use placeholder values. Every prop field the block exposes must be populated — an empty or default-placeholder prop renders broken in the editor.

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

### Emotional Arc Thinking (not mechanical placement)

Transitions serve emotional pivots — moments where the viewer's mental state shifts. Ask: **"What changes in the viewer's feeling at this boundary?"**

Every ad has 2–3 real pivots. Find them first, then choose a transition that matches the energy of THAT shift:

| Emotional shift | Pivot type | Right transition |
|---|---|---|
| Interrupt → Recognition (hook→problem) | **The hook lands** — viewer says "wait, that's me" | `hf/flash-through-white` — shock, aggression |
| Problem → Escalation (problem→agitate) | **The pain deepens** — frustration stacks | `hf/whip-pan` — kinetic, building urgency |
| Stuck → Revelation (agitate/problem→mechanism) | **The turn** — from darkness to answer | `hf/cinematic-zoom` — climax, use max once |
| Explanation → Trust (mechanism→proof) | **Belief builds** — facts settle in | `hf/crossfade` — continuity, no drama |
| Trust → Action (proof→cta) | **The exhale** — soft transition into the ask | `hf/blur-through` — resolve, invitation |

**Decision process:**
1. Map the ad's emotional arc beat by beat (1–2 sentences per beat: "viewer feels...")
2. Find where the feeling shifts hardest (usually 2–3 moments)
3. Place one transition per real pivot — not at every beat boundary
4. If a boundary has no emotional shift (two beats of the same emotional state), no transition

**Limits are consequences, not rules:**
- Short ads (≤25s) typically have 1–2 real pivots max
- `hf/cinematic-zoom` can only be the climax moment — if your ad has no agitate beat or no single revelation moment, skip it
- Never stack two high-energy transitions adjacent to each other (flash + whip-pan in sequence = chaotic, not powerful)

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
- Writing period-stacked fragments in VO: "X. Y. Z." = three TTS hard stops. Connect with commas or conjunctions.
- Using em-dashes in VO text. TTS restarts delivery at the dash. Replace with comma or restructure.
- Leaving numbers as digits: "70%" → TTS reads inconsistently. Spell out in the script language before calling dr_video_create.
- Not adding audio tags. Every beat should have 1–2 Eleven v3 audio tags where they're contextually earned. Read voice.md for which tags fit which beats.
- Selecting a voice without checking for the user's own cloned voice first. Cloned voice = highest authenticity for UGC. It must be offered before any other option.
- Adding more transitions than the beat count warrants. ≤15s = 1 max. ≤25s = 2 max. Short videos with 3 transitions feel choppy, not cinematic.
- Leaving any overlay prop unfilled. Every exposed prop on a block must have real data — empty props = broken overlay in editor.

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
