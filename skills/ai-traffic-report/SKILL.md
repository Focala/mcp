---
name: ai-traffic-report
description: Use when the user asks about traffic from AI assistants or chatbots (ChatGPT, Perplexity, Gemini, Claude, Copilot and similar), AI referrals, GA4 source/medium for AI, landing pages from AI, or a combined GA4 and Google Search Console report. Do not use this for brand-mention or citation briefings.
---

# GA4 and Search Console traffic-from-AI report

Report measured traffic from AI assistants and, separately, Google Search Console organic search. Use only the tools below. Combine sources carefully: they use different timezones and metric definitions.

Clients that support MCP prompts can also invoke `ai-traffic-report` with optional `startDate`, `endDate`, and `focus`.

## Operating rules

- Call `get_workspace_context` first. `canManage`, `role`, and `access` are real on that response.
- `get_traffic` returns **stored** AI-referral and Search Console totals already in Focala. `get_ga4_report` and `get_search_console_report` fetch **live** provider rows. Prefer `get_traffic` for the Focala AI-referral snapshot, then live reports for exploration.
- **Dates:** tools take only `startDate` / `endDate` (`YYYY-MM-DD`). Both or neither on range tools; 1–366 days. No `period` enum. Omitting dates on `get_traffic` defaults to the last 30 complete UTC days. **`get_ga4_report` and `get_search_console_report` always require both `startDate` and `endDate`** — never call them without dates.
- Timezones differ: `get_traffic` visibility-style ranges are UTC; GA4 uses the property timezone returned in the report; Search Console dates are Pacific time (`America/Los_Angeles`) and this tool returns **finalized** rows only.
- GA4 `users` / `totalUsers` are distinct within a row — never sum users across rows. New users, users, and sessions are different metrics.
- `get_traffic` AI-referral `totalUsers` / source `activeUsers` count **new users**. `dailyUsers` fields sum daily users (new and returning) and may count the same person on multiple days or engines. They are not sessions and not range-unique users.
- Source aliases and UTM values are not inferred. Discover actual `sessionSource` values with an unfiltered `traffic_acquisition` report, then filter with those exact values. Do not assume names like "chatgpt.com" or "ChatGPT" without seeing them in the data.
- **GA4 filters:** `{ "dimension", "values", "matchType"?, "caseSensitive"? }` (`matchType` EXACT or CONTAINS; `caseSensitive` defaults false). AND across filters, OR across values; max 10 filters, 50 values.
- **GSC filters are different:** `{ "dimension", "expression", "operator"? }` — not `values` / `matchType`.
- Search Console is Google organic search (and other `searchType`s you request). It is not AI-referral traffic. Keep it in a separate section.
- Empty provider rows do not prove zero traffic outside that provider's retention or limits. Recent GA4 data may still change.
- If `availability` is `unavailable`, report the `reason` and skip that section. Typical reasons: GA4 `not_configured`; Search Console `not_connected` or `property_missing`.
- **Pagination:** `page` / `limit` are inputs; `nextPage` is a response field only — never an argument.
- Do not treat content-tracking before/after differences as causal. Do not invent revenue or conversion claims the rows do not contain.

## Steps

1. **Context.** Call `get_workspace_context`.

2. **Stored Focala snapshot.** Call `get_traffic` with the date range, `includeLandingPages: true` (default is `false`), and optional `page` / `limit`. Read `definitions`. Report:
   - `aiReferrals` only if `availability` is `available`; otherwise state that stored AI-referral data is unavailable.
   - `ga4.aiUsers` and `ga4.organicUsers` when present (GA4 users are deduplicated over the range).
   - `searchConsole` totals only if `availability` is `available`.
   - Landing pages when returned (engine breakdown if present).
   Ignore fields the tool marks unavailable. Do not substitute other metrics for missing ones.

3. **Discover AI sources in live GA4** (only if you need more than the stored snapshot, or `get_traffic` AI referrals are unavailable). Call `get_ga4_report` with `report: "traffic_acquisition"`, required `startDate` and `endDate`, and no filters. If `availability` is `unavailable`, tell the user GA4 is not linked or the live request failed (`reason`), and point them to connect GA4 in Focala settings. Otherwise inspect `sessionSource` / `sessionMedium` / `sessionDefaultChannelGroup` for AI assistants (for example values that clearly refer to ChatGPT, Perplexity, Gemini, Claude, Copilot, or other AI products). If none appear, say so — do not invent sources.

4. **AI-filtered GA4 views.** If you found actual source values, call `get_ga4_report` again with the same required dates and filters such as `{ "dimension": "sessionSource", "values": ["...exact values..."], "matchType": "CONTAINS" }` or `EXACT` as appropriate (optional `caseSensitive`). Useful reports:
   - `overview` — deduplicated totals under the same filters.
   - `landing_pages` — entry pages receiving that traffic.
   - `traffic_trend` — daily trend (do not assume these daily user counts are unique for the whole range).
   - `campaigns` only if campaign values are relevant.
   If the response includes `nextPage`, continue with a higher `page` (do not send `nextPage` as input). Read `definitions` and metadata (sampling, thresholding).

5. **Search Console (organic, separate).** Call `get_search_console_report` with `report: "overview"`, `searchType: "web"` unless the user asked otherwise, and required `startDate` / `endDate`. If available, also fetch `search_trend`, and `queries` or `pages` for top rows. When filtering, use GSC filter shape `{ "dimension", "expression", "operator"? }` — not GA4's `values` / `matchType`. Note that results are top rows, not an exhaustive export, and CTR is a fraction. Query dimensions are not valid for `discover` or `googleNews`. If unavailable, report `not_connected` or `property_missing` and explain they need to connect Search Console and select a site in Focala.

6. **Tracked content (optional).** If the user asked about specific pages, call `list_content` (no `contentId` on that tool), then `get_content_performance` with required `contentId` (GUID from the list row). Label Search Console snapshots and pickup/traffic as existing tracking windows, not proof of causation.

## Output format

1. **What's available** — a clear list: stored AI referrals, live GA4, Search Console, tracked content — each available or not, with the reason when missing.
2. **AI referral / AI-assistant traffic** — date range, timezone, and metrics with their definitions. Break down by source/medium and by landing page when you have rows. Name only sources actually present in the data.
3. **Trend** — daily or stored trend for AI traffic when available, qualified if coverage is incomplete.
4. **Google Search Console (organic)** — separate section: clicks, impressions, CTR, average position, top queries or pages. Explicitly label this as Google Search, not AI traffic.
5. **How to read the two together** — they are complementary (AI referrals vs organic search), not the same funnel, and dates may not align perfectly across UTC, property timezone, and Pacific time.
6. **Gaps and next steps** — connect GA4 or Search Console if needed; add landing-page or query filters the user may want; do not promise attribution this server cannot provide.

## Edge cases

- **Not connected / auth error on MCP:** ask the user to reconnect at `https://app.focala.ai/api/mcp`.
- **GA4 not configured or live call failed:** use `get_traffic` if its AI or GA4 fields are available; otherwise stop that section and tell them to link the GA4 property in Focala.
- **Search Console not connected or no site selected:** omit organic tables; do not fill them from GA4 organic users as if they were GSC.
- **No AI sources in traffic_acquisition:** report that no AI assistant sources appeared in this property and range. Suggest reviewing source names in the unfiltered table with the user before filtering again.
- **Empty GSC rows:** do not claim zero search traffic beyond what finalized, top-row data shows.
- **Rate limits or 413:** drop landing-page pagination, narrow date ranges, or lower `limit`.
