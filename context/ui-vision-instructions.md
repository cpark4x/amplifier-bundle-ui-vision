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

## SPA & JavaScript Framework Awareness

Modern web apps (React, Vue, Angular, etc.) render client-side. All agents must
account for this:

### Hydration & Initial Render
- **Always wait after navigation** before taking screenshots — SPAs need time to hydrate
- A page may show a blank shell, skeleton loaders, or spinner before real content appears
- After `browser_navigate`, use `browser_snapshot` first to check if content has loaded before screenshotting

### Detecting "Page Ready"
- **Skeleton loaders** — placeholder shapes (gray boxes, pulsing lines) mean content is still loading
- **Spinners / progress indicators** — wait for them to disappear
- **Empty containers** — a `<div>` with no children may be waiting for async data
- **"Loading..." text** — explicit loading states in the accessibility tree

### Client-Side Routing
- SPA page transitions don't trigger full page reloads
- After clicking a navigation link, the URL changes but the page updates in-place
- Always re-snapshot after navigation to get the updated accessibility tree

### Transient UI
- **Toasts / notifications** — appear briefly and auto-dismiss; screenshot quickly if relevant
- **Modals / dialogs** — capture while open; they overlay the main content
- **Dropdowns / menus** — open them, then screenshot before they close
- **Tooltips** — hover to trigger, then screenshot immediately

## Waiting for Async Operations

Web apps frequently have operations that take time (API calls, AI generation, file
processing). Agents must handle this intelligently.

### Progress Detection Strategy
1. **Identify the indicator** — What element shows progress? (spinner, status text, progress bar)
2. **Watch for change** — Is the indicator different from the last snapshot?
3. **Detect completion** — Success message, new content appearing, indicator disappearing
4. **Ask if stuck** — After 2-3 snapshots with no change, report uncertainty rather than guessing

### Polling Pattern
- Take a snapshot to check for loading indicators
- If loading: wait a few seconds, re-snapshot, describe what changed
- If no change after 2-3 checks: note the page appears stuck and report what you see
- Never silently poll — always describe the current state between checks

## Error Recovery

When Playwright interactions fail, agents should recover gracefully:

| Error | Recovery |
|-------|----------|
| Element not found after click | Re-snapshot — the accessibility tree may have changed |
| Page appears blank | Wait for JS hydration — SPA may still be loading |
| Screenshot shows loading state | Wait and retry — content hasn't rendered yet |
| Navigation doesn't change page | Check if it's client-side routing — re-snapshot instead of re-navigating |
| Element not interactive | Check if it's obscured (modal, overlay, z-index) or disabled |
| Timeout on page load | Page may be slow or broken — screenshot whatever is visible and report |

## Authenticated Pages

Many web applications require login before testing. When testing authenticated apps:

- The Playwright MCP browser session persists cookies within a session
- If a page redirects to login, the agent should note that authentication is required
- Provide login credentials in the recipe/instruction context if the app needs auth
- After login, verify the session is active before proceeding with the audit

## Important Notes

- The Playwright MCP server starts automatically when the bundle loads
- Screenshots are returned as base64 images in tool results - the model sees them
- The accessibility tree (`browser_snapshot`) provides structural context alongside visuals
- Vision mode also enables coordinate-based clicking for elements without accessibility labels
