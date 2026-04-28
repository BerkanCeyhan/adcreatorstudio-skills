# AdCreator Studio — Agent Skills

Agent skills for [AdCreator Studio](https://adcreatorstudio.com). Adds AI-native workflows to your coding agent.

> **Requires an AdCreator Studio account** with MCP connected. [Set up here →](https://adcreatorstudio.com/settings/api-access)

---

## Skills

| Skill | Trigger | What it does |
|---|---|---|
| **Video Ads** | `"use adcreator to make a video ad about..."` | Creates a complete short-form video ad — script, voice, B-Roll, overlays, transitions — for TikTok, Reels & Shorts |

---

## Install

### Claude Code (recommended)

If you have [Superpowers](https://github.com/anthropics/claude-plugins-official) installed:

```
/plugin marketplace add BerkanCeyhan/adcreatorstudio-skills
```

Or install directly from the registry:

```bash
claude plugin install BerkanCeyhan/adcreatorstudio-skills
```

### Any agent harness (Claude Code, Cursor, Gemini CLI, Codex, Kiro, Antigravity)

```bash
npx skills add BerkanCeyhan/adcreatorstudio-skills
```

### Manual install (no npm required)

**Mac / Linux:**
```bash
git clone https://github.com/BerkanCeyhan/adcreatorstudio-skills ~/.claude/skills/adcreator
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/BerkanCeyhan/adcreatorstudio-skills "$env:USERPROFILE\.claude\skills\adcreator"
```

---

## Connect MCP (required)

The skill needs your AdCreator Studio API key connected as an MCP server.

**Claude Code:**
```bash
claude mcp add --transport http adcreator \
  https://adcreatorstudio.com/api/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

**Cursor / VS Code (`mcp.json`):**
```json
{
  "mcpServers": {
    "adcreator": {
      "url": "https://adcreatorstudio.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

Get your API key: [adcreatorstudio.com/settings/api-access](https://adcreatorstudio.com/settings/api-access)

---

## Usage

Once installed and MCP connected, just ask your agent:

```
Use adcreator to make a video ad about [your product]
```

The agent will walk through:
1. Product questionnaire (brand, audience, mechanism, offer)
2. Script in beats (hook → problem → mechanism → proof → CTA)
3. Voice generation (TTS per beat)
4. B-Roll suggestions
5. Overlay blocks (text pops, review cards, CTA button)
6. Transitions
7. Editor link for review
8. Render on confirmation

---

## Supported Harnesses

| Harness | Install method | Auto-load |
|---|---|---|
| Claude Code | `/plugin marketplace add` or `npx skills add` | SessionStart hook |
| Cursor | `npx skills add` → `.cursor/skills/` | On trigger |
| Gemini CLI | `npx skills add` → extension | `gemini-extension.json` |
| Codex CLI | `npx skills add` → `.codex/` | SessionStart |
| VS Code Copilot | `npx skills add` → `.github/` | Workspace |
| Antigravity | `npx skills add` → `.agents/skills/` | On trigger |
| Kiro | `npx skills add` → `.kiro/skills/` | On trigger |

---

## Update

```bash
npx skills add --update BerkanCeyhan/adcreatorstudio-skills
```
