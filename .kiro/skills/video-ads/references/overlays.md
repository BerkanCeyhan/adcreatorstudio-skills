# Overlay + Transition Routing Tables

These are **suggestions**, not hard caps. Use editorial judgment — pick blocks that match beat purpose and Visual Director mood. Vary choices across beats; avoid repeating the same block more than twice.

---

## Block Routing by Beat Purpose

Use `dr_blocks_list({ for_beat_type: "<beat>" })` to get a ranked list, then `dr_block_get(block_id)` for full schema.

| Beat purpose | Suggested blocks (ranked) | Mood note |
|---|---|---|
| `hook` | `dr/hook-bigtext-pop`, `dr/hook-question-zoom`, `dr/punctuation-pop`, `dr/search-query-overlay` | Big, single statement. Word-anchor on key noun. |
| `problem` | `dr/agitation-word-highlight`, `hf/macos-notification`, `dr/search-query-overlay`, `dr/video-inset-card` | Show pain. Notification or search style if relatable. |
| `agitate` | `dr/agitation-word-highlight`, `dr/punctuation-pop`, `dr/hook-question-zoom` | Punch-words synced to VO. |
| `why_others_fail` | `dr/animated-bullet-list`, `dr/step-path-goal` | 2–3 failure reasons. Keep copy tight. |
| `mechanism` | `dr/animated-bullet-list`, `dr/step-path-goal`, `dr/media-swap-stack` | Sequential steps or 3 bullets. Product-specific copy. |
| `value_prop` | `dr/animated-bullet-list`, `dr/receipt-breakdown`, `hf/apple-money-count` | Clear outcome or savings. |
| `proof` | `dr/map-pin-proof`, `dr/receipt-breakdown`, `dr/instagram-comment`, `dr/tiktok-comment`, `hf/reddit-post`, `hf/x-post`, `dr/dm-screenshot` | Vary platform by audience. Never use the same twice. |
| `objection` | `dr/instagram-comment`, `dr/tiktok-comment`, `dr/dm-screenshot`, `hf/x-post` | Real objection + response. |
| `cta` | `dr/cta-button-pulse`, `dr/scarcity-countdown`, `dr/punctuation-pop` | Pulse button always. Countdown only for real urgency. Word-anchor on price. |

---

## Transition Energy Table

Pick ONE primary transition family per video + ONE accent at the emotional climax.

| Energy | Primary transition | Accent | When |
|---|---|---|---|
| `calm` | `hf/blur-through`, `hf/focus-pull`, `hf/crossfade` | `hf/light-leak` | UGC, wellness, emotional, longer beats |
| `medium` | `hf/whip-pan`, `hf/push-slide`, `hf/cinematic-zoom` | `hf/chromatic-aberration` | SaaS, finance, coaching |
| `high` | `hf/flash-through-white`, `hf/glitch` | `hf/chromatic-aberration` | Fitness, gaming, hype, high-energy hook |

Match energy to the Visual Director Contract `energy` field (calm / medium / high).

**`hf/cinematic-zoom`**: the climax transition — use max once per video, at the emotional peak (Stuck → Revelation moment).

---

## Positioning Guidelines

- `position: "bottom"` — lower thirds, badges, CTAs
- `position: "middle"` — comments, DMs, search overlays, full-card overlays
- `position: "top"` — notifications (macOS, system alerts)
- `position: "custom"` with `yPercent` — precise placement (CTA button: `yPercent: 65`)

Safe zones (9:16): keep text within 64px left/right, avoid top 120px and bottom 180px except for deliberate lower-thirds.
