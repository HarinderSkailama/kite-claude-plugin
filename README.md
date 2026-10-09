# Kite plugin for Claude Code (POC)

A personal proof of concept, not an official release. It bundles:

- the **Kite MCP** (read-only promotion analytics, remote, OAuth), and
- a **campaign-analysis** skill that teaches Claude which Kite tools to use and how to analyze campaign performance.

## Install

In Claude Code (v2.1.275 or later):

```text
/plugin install kite --marketplace Harinder441/kite-claude-plugin
```

Or from your shell:

```bash
claude plugin marketplace add Harinder441/kite-claude-plugin
claude plugin install kite@kite-poc
```

## Connect

1. Restart Claude Code, or run `/reload-plugins`.
2. Run `/mcp`, select `plugin:kite:kite`, choose **Authenticate**, and finish the Kite sign-in in your browser.

## Use

Ask something like "How did my Kite campaigns do last month?", or run `/kite:campaign-analysis`.

## Layout

```text
.claude-plugin/marketplace.json            marketplace "kite-poc"
plugins/kite/.claude-plugin/plugin.json    plugin "kite"
plugins/kite/.mcp.json                     Kite MCP server (URL only, no secrets)
plugins/kite/skills/campaign-analysis/     SKILL.md + references/metrics.md
```
