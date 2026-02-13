# UI Vision Testing Capabilities

You have AI-powered visual UI testing capabilities via Playwright MCP with vision mode.

## Available Agents

| Agent | Use When |
|-------|----------|
| `ui-vision:visual-auditor` | User asks to "look at", "check", or "audit" a UI visually |
| `ui-vision:regression-checker` | User asks if UI "changed", "broke", or "regressed" after code changes |
| `ui-vision:accessibility-scanner` | User asks about accessibility, a11y, WCAG, contrast, or readability |

## Available Recipes

| Recipe | Purpose |
|--------|---------|
| `ui-vision:recipes/visual-audit.yaml` | Comprehensive page-by-page visual audit |
| `ui-vision:recipes/regression-check.yaml` | Before/after visual comparison |
| `ui-vision:recipes/accessibility-scan.yaml` | WCAG-focused accessibility analysis |

## How It Works

The Playwright MCP server provides browser tools. When an agent calls
`browser_take_screenshot`, the screenshot is returned as a base64 image that the
model can literally see and analyze. This is combined with `browser_snapshot`
(accessibility tree) for structural understanding.

ALWAYS delegate visual testing tasks to the appropriate agent above rather than
using Playwright tools directly.
