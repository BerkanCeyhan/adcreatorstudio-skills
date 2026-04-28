# Voice Reference

## Selection by Niche

Call `dr_voice_list` to get available voices with their labels. Pick based on product + audience.

| Product type | Voice personality | Labels to look for |
|---|---|---|
| Beauty, skincare, wellness (women) | Warm conversational female | "conversational", "female", "warm", "young adult" |
| Fitness, supplements (men) | Confident direct male | "confident", "male", "direct", "energetic" |
| Finance, coaching, high-ticket | Authority male, serious | "authority", "male", "deep", "serious", "mature" |
| Youth, gaming, entertainment | Young energetic | "young", "energetic", "excited", "fast" |
| Medical, insurance, premium | Calm trustworthy | "calm", "clear", "trustworthy", "neutral" |
| Luxury, cinematic | Deep deliberate male | "deep", "slow", "male", "luxury", "cinematic" |
| SaaS, productivity, B2B | Professional neutral | "professional", "neutral", "clear", "mid-range" |

No audio preview in coding agent — describe the voice from its labels when reporting to user.

---

## ElevenLabs Model Settings

### Models

| Model ID | Use case | Notes |
|---|---|---|
| `eleven_v3` | Best quality, emotion-aware | Supports audio tags for pacing. Recommended. |
| `eleven_multilingual_v2` | Fast, multilingual | Use if v3 unavailable or budget-sensitive |
| `eleven_flash_v2_5` | Lowest latency | For real-time or rapid iteration only |

Default: `eleven_v3` when available.

### Voice Speed

| Beat | Recommended speed | Notes |
|---|---|---|
| hook | 1.0–1.1 | Deliberate. Let the hook land. |
| problem | 0.95–1.0 | Slightly slower = empathetic, relatable |
| agitate | 1.1–1.2 | Faster = urgency building |
| why_others_fail | 1.0 | Confident dismissal |
| mechanism | 0.9–1.0 | Slower = explanation mode, trustworthy |
| value_prop | 1.0–1.05 | Clear and confident |
| proof | 1.0 | Measured delivery = credibility |
| objection | 1.05–1.1 | Quick reassurance stack |
| cta | 1.1–1.2 | Energy spike, urgency |

These are global speed settings. If the video has a consistent pace, use 1.0 as base and let the copywriting carry the pacing.

### Stability and Style

| Audience | Stability | Style |
|---|---|---|
| Premium, medical | 0.7–0.8 | 0.0 (neutral) |
| Lifestyle, conversational | 0.4–0.6 | 0.2–0.4 |
| Energetic, youth | 0.3–0.5 | 0.3–0.5 |

Higher stability = more consistent, less expressive. Lower = more dynamic, more varied.

### Audio Tags (eleven_v3 only)

The `eleven_v3` model supports inline audio tags for fine-grained pacing. These are automatically added by the TTS route based on beat type. Set `voice_audio_tag_mode` to control:
- `"auto"` — tags added automatically per beat type (default)
- `"off"` — no tags, plain text
- `"custom"` — tags from `voice_custom_audio_tags`

Example auto-tag enhancement for a hook beat:
```
[fast] Your skincare routine is making it worse. [pause: 0.3s]
```

---

## Voice Settings in dr_video_create

```typescript
dr_video_create({
  // ...
  voice_id: "21m00Tcm4TlvDq8ikWAM",  // from dr_voice_list
  // voice settings default to sensible values
  // user can adjust in editor per-video
})
```

Voice model, stability, style, speed are stored per-video and applied to all beats. They can be changed in the editor UI, then TTS regenerated.

---

## When TTS Sounds Wrong

| Symptom | Likely cause | Fix |
|---|---|---|
| Too fast, words blur | Speed > 1.2 | Reduce speed or shorten VO text |
| Monotone, no emotion | Stability too high | Lower to 0.4–0.5 |
| Inconsistent delivery | Stability too low | Raise to 0.6 |
| Wrong pacing mid-sentence | Word count over limit | Trim VO text to beat word count |
| Sounds robotic | Wrong model | Switch to `eleven_v3` |
| Audio too short, CTA cut off | Duration underestimated | Check `durationMs` from TTS response |
