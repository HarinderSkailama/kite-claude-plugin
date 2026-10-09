---
name: campaign-analysis
description: Analyze a store's Kite promotion performance with the Kite MCP tools — revenue, orders, AOV, discount cost, top and bottom campaigns, trends, period comparisons, and why a campaign is underperforming. Use when the user asks how their Kite campaigns, free gifts, BOGO or discounts are doing, wants a performance report, or asks why an offer isn't working.
allowed-tools: mcp__plugin_kite_kite__get_kite_context mcp__plugin_kite_kite__get_kite_campaigns mcp__plugin_kite_kite__get_kite_performance mcp__plugin_kite_kite__get_kite_campaign_configuration mcp__plugin_kite_kite__get_kite_offer_issues mcp__plugin_kite_kite__get_kite_reference
---

# Kite campaign analysis

The Kite tools are read-only. If they aren't available, tell the user to run `/mcp`, pick `plugin:kite:kite` and sign in.
Pass the user's question as `merchant_query` on every call.

## Tools

| Need | Tool | Key inputs |
|---|---|---|
| Timezone, currency, data coverage, valid campaign types | `get_kite_context` | none |
| Find campaigns or turn a name into an id | `get_kite_campaigns` | `filter.name_pattern`, `status`, `campaign_type`, `starts_*`/`ends_*`, `sort_by`, `page` |
| Totals, trends, rankings | `get_kite_performance` | `aggregate_result_to` (`summary` / `daily_series` / `monthly_series`), `group_by` (`total` / `campaign`), `filter.date_range`, `filter.campaign_ids`, `filter.name_includes` |
| How one campaign is set up | `get_kite_campaign_configuration` | `ref_type` + `ref_value` |
| Known problems (out-of-stock gift, empty collection, zero orders, overlaps) | `get_kite_offer_issues` | `filter.status`, `filter.severity`, `filter.campaign_id` |
| Metric definitions and caveats | `get_kite_reference` | `query_keywords` |

## Analysis recipe

1. **Frame.** Call `get_kite_context` once. Resolve relative dates ("last month") in the shop timezone and state the exact range. If no range can be inferred, ask once.
2. **Store overview.** `get_kite_performance` with `aggregate_result_to: "summary"`, `group_by: "total"`.
3. **Ranking.** ONE call: `aggregate_result_to: "summary"`, `group_by: "campaign"` (50 rows per page, highest revenue first). Never call it once per campaign.
4. **Trend.** `daily_series` (≤ 92 days) or `monthly_series` (≤ 24 months) for the store or for a few `campaign_ids`.
5. **Comparison.** Run the same call for the previous equal-length period and show the deltas.
6. **Diagnose.** For weak or zero-order campaigns, call `get_kite_offer_issues` first, then `get_kite_campaign_configuration`.
7. **Report.** Use the format below.

Metric meanings and how to read them: [references/metrics.md](references/metrics.md).

## Rules

- Say **attributed revenue**. Never call it profit, ROI or incremental. Label estimates, and don't invent benchmarks.
- If `reason.code` is `BEFORE_DATA_COVERAGE` or `meta.partialCoverage` is true, say Kite's data starts at `meta.dataCoverage.earliest`. Never report it as zero sales.
- If a name matches several campaigns, list them and ask. Don't pick one silently.
- For gift questions, search by name (`name_includes: ["gift","free"]`) as well as `campaign_type: "FREE_GIFT"`. Gifts can be built as Free Gift, Custom or Combined campaigns.
- Read offer-issue lines back as written. An empty list means "no known problems", not "healthy".
- A "why" answer is a possible cause, not a proven one.
- Free Gift campaigns don't run on POS.
- These tools can't change anything. If the user wants a change, tell them to make it in the Kite app.

## Report format

1. **Headline**: one sentence with the range and the biggest finding.
2. **Numbers**: a table of orders, attributed revenue, AOV, discount value and discount-to-revenue %, with the change vs the previous period.
3. **Top / bottom campaigns**: up to 5 each, with revenue and orders.
4. **Issues found**: offer-issue lines as written.
5. **What to look at next**: 2–3 concrete checks or changes for the merchant to make in Kite, marked as suggestions.
