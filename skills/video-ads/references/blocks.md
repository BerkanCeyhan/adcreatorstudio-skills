# Blocks Reference

## You Do This Wrong

- You add `dr/cta-button-pulse` to hook or proof beats. It belongs only on the CTA beat.
- You use `hf/cinematic-zoom` twice. It's the climax transition. Use it once, at the biggest emotional pivot (usually agitate → mechanism).
- You pile 5 blocks on one beat. Max 3 overlays per beat or it looks cluttered.
- You leave overlay props empty or with placeholder text. Fill every field with real brand data.
- You skip `dr/fx-grain-overlay` and wonder why the ad looks flat. Add it to hook and CTA beats.
- You add `hf/instagram-follow` for brands with under 10K followers. Weak follower count destroys trust instead of building it. Only use if the number is impressive.
- You add `hf/instagram-follow` on product-page CTA ads. If the viewer is going to a shop link, showing "follow us" confuses the call to action.

## Extensibility

Blocks are served dynamically by `dr_blocks_list`. Always call it before Step 5 — do NOT rely on memory of what blocks exist. New blocks appear with `drUseCase` and `defaultProps` in the response. Use those fields as the primary signal for how to use a new block:
- `drUseCase` — tells you which beat type and goal it serves
- `defaultProps` — shows all settable fields with example values
- `transitionEngine: "shader"` vs `"css"` vs `null` — `null` = overlay block (not transition), `"shader"` = WebGL transition (more dramatic), `"css"` = lighter CSS transition
- `transitionFamily` — groups transitions by visual feel (dissolve, push, scale, distortion, light, etc.)

---

## Block Selection Guide

Each block has a visual behavior. Know what it looks like before choosing it.

| Beat | Block ID | Props to set | What it looks like | Notes |
|---|---|---|---|---|
| hook | `dr/hook-bigtext-pop` | `headline`, `kicker` | Large headline punches onto screen with a quick scale pop. Kicker appears above in smaller weight. Full-frame text dominance. | Main text interrupt |
| hook (question) | `dr/hook-question-zoom` | `question`, `eyebrow` | Question text zooms in from slightly small, settles. Eyebrow above in subdued color. Conversational frame. | For question-style hooks |
| problem | `hf/macos-notification` | `appName`, `title`, `body` | Realistic macOS notification card (rounded rect, app icon, title + body) slides in from top-right corner. Viewer reads it like a real alert. | Use as pattern interrupt |
| agitate | `dr/agitation-word-highlight` | `word` (pain word), `caption` | Single pain word with a yellow marker bar sweeping left-to-right (scaleX 0→1). Caption text below. Feels like a teacher underlining the most important word on a whiteboard. | Highlight the pain word |
| social proof | `hf/instagram-follow` | `handle`, `followers`, `displayName`, `avatarUrl` | Instagram follow card: circular avatar left, display name + follower count right, blue "Follow" button. Slides up from bottom. Looks like native Instagram UI. | Only if 10K+ followers AND goal is social proof (not product-page CTA) |
| social proof | `hf/tiktok-follow` | `handle`, `followers`, `displayName` | TikTok-style follow prompt: username + follower count, red Follow button. Same native-app feel as the instagram block. | Only if 10K+ followers AND goal is social follow |
| proof | `dr/social-proof-reviews` | `quote`, `name`, `stars` | White card with 5 filled stars, verbatim quote in bold, name below in lighter weight. Clean review format — no clutter. | Real verbatim quote |
| proof | `hf/yt-lower-third` | `channelName`, `subscribers` | YouTube-style lower-third: channel logo area + name + subscriber count in a horizontal bar. Recognizable as a YouTube-credibility signal. | If YouTube-style proof |
| mechanism/value_prop | `dr/solution-product-reveal` | `title`, `bullet1`, `bullet2`, `bullet3` | Product card with a title line and 3 bullet points appearing in stagger. Each bullet animates in with a short delay. Feels like a feature reveal. | 3 benefit bullets |
| cta | `dr/cta-button-pulse` | `cta`, `offer` | Rounded button with CTA text, pulsing glow ring expanding outward on loop. Offer text below in smaller size. Hard to ignore. | **Required on CTA** |
| hook + cta | `dr/fx-grain-overlay` | (no props) | Subtle film grain noise over the full frame on every frame. Not visible as a "thing" — it makes the scene feel like footage, not a rendered ad. | Always add to hook and CTA beats |

---

## Timing Patterns

All overlays use `beat-relative` timing. Appear 300–500ms after beat starts. Duration = beat duration minus 500ms.

```typescript
dr_overlay_add({
  video_id: "...",
  block_id: "hf/instagram-follow",
  props: {
    handle: "@brandname",
    followers: "47.5K followers",
    displayName: "Brand Name",
    avatarUrl: "https://..." // optional, leave out if none
  },
  timing: {
    mode: "beat-relative",
    beat_id: "<beat id>",
    at_ms: 500,        // appear 0.5s after beat starts
    duration_ms: 3500  // show for 3.5s
  },
  track_index: 1
})
```

### Timing by block type

| Block type | at_ms | duration_ms |
|---|---|---|
| Hook text pop | 0 | full beat |
| Social follow | 500 | 3500 |
| Review quote | 300 | 4000 |
| Product reveal | 800 | beat remaining |
| CTA pulse | 200 | full beat |
| Grain overlay | 0 | full beat |
| Agitation highlight | 200 | 2000 |

---

## Transition Catalog

Call `dr_blocks_list` to see all available transitions (category = "transition"). The list is dynamic — always call it fresh, don't rely on memory.

**Max 2–3 transitions per video.** Use fewer on short videos (15–25s). Pick ONE primary (used 2x) + one accent (used 1x at the biggest moment).

### What each transition communicates

Understanding the visual feel and emotional meaning of each transition is required to choose correctly. Wrong transition = wrong emotion at the wrong moment.

#### High-energy (use for hook, interruptions, urgency)

| Block ID | Visual | Feel | DR Moment |
|---|---|---|---|
| `hf/flash-through-white` | Scene overexposes to pure white luminance burst. Nothing bleeds through. The viewer blinks. | Shock. Pattern interrupt. Aggression. | hook → problem. Most powerful opener. |
| `hf/flash-cut` | White flash at 1.8s. Brain barely registers the cut. | Ultra-fast disruption. Urgency. | Hook to anything when the hook is very short (<3s). |
| `hf/glitch` | RGB channels split apart (±30-60px), scene jitters, scan lines. Posterization. | Digital chaos. System instability. | Agitate beats. Use sparingly — once max. |
| `hf/whip-pan` | Horizontal motion blur sweeps like a camera snap. Creates velocity. | Kinetic shift. Things are moving fast. | problem → agitate. Escalation without break. |

#### Medium-energy (use for pivots, structural beat changes)

| Block ID | Visual | Feel | DR Moment |
|---|---|---|---|
| `hf/cinematic-zoom` | Old scene shrinks with blur, new zooms in from behind. Scale collapse. | Revelation. Dramatic. Time slows. | agitate → mechanism. **Use max 1x — this is the climax.** |
| `hf/chromatic-aberration` | RGB channels spread apart then converge on new scene. 0.3s. | Optical distortion. Edge, tension. | problem → agitate alternative. |
| `hf/push-slide` | Horizontal push: old exits left, new enters right. | Forward movement. Next chapter. | Between structured beats. |
| `hf/zoom-through` | Old zooms past camera + blurs, new zooms in from behind. | Forward thrust. Energy. | Problem → solution pivot. |

#### Low-energy (use for trust moments, proof, CTA approach)

| Block ID | Visual | Feel | DR Moment |
|---|---|---|---|
| `hf/blur-through` | Scene fully abstracts (30px blur + 8% scale) before resolving. Heaviest blur. | "Drift with me." Calm transition. Soft thought. | proof → CTA. The exhale before the ask. |
| `hf/crossfade` | Opacity dissolve. Both scenes visible for 0.4s. No motion. | "This continues." Continuity. Trust. | mechanism → proof. Smooth trust pass. |
| `hf/blur-crossfade` | Opacity dissolve with slight blur. Softer than crossfade. | Gentle. Emotional. Soft handoff. | Any calm moment. |
| `hf/color-dip` | Dip to a color between beats. Clean, minimal pause. | Intentional pause. Breath. Structure. | Formal topic change. |
| `hf/focus-pull` | Blur in, color shift, blur out. Like racking focus on a camera. | Cinematic calm. Premium. | Beauty/wellness/premium products. |

#### Dramatic / rare (use maximum once — reserved for hero moments)

| Block ID | Visual | Feel | DR Moment |
|---|---|---|---|
| `hf/gravity-drop` | Old scene falls straight down (y +1200px, slight 4° rotation), new enters from above. Heavy physical weight. | Collapse. Gravity taking over. Final. | Extreme agitate → mechanism when the failure feels physical and total. Rare. |
| `hf/page-burn` | Organic fire front creeps across the frame, consuming the old scene. New scene revealed underneath as ash. No falling debris — pure edge geometry. | Destruction. Maximum drama. Catharsis. | Reserve for the single most dramatic pivot in the entire video. One use ever. Never on calm ads. |
| `hf/morph-circle` | A circle scales from center: 0 → fills entire frame → collapses to reveal new scene. | Playful, bold, graphic. Confident pop. | Youth/gaming/entertainment. Energetic product reveals. Not for serious DR. |
| `hf/3d-card-flip` | Frame rotates 180° on Y-axis like flipping a physical card. Physical, tactile. | "The other side of the story." Reveal. Before/after. | Objection handling. Can flip from "the problem" to "the fix" literally. 1x max. |

#### Structured / designed (use for editorial or high-design ads)

| Block ID | Visual | Feel | DR Moment |
|---|---|---|---|
| `hf/push-slide` | Horizontal push: old exits left, new enters right together. Both move at same time. | "Next chapter." Forward movement. Clean. | Between structured beats in finance/coaching templates. |
| `hf/staggered-blocks` | Color panels slide across the frame in staggered sequence, then wipe away in reverse. Rhythmic, designed. | "This was designed." Structure. Craft. | Mechanism or proof beat when the product has a designed/premium feel. Not for UGC style. |
| `hf/shutter` | Two horizontal halves close from top and bottom, meet in middle, then open to reveal new scene. Like a camera shutter or stage curtain. | Mechanical reveal. Anticipation. | Mechanism reveal when you want theatrical buildup. Finance/tech. |
| `hf/clock-wipe` | Radial wipe sweeps clockwise from 12 o'clock across the frame. Feels like time moving. | Transition of time. "Meanwhile." | Before/after time-gap beats. "After 30 days..." |
| `hf/circle-iris` | Circle grows from center, iris-in style, to reveal new scene. Classic film technique. | Warm. Familiar. Cinematic. | Proof beats with heritage/nostalgia feel. Wellness, food. |
| `hf/diagonal-split` | Frame splits diagonally — old scene slides off one corner, new slides in from opposite. | Bold. Dynamic. Angled energy. | Hook to problem in high-energy fitness/gaming ads. Alternative to flash. |
| `hf/grid-dissolve` | Frame breaks into grid cells; cells fade out staggered revealing new scene. Like pixels dissolving. | Technical. Digital. Disintegration. | Tech/SaaS/AI ads. Agitate to mechanism when the old system is broken. |

### Visual Style × DR Niche

The HyperFrames visual style system maps directly to DR ad niches. When selecting overlays and transitions, match the style energy to the niche.

| DR Niche | Visual Style | Energy | Avoid |
|---|---|---|---|
| Fitness / supplements | Maximalist Type | High — kinetic, full-frame type, hard cuts | Soft dissolves, pastel |
| Beauty / skincare / wellness | Soft Signal | Medium-low — drifts, floats, warmth | Glitch, hard flash, glitch |
| Finance / coaching / high-ticket | Velvet Standard or Shadow Cut | Medium — slow reveals, dark premium, authority | Flash-cut, glitch, morph-circle |
| Tech / SaaS / AI | Swiss Pulse or Data Drift | Medium — geometric, precision, accent neon | Page-burn, morph-circle |
| Youth / gaming / entertainment | Deconstructed or maximalist | High — glitch artifacts, electric, oversaturated | Focus-pull, blur-crossfade |
| Food / consumer / lifestyle | Folk Frequency | Medium — bounce, warmth, organic | Grid-dissolve, 3D-card-flip |

Use this to choose: which blocks look right (grain overlay fits Maximalist Type, focus-pull fits Soft Signal), which transitions fit (glitch + flash for Deconstructed, crossfade + focus-pull for Soft Signal).

### Recommended per beat boundary

| Boundary | Primary | Alternative | Avoid |
|---|---|---|---|
| hook → problem | `hf/flash-through-white` | `hf/flash-cut` | Slow dissolves (wrong energy) |
| problem → agitate | `hf/whip-pan` | `hf/chromatic-aberration` | `hf/cinematic-zoom` (save for climax) |
| agitate → mechanism | `hf/cinematic-zoom` (**max 1x**) | `hf/zoom-through` | Any dissolve (too soft) |
| mechanism → proof | `hf/crossfade` | `hf/blur-crossfade` | Glitch (wrong tone for trust) |
| proof → cta | `hf/blur-through` | `hf/crossfade` | Flash (too aggressive) |

### Transition selection by ad energy

| Ad type | Energy | Primary | Accent |
|---|---|---|---|
| High-conversion fitness/supplement | High | `hf/flash-through-white` | `hf/cinematic-zoom` |
| Beauty/wellness | Medium | `hf/crossfade` | `hf/focus-pull` |
| Finance/coaching/premium | Medium | `hf/blur-through` | `hf/cinematic-zoom` |
| Gaming/youth | High | `hf/glitch` | `hf/flash-cut` |

### Transition call

```typescript
dr_transition_add({
  video_id: "...",
  from_beat_id: "<beat id>",
  to_beat_id: "<next beat id>",
  block_id: "hf/flash-through-white"  // choose based on visual/emotional need above
})
```

---

## Props Reference by Block

### `dr/hook-bigtext-pop`
```typescript
{
  headline: "Your skincare routine is making it worse",
  kicker: "⚠ Stop scrolling"  // optional
}
```

### `hf/instagram-follow`
```typescript
{
  handle: "@brandname",           // with @ prefix
  followers: "47.5K followers",  // formatted string with label
  displayName: "Brand Name",
  avatarUrl: "https://..."        // public image URL, optional
}
```

### `hf/tiktok-follow`
```typescript
{
  handle: "@brandname",
  followers: "47.5K",
  displayName: "Brand Name"
}
```

### `dr/social-proof-reviews`
```typescript
{
  quote: "I cleared my skin in 12 days. Nothing else worked.",
  name: "Sarah M.",
  stars: 5  // integer 1-5
}
```

### `dr/solution-product-reveal`
```typescript
{
  title: "The Barrier Reset Process",
  bullet1: "Rebuilds natural pH shield",
  bullet2: "Works in 7 days",
  bullet3: "No harsh chemicals"
}
```

### `dr/cta-button-pulse`
```typescript
{
  cta: "Tap below to shop",
  offer: "20% off — today only"
}
```

### `hf/macos-notification`
```typescript
{
  appName: "Skin Journal",
  title: "Another breakout",
  body: "Day 47. Nothing's working."
}
```

### `dr/agitation-word-highlight`
```typescript
{
  word: "tired",       // single pain word from VO
  caption: "So tired of trying everything"
}
```
