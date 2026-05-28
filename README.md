# CGD Interactive Toolkit

Resources for building interactive tools, data visualizations, and microsites at CGD. This repo is the single source of truth for brand, analytics, and design standards for interactives — whether you're building a simple custom Plotly chart or a multi-page dashboard.

## How to use this

**IMPORTANT: If you're planning to build an interactive, loop in comms before you start.** Send [Jeremy Gaines](mailto:jgaines@cgdev.org) and your [communications manager](https://centerforglobaldevelop.sharepoint.com/SitePages/mediarelations.aspx) a message, and we'll help you figure out hosting, design, and analytics. The earlier we're involved, the smoother the process. If you show up with a completely-built interactive, it may need to be extensively reworked. Minor, early design and architecture designs can have major implications—10x faster speed, or hundreds of dollars per month in hosting vs. cents.

**If you're using Claude Code, ChatGPT Codex, or another LLM to build**, you can feed it the relevant files from this repo in your project context. Each document is written to be useful to both humans and LLMs.

This is an early-stage project, and we'll build out more materials and guidance.

## When to use a custom interactive

Most graphs should be either static images, or simple interactives built within CGD's Flourish account. Bespoke custom-coded interactives take more work on the parts of both researchers and comms to create and maintain. They should **only** be created when you need functionality or control that is not possible within Flourish.

## Reference and governance documents

- **[interactive-coding-standard.md](interactive-coding-standard.md)** — Start here. Default technical standards for CGD custom interactives, including project structure, library choices, data handling, embedding, accessibility, security, performance, documentation, and QA.

- **[cgd-brand-reference.md](cgd-brand-reference.md)** — CGD's branding guide translated into a simple text document for LLM use. It includes color palette (hex, RGB, CSS custom properties, Tailwind config), fonts, logo usage, and core design elements like buttons and links. Once you've established the technical basics, make it match CGD's brand.

- **[analytics-tracking-standard.md](analytics-tracking-standard.md)** — A set of rules governing how we track user interactions across embedded interactives using GA4 and postMessage. Includes the event schema, implementation code, naming conventions, and a checklist for adding tracking to a new tool. Any interactive embedded on cgdev.org needs to follow this. Implement once the interactive is essentially done, to avoid rewrites and adjustments.

## Delivery checklist

## Contact

For questions, to discuss a new interactive, or to request additions to this toolkit: [jgaines@cgdev.org](jgaines@cgdev.org).
