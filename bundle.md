---
bundle:
  name: ui-vision
  version: 0.1.0
  description: AI-powered visual UI testing with Playwright vision capabilities

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: ui-vision:behaviors/ui-vision
---

# UI Vision Testing

AI-powered visual UI testing for web applications. Uses Playwright MCP with vision
capabilities to let the model literally see screenshots and analyze your UI.

@ui-vision:context/ui-vision-instructions.md

---

@foundation:context/shared/common-system-base.md
