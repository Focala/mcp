# Focala MCP server

<p align="center">
  <img src="assets/focala-icon-400.png" alt="Focala" width="120" height="120" />
</p>

Official docs and setup for the Focala MCP server — AI search visibility for Answer Engine Optimization (AEO), Generative Engine Optimization (GEO) and SEO: brand mentions and citations in ChatGPT, Claude, Perplexity, Gemini and Google AI Overviews, plus GA4, Search Console, CRM and buyer insights in Claude, Cursor, ChatGPT and more.

## What it is

[Focala](https://focala.ai) is the AI Visibility / AI Search Optimization platform for AEO (Answer Engine Optimization), GEO (Generative Engine Optimization) and SEO. It helps brands get discovered, cited and recommended across ChatGPT, Perplexity, Claude, Gemini, Google AI Overview and other AI answer engines — tracking LLM visibility, AI citations, share of voice and competitor benchmarking alongside traditional search.

Its MCP server is a **hosted remote server** (Streamable HTTP) at:

```text
https://app.focala.ai/api/mcp
```

Connect ChatGPT, Claude, Perplexity, Gemini, Grok, Cursor or any MCP client and get AI search analytics for AEO, GEO and AI SEO in one place — brand visibility, competitor comparisons, cited sources, content opportunities, AI referral traffic, GA4, Search Console, CRM pipeline and buyer insights. Auth is **OAuth 2.0 authorization code + PKCE (S256)**. There is no API key, nothing to install, and no npx / npm / Docker package. Add the URL in your client, sign in, and pick one Focala workspace per connection. A Focala account is required.

## What you can do

Ask in plain language once connected, for example:

- How often does our brand appear in AI answers, and how has our AI Visibility Score changed?
- Which competitors are cited instead of us in ChatGPT, Claude, Perplexity or Google AI Overview?
- Which domains and pages are the cited sources driving share of voice?
- Which pages get the most AI referral traffic in GA4 this month?
- Which Search Console queries lost clicks while impressions grew?
- What content should we create next for AEO and AI SEO?
- What questions and objections do buyers raise most on sales calls?

## Setup

Use this single URL everywhere:

```text
https://app.focala.ai/api/mcp
```

### Claude (claude.ai / Claude Desktop)

Open **Connectors** (under Customize or Settings, depending on the client), choose **Add custom connector**, paste `https://app.focala.ai/api/mcp`, then sign in when prompted.

**Claude Code:**

```bash
claude mcp add --transport http focala https://app.focala.ai/api/mcp
```

### Cursor

Add to `~/.cursor/mcp.json` (or a project `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "focala": {
      "url": "https://app.focala.ai/api/mcp"
    }
  }
}
```

Cursor prompts you to sign in.

### VS Code

Add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "focala": {
      "type": "http",
      "url": "https://app.focala.ai/api/mcp"
    }
  }
}
```

### ChatGPT

Use the [official Focala plugin](https://chatgpt.com/plugins/plugin_asdk_app_6aca7fd827c48191aba49164d37c054d), or a custom connector with `https://app.focala.ai/api/mcp` where developer mode / custom connectors are available.

### Other remote-MCP clients

Any client that supports **Streamable HTTP** and **OAuth** can use `https://app.focala.ai/api/mcp`.

## Tools (22)

### Read tools

Available to any authorized role on the connection:

| Tool | Description |
| --- | --- |
| `get_workspace_context` | Connected workspace, permissions, brand context, and available filters |
| `get_visibility` | Brand visibility time series with previous-period comparison |
| `get_visibility_breakdown` | Breakdown by prompt, engine, category, and competitors |
| `get_coverage_health` | Observation coverage, freshness, and missing data |
| `get_competitors` | Competitor rankings and details |
| `get_sources` | Cited domains and pages: list, compare, or inspect |
| `list_prompts` | Monitored prompts, tags, and observed answers |
| `get_evidence` | Supporting evidence for prompt runs |
| `list_recommendations` | Recommendations, reasoning, and estimated impact |
| `get_buyer_insights` | Themes, objections, and excerpts from buyer conversations (not raw recordings or contacts) |
| `list_content` | Tracked content pages |
| `get_content_performance` | Content pickup and traffic metrics |
| `get_traffic` | AI referral, organic, and Search Console totals |
| `get_ga4_report` | Live GA4 rows from the workspace property |
| `get_search_console_report` | Finalized Search Console rows |
| `list_tasks` | Organization tasks |
| `get_task` | One task and its current version |
| `list_assignees` | Eligible assignees, names, and IDs |

### Write tools

Require manage access on the connection plus an editor or admin role:

| Tool | Description |
| --- | --- |
| `create_prompt` | Create a monitored prompt |
| `update_prompt` | Update a prompt and/or replace its tags |
| `create_task` | Create and optionally assign a task |
| `update_task` | Update task details, assignment, or status |

Writes require an **idempotency key**. Updates need the record's latest `updatedAt`.

## Limits

- **60** non-write requests and **10** write calls per connection per minute
- **300** requests per workspace per minute
- Over limit: HTTP **429** with `Retry-After: 60`
- Payload cap: **128 KiB**

## Integrations

These connect inside Focala (they are not separate MCP servers): GA4, Google Search Console, HubSpot, Salesforce, Gong, Fathom, Fireflies, and more.

## Links

- [Docs](https://focala.ai/docs/mcp)
- [Setup guide](https://focala.ai/guides/connect-ai-assistants-focala-mcp)
- [Website](https://focala.ai)
- [Support](https://focala.ai/contact)
- [Privacy](https://focala.ai/privacy-policy)
- [Terms](https://focala.ai/tos)

## MCP Registry metadata

This repository includes [`server.json`](server.json) for MCP Registry listings (`name`: `ai.focala/mcp`).

## License

The documentation and configuration snippets in this repository are MIT licensed (see [LICENSE](LICENSE)). The Focala service and the MCP server itself are proprietary and are not covered by this license; use of the service is subject to the [Focala terms of service](https://focala.ai/tos).
