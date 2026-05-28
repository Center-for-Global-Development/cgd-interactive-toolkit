# CGD Interactive Toolkit

Resources for building interactive tools, data visualizations, and microsites at CGD. This repo is the single source of truth for brand, analytics, and design standards for interactives — whether you're building a simple custom Plotly chart or a multi-page dashboard.

## How to use this

**IMPORTANT: If you're planning to build an interactive, loop in comms before you start.** Send [Jeremy Gaines](jgaines@cgdev.org) and your [communications manager](https://centerforglobaldevelop.sharepoint.com/SitePages/mediarelations.aspx) a message, and we'll help you figure out hosting, design, and analytics. The earlier we're involved, the smoother the process. If you show up with a completely-built interactive, we may say no, or need to rebuild it. Minor, early design and architecture designs can have major implications—10x faster speed, or hundreds of dollars per month in hosting vs. cents.

**If you're using Claude Code, ChatGPT Codex, or another LLM to build**, you can feed it the relevant files from this repo in your project context. Each document is written to be useful to both humans and LLMs.

This is an early-stage project, and we'll build out more materials and guidance.

## What's in here

### Reference documents (root)

- **[cgd-brand-reference.md](cgd-brand-reference.md)** — CGD's branding guide translated into a simple text document for LLM use. It includes color palette (hex, RGB, CSS custom properties, Tailwind config), fonts, logo usage, and core design elements like buttons and links. Start here for any visual work.

- **[analytics-tracking-standard.md](analytics-tracking-standard.md)** — A set of rules governing how we track user interactions across embedded interactives using GA4 and postMessage. Includes the event schema, implementation code, naming conventions, and a checklist for adding tracking to a new tool. Any interactive embedded on cgdev.org needs to follow this.

- **[interactive-coding-standard.md](interactive-coding-standard.md)** — Default technical standards for CGD custom interactives, including project structure, library choices, data handling, embedding, accessibility, security, performance, documentation, and QA.

### Skills (for Claude Code)

- **[skills/cgd-colors/](skills/cgd-colors/)** — A Claude Code skill created by Dany Bahar for CGD data visualization styling. Covers color palette ordering, chart typography, and figure export specs. Install this skill in your Claude Code project if you're producing charts or figures for CGD publications and want to be able to easily reference it with a slash command.

## Contact

For questions, to discuss a new interactive, or to request additions to this toolkit: [jgaines@cgdev.org](jgaines@cgdev.org).
