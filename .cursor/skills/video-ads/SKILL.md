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

### Visual Director Contract

Before overlays/transitions, define a short visual direction. This is the editing brief the MCP calls must follow:

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
  "type": {
    "headline": "condensed heavy | humanist serif | precise grotesk | mono data",
    "tone": "warm | premium | aggressive | clinical"
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

Use HyperFrames discipline:
- Layout first, animation second. Place overlays where they are fully readable before choosing animation.
- Pick one visual identity and repeat it. Do not invent new colors per overlay.
- One primary transition family plus one accent. Never randomize every boundary.
- Video ad text must be readable in two seconds: fewer words, bigger type.
- Avoid AI design tells: generic purple/blue gradients, random glow blobs, centered everything, empty cards, placeholder copy.
- Keep 9:16 safe zones: important text within 64px left/right, avoid top 120px and bottom 180px unless intentionally using a CTA/lower-third.

### Performance Creative Rules

Use these rules for DR/VSL video structure:
- First frames must carry the value hook. TikTok performance guidance stresses a hook, unique selling points, and a clear CTA; do not spend the first seconds on brand intro.
- Make the video native to the platform: 9:16, safe-zone aware, visible product/person/context, and movement that feels like social footage rather than a web page.
- Use a simple argument arc: hook → problem/intent → mechanism → proof → value/offer → CTA.
- Create multiple creative variants by changing hook, CTA, headline, or core video asset. Google video-action guidance recommends multiple creative variants; do not treat one edit as final.
- Show the product or proof while making claims. Empty text-only claims feel weak unless used as a deliberate hook hit.
- CTA must tell the viewer what to do now. If urgency is used, it must be real.

### VSL Overlay Logic

When the ad is VSL-like, overlays should support the persuasion argument:
- Open loop / question: `dr/search-query-overlay`, `dr/hook-question-zoom`, `dr/punctuation-pop`.
- Problem proof: `dr/dm-screenshot`, `dr/instagram-comment`, `dr/tiktok-comment`, `dr/problem-split-compare`, `hf/reddit-post`, `hf/x-post`.
- Mechanism: `dr/step-path-goal` for process, `dr/solution-product-reveal` for product mechanism.
- Proof escalation: `dr/proof-ticker` for multiple short proof points, `dr/social-proof-reviews` for a verbatim quote, `hf/reddit-post` for community proof, `hf/x-post` for public creator/customer proof.
- Value justification: `dr/receipt-breakdown` for price, bonuses, savings, or time saved; `hf/apple-money-count` for a big money/result counter.
- Close: `dr/scarcity-countdown` only for real urgency, plus `dr/cta-button-pulse`.


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

Never use stock for mechanism or proof beats — those require real product footage.

Assign base beat media with `dr_beat_broll_assign`. This keeps the core rule intact: one beat has one primary background video/image.

```typescript
dr_beat_broll_assign({
  video_id: "...",
  beat_id: "<mechanism beat id>",
  media_url: "https://...",
  thumb_url: "https://...",
  media_type: "video"
})
```

Use `dr_media_clip_add` only for extra cutaways inside an already planned beat: product close-ups, proof screenshots, packshots, jump-cut inserts, or quick context shots. These media clips sit above base beat media, below captions/overlays/transitions, and are muted.

```typescript
dr_media_clip_add({
  video_id: "...",
  media_url: "https://...",
  thumb_url: "https://...",
  media_type: "video",
  timing: { mode: "beat-relative", beat_id: "<mechanism beat id>", at_ms: 1800, duration_ms: 1400 },
  source_in_ms: 1200,
  source_out_ms: 2600,
  playback_rate: 1,
  track_index: 1,
  fit: "cover",
  animation: "zoom_in"
})
```

Cutaway discipline:
- Use base B-Roll for the main beat image.
- Use media clips for short inserts, not for rewriting the beat structure.
- Usually 0-1 cutaway in a short beat, 1-3 in a long mechanism/proof beat.
- No media-to-media transitions yet. Use hard cuts inside beats. Beat transitions still belong between beats.

### Sound + Audio Design

HyperFrames rule: video elements are muted; audio is separate timeline media. In AdCreator, voiceover is generated per beat and overlays/transitions may carry their own deterministic `sound_effect`.

Use sound-aware editing:
- Put overlay entrances 100-300ms after a voiced punch word, not randomly at beat start.
- Use one visual hit per phrase: punch zoom, punctuation pop, word highlight, or flash transition. Do not stack all at once.
- For music-led ads, HyperFrames best practice is pre-extracted audio data driving subtle GSAP intensity. Keep it subtle: 3-6% scale, small glow/contrast changes, no equalizer bars, no generic waveforms, no music-note graphics.
- Treat transition choice as sound design: `flash-through-white` = blink/hit, `whip-pan` = whoosh, `cinematic-zoom` = reveal swell, `blur-through` = breath, `crossfade` = trust continuity, `glitch` = digital malfunction.
- Use `sound_effect` only when the visual moment implies a physical sound. Many proof/review beats should stay clean so VO stays premium.

Available SFX presets:

| Preset | Use |
|---|---|
| `soft-hit` | Big claim, product reveal, CTA arrival |
| `pop-click` | Bullet/check/comment entrance |
| `sub-tick` | Proof ticker, receipt row, countdown tick |
| `whoosh` | Push, whip, slide, directional movement |
| `flash-rise` | Flash, light leak, overexposure reveal |
| `glitch-tick` | Digital error/glitch/problem interruption |
| `cash-tick` | Price, savings, receipt/invoice moment |
| `breath` | Blur/crossfade/proof-to-CTA exhale |

Keep SFX volume subtle: `0.2-0.45`. Use negative `offsetMs` (`-80` to `-40`) for transitions so the sound anticipates the visual.


### Editing Moves Beyond Overlays

Use these when the VO has a punchline or statement:
- **Punch zoom:** on a strong claim, add a short `dr_media_clip_add` cutaway with `animation: "zoom_in"` or trim the source to the punch moment. Use for hook and agitate statements.
- **Cutaway insert:** when a beat needs more visual proof than one background clip can carry, add a 900-1800ms media clip at the spoken claim. Keep it muted and let VO carry the argument.
- **Blur-to-solid emphasis:** when footage is too busy for text, use a darker/glass overlay style or transition through `hf/color-dip` / `hf/blur-through` into a cleaner scene.
- **Statement hit:** pair a punch word overlay (`dr/agitation-word-highlight`) with a fast transition (`hf/flash-cut` or `hf/whip-pan`) only once or twice. Too many hits feel noisy.
- **Premium pause:** for luxury/finance/wellness, use `hf/focus-pull`, `hf/blur-crossfade`, glassmorphism, grain, and slower entrance timing. Do not use glitch/flash.


---

## Step 5 — Overlay Blocks

Call `dr_blocks_list` to see all available blocks. Add with `dr_overlay_add`.

**Overlay duration follows timeline.** If you set `duration_ms` to six seconds, the overlay remains visible for six seconds. Use longer holds for review/comment overlays; use short 1200-1800ms hits for punctuation and word highlights.

See [references/blocks.md](references/blocks.md) for full block selection guide and props reference.

### Purpose-first thinking

Each overlay must answer: **"What does this block DO for this specific moment?"** If you can't answer clearly, don't add it.

### Overlay Styling Contract

`dr_blocks_list` returns `defaultProps` and `propFields`. For DR-native overlays, always use these shared style props when available:

| Prop | Use |
|---|---|
| `accentColor` | Brand/action color. Use for hook badges, stars, bullets, CTA button. |
| `backgroundColor` | Card/glass/solid surface. Can be transparent `rgba(...)` for glassmorphism. |
| `textColor` | Primary readable text. Must contrast background. |
| `mutedTextColor` | Eyebrow/supporting text. Lower contrast but still readable. |
| `opacity` | Overall overlay opacity. Use `0.86-0.94` for glass, `1` for CTA/proof. |
| `scale` | Whole overlay size. Use `0.85-0.95` for lower-thirds, `1.05-1.2` for hook emphasis. |
| `position` | Use `top`, `middle`, or `bottom` first. Use `custom` only when setting exact `xPercent`/`yPercent`. |
| `fontFamily` | Use the selector value returned by `dr_blocks_list`; do not invent CSS stacks. |
| `introAnimation` / `outroAnimation` | Whole overlay entrance/exit. |
| `internalAnimation` | Motion inside the overlay, such as bullet stagger, like pop, upvote pop, or money burst. |

Good overlay presets:

```typescript
// Premium glassmorphism
{
  accentColor: "#c9a84c",
  backgroundColor: "rgba(16,18,24,0.72)",
  textColor: "#fffaf0",
  mutedTextColor: "rgba(255,250,240,0.70)",
  opacity: "0.94",
  scale: "0.92"
}

// Clean product-page DR
{
  accentColor: "#e85d04",
  backgroundColor: "#fff8f2",
  textColor: "#191410",
  mutedTextColor: "rgba(25,20,16,0.62)",
  opacity: "1",
  scale: "1"
}

// Bold fitness / launch
{
  accentColor: "#ff2f2f",
  backgroundColor: "rgba(10,10,10,0.88)",
  textColor: "#ffffff",
  mutedTextColor: "rgba(255,255,255,0.72)",
  opacity: "1",
  scale: "1.12"
}
```

Position logic:
- Hook: top or middle, full-frame dominance, `scale` 1.05-1.2.
- Agitation word: middle/bottom third, never cover face/product, `scale` 1.
- Review/social proof: bottom third, `scale` 0.85-1.0, glass or solid card.
- Product reveal: middle/top third, leave product visible.
- CTA: bottom third, large enough for thumb-stop, `scale` 1-1.15.

Bad practice:
- Do not use more than two overlay surface types in one ad.
- Do not use glassmorphism over busy footage unless adding `backgroundColor` alpha >= 0.72.
- Do not put tiny web-app typography in video. Overlay copy must be short and large.
- Do not use default placeholder styling if brand colors are known.


| Moment | Purpose | Block | Condition |
|---|---|---|---|
| Hook opens | Pattern interrupt — force attention | `dr/hook-bigtext-pop` | Always |
| Mechanism/value_prop | Amplify the solution — make benefit visual | `dr/solution-product-reveal` | When product has clear bullet benefits |
| CTA | Drive the action — make clicking feel urgent | `dr/cta-button-pulse` | Always on CTA beat |
| Hook or CTA | Premium cinematic feel | `dr/fx-grain-overlay` | When the ad has a premium/lifestyle tone |
| Proof (social follow) | Show community size as trust signal | `hf/instagram-follow` or `hf/tiktok-follow` | **Only** if goal = social follow OR 10K+ followers |
| Proof (testimonial) | Show specific social proof | `dr/social-proof-reviews` | Only with a real verbatim quote |
| Proof (public post) | Show native public proof | `hf/x-post` or `hf/reddit-post` | Use real post text/metrics or clearly paraphrased proof |
| Value/money result | Make a financial outcome feel large | `hf/apple-money-count` | Use real savings/revenue/value math, not fake earnings |

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
  track_index: 1,
  sound_effect: { presetId: "pop-click", volume: 0.28, offsetMs: 0 }
})
```

Fill required content props with real brand data from the questionnaire. Never use placeholder values. Optional props can be intentionally empty only when the overlay supports hiding that element, for example avatar URL, caption, eyebrow, kicker, or optional metrics.

---

## Step 6 — Transitions

Add transitions AFTER overlays. See [references/blocks.md](references/blocks.md) for the full transition catalog.

```typescript
dr_transition_add({
  video_id: "...",
  from_beat_id: "<hook beat id>",
  to_beat_id: "<problem beat id>",
  block_id: "hf/flash-through-white",
  sound_effect: { presetId: "flash-rise", volume: 0.36, offsetMs: -50 }
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
- [X] base B-Roll assignments
- [X] extra media cutaways, if useful
- [X] overlays
- [X] transitions

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
- Leaving required overlay props unfilled. Optional props may be empty only when the block hides that element cleanly.

---

## References (loaded on demand)

- **[references/beats.md](references/beats.md)** — Beat word counts, timing rules, niche examples, anti-patterns. Read when scripting.
- **[references/copywriting.md](references/copywriting.md)** — DR formulas, hook patterns, mechanism naming, proof formats, CTA structures. Read when writing hooks or need copy inspiration.
- **[references/blocks.md](references/blocks.md)** — Full block catalog, overlay styling contract, props reference, timing recommendations. Read when adding overlays or transitions.
- **[references/broll.md](references/broll.md)** — B-Roll strategy per beat, source priority, what to look for. Read when suggesting B-Roll.
- **[references/voice.md](references/voice.md)** — Voice selection by niche, ElevenLabs model settings, speed by beat type. Read when selecting voice or troubleshooting TTS.
- **[palettes/beauty-wellness.md](palettes/beauty-wellness.md)** — Pastel, clean, skin tones.
- **[palettes/fitness-energy.md](palettes/fitness-energy.md)** — Bold, high-contrast, red/black.
- **[palettes/finance-coaching.md](palettes/finance-coaching.md)** — Dark, premium, navy/gold.
- **[palettes/tech-saas.md](palettes/tech-saas.md)** — Dark, minimal, accent neon.
- **[palettes/youth-entertainment.md](palettes/youth-entertainment.md)** — Electric, loud, saturated.
- **[examples/skincare-30s.md](examples/skincare-30s.md)** — Full worked example: 30s skincare, female audience.
- **[examples/fitness-45s.md](examples/fitness-45s.md)** — Full worked example: 45s supplement, male audience.
