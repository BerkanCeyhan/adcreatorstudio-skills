# AdCreator Studio — Agent Skills

Agent skills for [AdCreator Studio](https://adcreatorstudio.com). Adds AI-native workflows to your coding agent.

> **Requires an AdCreator Studio account** with MCP connected. [Set up here →](https://ai.adcreatorstudio.com/settings/api-access)

---

## Skills

| Skill | Trigger | What it does |
|---|---|---|
| **Video Ads** | `"use adcreator to make a video ad about..."` | Creates a complete short-form video ad — script, voice, B-Roll, overlays, transitions — for TikTok, Reels & Shorts |

---

## Install

### Claude Code

Via the plugin marketplace:

```
/plugin marketplace add BerkanCeyhan/adcreatorstudio-skills
```

Or from the CLI:

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
  https://ai.adcreatorstudio.com/api/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

**Cursor / VS Code (`.cursor/mcp.json` or `.vscode/mcp.json`):**
```json
{
  "mcpServers": {
    "adcreator": {
      "url": "https://ai.adcreatorstudio.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**Gemini CLI (`~/.gemini/settings.json`):**
```json
{
  "mcpServers": {
    "adcreator": {
      "httpUrl": "https://ai.adcreatorstudio.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

**Codex CLI:**
```bash
codex mcp add adcreator \
  https://ai.adcreatorstudio.com/api/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

Get your API key: [ai.adcreatorstudio.com/settings/api-access](https://ai.adcreatorstudio.com/settings/api-access)

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
| Claude Code | `claude plugin install` or `npx skills add` | On trigger |
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
