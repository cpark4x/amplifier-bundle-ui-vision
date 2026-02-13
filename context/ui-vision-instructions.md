# UI Vision Testing System

You are equipped with AI-powered visual UI testing capabilities. The Playwright MCP
server runs in vision mode, allowing the model to see actual screenshots of rendered
web pages and analyze them as a human would.

## Architecture

```
User Request -> Root Session (you) -> Delegate to specialist agent
                                           |
                                     Agent uses Playwright MCP:
                                       browser_navigate -> go to URL
                                       browser_take_screenshot -> model SEES the image
                                       browser_snapshot -> accessibility tree (text)
                                       browser_click / browser_press_key -> interact
                                           |
                                     Agent returns structured report
```

## When to Delegate

| User Says | Delegate To |
|-----------|-------------|
| "Check how this page looks" | `ui-vision:visual-auditor` |
| "Audit the UI" | `ui-vision:visual-auditor` |
| "Look at the app" | `ui-vision:visual-auditor` |
| "Did the UI change?" | `ui-vision:regression-checker` |
| "Compare before and after" | `ui-vision:regression-checker` |
| "Did my CSS break anything?" | `ui-vision:regression-checker` |
| "Check accessibility" | `ui-vision:accessibility-scanner` |
| "Is the contrast OK?" | `ui-vision:accessibility-scanner` |
| "Can screen readers use this?" | `ui-vision:accessibility-scanner` |

## Running Recipes

For repeatable workflows, use the recipes tool:

```
recipes execute ui-vision:recipes/visual-audit.yaml
  context: { "url": "http://localhost:5173", "pages": "/,/settings" }
```

## Prerequisites

The target web application must be running before visual testing. For local
development, ensure the dev server is started (e.g., `pnpm run start`).

## Important Notes

- The Playwright MCP server starts automatically when the bundle loads
- Screenshots are returned as base64 images in tool results - the model sees them
- The accessibility tree (`browser_snapshot`) provides structural context alongside visuals
- Vision mode also enables coordinate-based clicking for elements without accessibility labels
