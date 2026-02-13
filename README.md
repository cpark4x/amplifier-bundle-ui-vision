# amplifier-bundle-ui-vision

AI-powered visual UI testing for [Amplifier](https://github.com/microsoft/amplifier) agents. Uses [Playwright MCP](https://github.com/microsoft/playwright-mcp) with vision capabilities so the model can literally **see** screenshots and analyze your UI.

## What It Provides

- **Playwright MCP server** configured with vision mode (`--caps=vision`, `--image-responses allow`)
- **3 specialist agents** for visual testing workflows
- **3 recipes** for repeatable visual testing

### Agents

| Agent | Purpose |
|-------|---------|
| `ui-vision:visual-auditor` | Comprehensive visual UI quality audit |
| `ui-vision:regression-checker` | Visual regression detection after code changes |
| `ui-vision:accessibility-scanner` | WCAG-focused accessibility analysis |

### Recipes

| Recipe | Purpose |
|--------|---------|
| `ui-vision:recipes/visual-audit.yaml` | Page-by-page visual quality audit |
| `ui-vision:recipes/regression-check.yaml` | Before/after visual comparison |
| `ui-vision:recipes/accessibility-scan.yaml` | WCAG accessibility analysis |

## Installation

### As a full bundle (standalone use)

```yaml
# In your .amplifier/bundle.md or bundle.md
includes:
  - bundle: git+https://github.com/cpark4x/amplifier-bundle-ui-vision@main
```

### As a behavior (compose into existing bundle)

```yaml
# In your bundle's includes section
includes:
  - bundle: git+https://github.com/cpark4x/amplifier-bundle-ui-vision@main#subdirectory=behaviors/ui-vision.yaml
```

## Prerequisites

- **Node.js** (for `npx` to launch Playwright MCP)
- **Amplifier** with foundation bundle
- A running web application to test

## How It Works

```
User: "Check how the dashboard looks"
  |
  v
Root Session -> delegates to ui-vision:visual-auditor
  |
  v
Agent uses Playwright MCP:
  browser_navigate -> go to URL
  browser_take_screenshot -> model SEES the actual pixels
  browser_snapshot -> accessibility tree (structured text)
  browser_click / browser_press_key -> interact with UI
  |
  v
Agent returns structured visual analysis report
```

The key innovation: when the agent calls `browser_take_screenshot`, the screenshot is returned as a base64 image that the model can **literally see and describe** - not just a file path. This enables genuine visual analysis of layout, color, typography, spacing, and more.

## Usage Examples

### In a session

```
"Check how localhost:3000 looks"
"Did my CSS changes break anything on the settings page?"
"Run an accessibility scan on the signup form"
```

### Via recipes

```
recipes execute ui-vision:recipes/visual-audit.yaml
  context: { "url": "http://localhost:3000", "pages": "/,/dashboard,/settings" }
```

## License

MIT
