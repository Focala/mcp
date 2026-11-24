# Focala customer MCP skills

Customer-facing skills for the hosted Focala MCP server (`https://app.focala.ai/api/mcp`, OAuth, Streamable HTTP). They teach an agent how to run marketer jobs with the [22 tools](../README.md#tools-22) documented in this repo.

Buyer insights (`get_buyer_insights`) cover themes and objections from buyer conversations. There is no pipeline or deal-attribution tool on this server.

Canonical docs: https://app.focala.ai/docs/mcp.html

## What's in this folder

| Path | Purpose |
| --- | --- |
| `ai-visibility-briefing/SKILL.md` | Weekly / period AI visibility briefing (mentions, citations, trends, share vs competitors, recommended actions). |
| `competitor-gap-analysis/SKILL.md` | Where competitors are cited or mentioned and the brand is not; prompts to target. |
| `opportunity-planner/SKILL.md` | Content and prompt action plan; creates tasks or prompts only after the user confirms. |
| `ai-traffic-report/SKILL.md` | Traffic from AI assistants via stored Focala totals, live GA4, and a separate Search Console organic section. |
| `chatgpt/visibility-briefing-instructions.md` | Short ChatGPT app / custom GPT instructions for the briefing skill. |
| `cursor/visibility-briefing.mdc` | Cursor rule (`.mdc`) for the briefing skill. |
| `starter-prompts.md` | Suggested starter prompts for directories and docs. |

Each `SKILL.md` uses the Anthropic Agent Skills format: YAML frontmatter with `name` and a `description` that says when to use the skill, then concise steps that call Focala tools by name, an expected output format, and edge cases (no data, not connected, GA4 or Search Console not linked, no write access).

## Install

### Claude Code (plugin)

This repository is packaged as a Claude Code plugin (see [`.claude-plugin/plugin.json`](../.claude-plugin/plugin.json)). From a machine with Claude Code:

```bash
claude plugin marketplace add Focala/mcp
claude plugin install focala@focala-mcp
```

Or load the checkout in place:

```bash
claude --plugin-dir /path/to/Focala/mcp
```

The plugin bundles the four Agent Skills and declares the remote MCP server at `https://app.focala.ai/api/mcp` (HTTP / Streamable HTTP, OAuth). Sign in when Claude Code prompts you.

You can also copy any skill folder into a project `.claude/skills/` directory.

### Cursor

- Copy `cursor/visibility-briefing.mdc` to `.cursor/rules/focala-visibility-briefing.mdc` in your project.
- Optionally copy the four Agent Skill folders into Cursor skills so you can @-mention them (`SKILL.md` is compatible).
- Connect the MCP server as described in the [root README](../README.md#cursor).

`alwaysApply` is `false`, so the rule runs when you @-mention it or the agent judges the description relevant.

### ChatGPT

- Paste `chatgpt/visibility-briefing-instructions.md` into the Focala ChatGPT app / custom GPT developer instructions.
- Use prompts from `starter-prompts.md` as defaults or examples.
- Prefer the [official Focala plugin](https://chatgpt.com/plugins/plugin_asdk_app_6aca7fd827c48191aba49164d37c054d), or a custom connector with `https://app.focala.ai/api/mcp`.

### Docs and directories

The prompts in `starter-prompts.md` are safe for public MCP docs and directory “example prompts” fields. They do not promise tools that do not exist (no email send, prompt deletion, or immediate monitoring runs).

## Tool notes the skills already work around

- There is no first-class share-of-voice metric. The briefing compares visibility and answer presence versus competitors on monitored prompts only, and says so.
- `create_prompt` does not start an analysis run.
- `get_competitors` citation opportunities exclude social hosts; skills route those questions to `get_sources`.
- GA4 source names are not aliased; the traffic skill discovers live `sessionSource` values first.
- GA4 and Search Console may be unlinked (`not_configured`, `not_connected`, `property_missing`).
- Write tools are hidden for viewers and read-only connections.
