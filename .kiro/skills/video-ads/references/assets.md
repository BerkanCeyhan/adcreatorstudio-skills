# Asset Library

## Checking Available Assets

Before calling `dr_broll_suggest`, check what the user has uploaded:

```typescript
dr_assets_list({ video_id })
// Returns user's uploaded media with tags, description, file_type, url
```

Assets are auto-tagged by GPT-4o-mini vision at upload time:
- `objects` — what's in the frame
- `shot_type` — closeup / medium / wide / detail / product
- `product_visible` — boolean
- `usable_as_broll` — boolean
- `short_description` — German + English

Trust these tags when matching assets to beats. Filter by `usable_as_broll: true` for B-Roll candidates.

---

## Asset Gap Detection (S7)

For key beats, check if a matching product asset exists. If not, offer the user a direct upload link before falling back to stock.

| Beat | What to look for | When to ask |
|---|---|---|
| `mechanism` | `shot_type: "product"` or `"detail"`, `product_visible: true` | Always — stock never works for mechanism |
| `proof` | `shot_type: "product"` with customer context, or screenshot-type | When no proof asset found |
| `cta` | `shot_type: "product"` hero shot | Preferred — stock acceptable as fallback |
| `hook` | `shot_type: "closeup"` or dramatic product | Optional — bold stock is fine |

### Requesting an Upload

```typescript
// When no matching product asset found for mechanism beat:
dr_assets_upload_link({
  project_id: "<video_id>",
  slot_label: "Product closeup",
  hint: "Mechanism beat — product detail shot showing the key feature"
})
// Returns a signed upload URL the user can open to drop files
```

**User message template:**
> "For the mechanism beat, I'd love a closeup of [product] showing [key feature]. Your library doesn't have one yet — upload it here: [url]. Otherwise I'll use Pexels lifestyle stock."

Always give the user the choice (upload vs. proceed with stock). Don't block the workflow waiting indefinitely.

---

## Why Product-Scoped Assets Win

User assets are searched first in `dr_broll_suggest`. Assets with matching tags score higher than stock. A well-tagged product closeup will always outrank a generic Pexels lifestyle shot — because the tags match the VO nouns extracted by the LLM.

If the user uploads footage during the agent run, call `dr_assets_list` again after the upload webhook fires to get the new asset URLs.

---

## Auto-Tagging Pipeline

At upload, the server runs:
1. `ffprobe` → duration, dimensions
2. Sample 2–4 frames
3. GPT-4o-mini vision → structured tags + short description
4. Tags written to `assets.tags` and `assets.description`

This means user uploads are searchable immediately after upload. No manual tagging needed.
