# B-Roll Strategy

## You Do This Wrong

- You suggest stock B-Roll for mechanism beats. Mechanism requires own B-Roll: product in use, ingredient macro, process detail. Stock footage of a random person using a generic product destroys credibility.
- You suggest Pexels over Unsplash. Prefer Unsplash for still images (higher quality, editorial feel). Prefer Pexels for motion clips. Both beat nothing — but own B-Roll always wins.
- You suggest the same shot style for every beat. Each beat has a different visual requirement.
- You ignore the `own_broll_available` field. If the user has their own footage, it must be used for product/proof/mechanism beats. Ask for URLs if they said yes but haven't provided them.

--- 

## Priority Order

```
1. Own B-Roll (library) — always preferred for product/proof/mechanism
2. Unsplash — editorial stills, lifestyle context, emotion beats
3. Pexels — motion clips, lifestyle video, context beats
```

For beats where the product isn't relevant (agitate, why_others_fail, objection), stock is fine.

---

## Strategy by Beat

| Beat | Own B-Roll | Stock acceptable | What to look for |
|---|---|---|---|
| **hook** | Strongly preferred | Yes (bold stock) | Fast movement, close-up, dramatic product shot, bold face |
| **problem** | Strongly preferred | Yes (authentic feel) | Relatable pain scenario, frustrated person, real life not staged |
| **agitate** | Optional | Yes | Chaos, stress, overwhelm, clutter, failure moments |
| **why_others_fail** | No | Yes | Generic product shelf, "bad solution" visual |
| **mechanism** | **Required** | No | Product in use, ingredient macro, process detail, close-up of key feature |
| **value_prop** | Preferred | Yes (aspirational) | After-state, transformation, happy outcome, result |
| **proof** | **Required** | No | Screenshots, UGC, before/after, real customer reaction |
| **objection** | Optional | Yes | Trust signals, clean packaging, premium feel, guarantee |
| **cta** | Preferred | Yes (product hero) | Product hero shot, pack shot, offer visual, clean product |

---

## B-Roll by Niche

### Skincare / Beauty
- hook: Close-up skin texture, morning routine, mirror reflection
- problem: Pore close-up, frustrated face, cluttered skincare shelf
- mechanism: Ingredient dropper, serum absorbing, microscope-style detail
- proof: Before/after split, skin transformation, glowing skin
- cta: Product flat lay, clean bottle on marble

### Fitness / Supplements
- hook: Training montage, lifting heavy, sweat close-up
- problem: Tired after gym, plateau moment, no results
- mechanism: Capsule/powder pouring, cellular animation style, product detail
- proof: Physique transformation, weight on scale, PR moment
- cta: Product pouch/bottle hero, shaker in hand

### SaaS / Productivity
- hook: Messy inbox, notification flood, overwhelmed at desk
- problem: Failed meeting, missed deadline, frustrated face
- mechanism: Dashboard UI, feature demo, workflow animation
- proof: Happy team, productivity chart, Slack reaction
- cta: App UI hero, clean dashboard screenshot

### Finance / Coaching
- hook: Dollar bills, stressed over finances, laptop + coffee
- problem: Debt spiral, rejected loan, confused spreadsheet
- mechanism: Chart going up, financial model, clean dashboard
- proof: Client transformation, testimonial screenshot, before/after numbers
- cta: Premium office, confident person, product/program hero

---

## B-Roll API

Always pass `locale` matching the VO language. Server LLM-rewrites the VO into concrete nouns for the correct language — do not prepend camera adjectives like "bold" or "dramatic".

```typescript
dr_broll_suggest({
  beat_type: "problem",
  vo_text: "Immer noch Pickel trotz zweimal waschen jeden Morgen...",
  locale: "de-DE"  // always pass — match VO language
})
```

Returns:
```json
{
  "keywords": "Gesicht waschen Pickel Haut",
  "locale": "de-DE",
  "results": [
    {
      "id": "...",
      "media_type": "image" | "video",
      "media_url": "https://...",
      "thumb_url": "https://...",
      "source": "media_library" | "library" | "unsplash" | "pexels",
      "title": "...",
      "reason": "..."
    }
  ]
}
```

- `source: "media_library"` = user's own uploads (highest priority — always pick first)
- Results ranked: media_library → library → unsplash/pexels
- Pexels returns up to 20 results re-ranked by locale-correct query — trust top result unless thumbnail clearly off

## Base B-Roll vs Extra Cutaways

Use two different media tools:

1. `dr_beat_broll_assign` — sets the main background media for a beat. One per beat.
2. `dr_media_clip_add` — adds extra muted cutaways inside the beat (close-ups, proof inserts, jump-cuts). Short: 900–1800ms each.

### Animation Rules (non-negotiable)

**Images MUST have animation.** Never pass `animation: "none"` on an image clip unless intentional.

Alternate between animation styles:
```
beat 0 (image) → ken_burns
beat 1 (image) → zoom_in
beat 2 (image) → pan_left
beat 3 (image) → zoom_out
(repeat)
```

Server auto-defaults image animation if you omit it, but explicit is better — pass it every time.

### Fill Policy for Short Videos

When a video clip source is shorter than the beat audio duration, set `fill_policy`:

```typescript
dr_media_clip_add({
  ...,
  media_type: "video",
  timing: { mode: "beat-relative", beat_id: "...", at_ms: 0, duration_ms: 18000 },
  source_duration_ms: 9000,   // tell server the source is 9s
  fill_policy: "loop"         // loop | freeze_last | speed_down
})
```

- `loop` — video loops seamlessly (default choice for lifestyle footage)
- `freeze_last` — holds last frame (use for product shots at end of beat)
- `speed_down` — slows video to fill duration (use when slight slow-mo enhances mood)

`dr_video_lint` will warn `broll_too_short` if source < clip duration without a fill_policy set.

```typescript
dr_beat_broll_assign({
  video_id: "...",
  beat_id: "<beat id>",
  media_url: "https://...",
  thumb_url: "https://...",
  media_type: "video"
})

dr_media_clip_add({
  video_id: "...",
  media_url: "https://...",
  thumb_url: "https://...",
  media_type: "image",
  timing: { mode: "beat-relative", beat_id: "<beat id>", at_ms: 1400, duration_ms: 1200 },
  track_index: 1,
  fit: "cover",
  animation: "zoom_in"   // always set on images
})
```

**Report to user:** Tell them which base B-Roll was assigned and which extra cutaways were added, with URLs/source labels.

---

## When User Has Own B-Roll

If `own_broll_available = true` but URLs weren't provided:

> "You mentioned you have your own B-Roll. For best results on the mechanism and proof beats, I need the direct URLs. Can you paste them here or upload via the editor?"

Don't proceed with stock-only suggestions for those beats until the user provides their footage or explicitly says to skip.
