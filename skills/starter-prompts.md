# Suggested starter prompts

Use these in directory listings, the public MCP docs, the ChatGPT `defaultPrompt` list, and "try asking" examples. They match tools that exist today and do not promise email sending, prompt deletion, or an immediate monitoring run.

## MCP prompt names (prompts/list)

Clients that support MCP prompts can call these four workflows (`prompts/get`). Args match live tools: `startDate` / `endDate` (`YYYY-MM-DD`, both or neither; no `period` enum). Omitting dates on range tools defaults to the last 30 complete UTC days; the briefing workflow should pass an explicit 7-day window when dates are omitted.

| Prompt | Optional args |
| --- | --- |
| `ai-visibility-briefing` | `startDate`, `endDate`, `focus` |
| `competitor-gap-analysis` | `startDate`, `endDate`, `competitor`, `focus` |
| `opportunity-planner` | `startDate`, `endDate`, `focus` |
| `ai-traffic-report` | `startDate`, `endDate`, `focus` |

## Natural-language starters

Aligned with the four MCP prompts and the ChatGPT plugin defaults:

1. Give me a weekly AI visibility briefing for my brand: mentions, citations, trends, how I compare with competitors on monitored prompts, and what I should do next. *(→ `ai-visibility-briefing`)*
2. How has my brand's AI visibility changed over the last 30 days, and is the observation coverage strong enough to trust the change? *(→ `ai-visibility-briefing` with a 30-day `startDate`/`endDate`)*
3. Which competitors rank ahead of my brand on monitored prompts, and where are they cited in AI answers when we are not? Which prompts should I target? *(→ `competitor-gap-analysis`; optional `competitor`)*
4. What are my highest-priority content and prompt opportunities this week? Draft tasks and new monitored prompts, but do not create them until I confirm. *(→ `opportunity-planner`)*
5. Show my traffic from AI assistants in GA4 for the last 30 days, including landing pages, and separately summarize my Google Search Console organic performance. *(→ `ai-traffic-report`; live GA4/GSC calls need explicit `startDate`/`endDate`)*
6. Which monitored prompts had the biggest visibility gains or losses, and which engines drove those changes?
7. Which domains are cited most often in answers that mention my brand versus my top competitors?
8. Summarize our buyer themes and objections, then recommend content angles and tasks I can act on (show me the plan before creating anything). *(buyer insights + `opportunity-planner`)*

The live ChatGPT plugin keeps three shorter defaults ("How has my brand's AI visibility changed over the last 30 days?", "Which competitors rank ahead of my brand on monitored prompts?", "What tasks are open in my Focala workspace?") and adds starters 1, 4, and 5 when the listing allows more than three.
