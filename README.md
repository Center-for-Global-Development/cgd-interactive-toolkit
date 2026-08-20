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

Before you hand an interactive to comms, you should be able to answer **yes** to every item below. This is a final sign-off gate, not a how-to — the detailed requirements (and the code to copy) live in the three documents linked above. If you can't say yes to something, fix it or flag it for comms.

**Scope & process**
- [ ] I looped in comms before building, not after.
- [ ] This genuinely needs a custom interactive — it couldn't be a static image or a Flourish chart.

**Embedding & technical** (see `interactive-coding-standard.md`)
- [ ] It's built as the *inside* of an iframe: no outer border, shadow, background chrome, or outer margin/padding.
- [ ] It works responsively from ~320px to 1200px wide with no horizontal scroll.
- [ ] It has no fixed height, and the CGD iframe-resize postMessage script is implemented and resizes correctly after load *and* after interactions that change height.
- [ ] The interactive's controls — links, downloads, filters, reset — all behave as expected.
- [ ] Third-party libraries are pinned to exact versions (no `latest`) and loaded minified.
- [ ] The browser console shows no meaningful errors.
- [ ] There's no build step, backend, or live data fetch that comms hasn't signed off on.

**Brand & design** (see `cgd-brand-reference.md`)
- [ ] Colors come from the CGD palette, using the right system (categorical / sequential / polar / stoplight) for the data.
- [ ] Type uses Sofia Pro and follows the brand hierarchy.
- [ ] Static titles, captions, and source notes are left for the surrounding CMS text; only dynamic, control-driven titles live inside the embed.
- [ ] The chart follows data-viz best practices: meaningful title, direct labeling where feasible, no clutter, no rotated text.

**Accessibility** (see `interactive-coding-standard.md`)
- [ ] Every control works by keyboard, with visible focus states.
- [ ] Inputs have labels and meaning is never carried by color alone; contrast is readable.
- [ ] It stays usable at mobile widths and under browser zoom.

**Data & privacy** (see `interactive-coding-standard.md`)
- [ ] There is no PII or sensitive data, and no secrets, credentials, or API keys in the code or data.
- [ ] Data sources, dates, transformations, caveats, and licenses are documented.

**Analytics** (see `analytics-tracking-standard.md`)
- [ ] `tracking.js` is implemented with a unique `interactive_name`; `trackView()` fires once and engagement events fire on the relevant controls.
- [ ] `TRACKING.md` documents every tracked event.

**Documentation**
- [ ] The repo has a README covering what it is, where it's embedded, data sources, how to regenerate data, libraries/choices, and hosting notes.

## Deployment

Most CGD interactives will be hosted on GitHub Pages from the CGD GitHub organization. Comms will handle this, but if you'd like to have a deployed version as part of your building workflow, you can go to the repo's page -> settings -> pages -> deploy from a branch (pick main).

## Contact

For questions, to discuss a new interactive, or to request additions to this toolkit: [jgaines@cgdev.org](jgaines@cgdev.org).
