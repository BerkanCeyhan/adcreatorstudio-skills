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

```typescript
dr_broll_suggest({
  beat_type: "problem",
  vo_text: "still breaking out every morning even after washing twice..."
})
```

Returns:
```json
{
  "results": [
    {
      "id": "...",
      "media_type": "image" | "video",
      "media_url": "https://...",
      "thumb_url": "https://...",
      "source": "ai_pick" | "unsplash" | "pexels",
      "title": "...",
      "reason": "..."
    }
  ],
  "keywords": "frustration stress skin breakout washing face"
}
```

- `source: "ai_pick"` = from own library (highest priority — always show first)
- Results ranked: library → unsplash → pexels

## Base B-Roll vs Extra Cutaways

Use two different media tools:

1. `dr_beat_broll_assign` sets the main media for a beat. This is the base background layer. Keep one primary B-Roll assignment per beat.
2. `dr_media_clip_add` adds extra muted media clips inside the beat. Use this for cutaways, close-ups, proof inserts, screenshots, packshots, or quick visual punctuation after the base B-Roll is already set.

Base B-Roll should carry the beat's main context. Extra cutaways should be short: usually 900-1800ms, placed on a voiced claim or product/proof detail. Do not add extra clips just to create motion; each one must clarify or intensify the argument.

There are no media-to-media transitions yet. Treat extra clips as hard cuts inside the beat. Beat transitions still happen between beats through `dr_transition_add`.

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
  media_type: "video",
  timing: { mode: "beat-relative", beat_id: "<beat id>", at_ms: 1400, duration_ms: 1200 },
  source_in_ms: 800,
  source_out_ms: 2000,
  track_index: 1,
  fit: "cover",
  animation: "zoom_in"
})
```

**Report to user:** Tell them which base B-Roll was assigned and which extra cutaways were added, with URLs/source labels.

---

## When User Has Own B-Roll

If `own_broll_available = true` but URLs weren't provided:

> "You mentioned you have your own B-Roll. For best results on the mechanism and proof beats, I need the direct URLs. Can you paste them here or upload via the editor?"

Don't proceed with stock-only suggestions for those beats until the user provides their footage or explicitly says to skip.
