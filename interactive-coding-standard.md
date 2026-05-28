# CGD Interactive Coding Standard

## Purpose

This document defines the default technical standards for CGD custom interactives: data visualizations, small dashboards, calculators, maps, and microsites that are typically hosted as static files and embedded on cgdev.org.

The goal is to keep interactives simple, durable, fast, accessible, and easy for future staff or contractors to maintain. If a project needs a heavier architecture, live data pipeline, backend service, or build step, talk to comms before starting.

## Core Principles

- Prefer the simplest stack that meets the editorial and user need.
- Optimize for static hosting and long-term maintainability.
- Keep each interactive embeddable, responsive, and visually compatible with cgdev.org.
- Make data sources and transformations reproducible.
- Do not introduce licensing, security, privacy, or hosting obligations without comms review.

## Project Structure

Use one repository per project. A "project" usually means a blog, paper, report, or campaign. A closely related family of outputs may share one repository if the interactives use the same data, design system, or publication workflow.

All relevant interactives for that project should live in the same repository. For projects with multiple charts, each separately embedded chart should usually be its own HTML file.

Recommended structure for a small single-interactive project:

```text
project-name/
  index.html
  README.md
  TRACKING.md
  data/
    source-or-processed-data.csv
  scripts/
    prepare-data.js
```

Recommended structure for a multi-interactive project:

```text
project-name/
  figure-1.html
  figure-2.html
  table-1.html
  README.md
  TRACKING.md
  data/
  scripts/
  shared/
    styles.css
    utils.js
```

Use descriptive, stable names for repositories and files. Prefer short kebab-case names such as `green-skills-map`, `figure-1.html`, or `salary-threshold-calculator.html`.

## Single-File vs. Shared Files

For small interactives, it is acceptable and often preferable to keep CSS and JavaScript inside the HTML file:

- CSS in a `<style>` block in the document head.
- JavaScript in a `<script>` block near the end of the body.
- Small data embedded directly in the page.

Break CSS, JavaScript, or data into separate files when:

- Multiple interactives share meaningful duplicated code.
- The HTML file is becoming hard to scan or maintain.
- The same dataset is used by multiple pages.
- The data is large enough that a separate JSON or CSV file improves load time or maintainability.

Avoid adding a build system just to split files. If a project needs React, Vite, server-side code, package bundling, or other build tooling, discuss it with comms first.

## Libraries

Default to vanilla HTML, CSS, and JavaScript, with SVG or canvas where appropriate.

Recommended escalation path:

1. Vanilla HTML/CSS/JS, SVG, or canvas.
2. Chart.js for common interactive charts.
3. Plotly when built-in chart interactions are worth the extra weight.
4. D3 only when the visualization logic or layout truly requires it.

Maps should generally use Leaflet. Confirm basemap licensing, terms, and attribution before launch; do not assume "no API key" means unrestricted use.

Do not use Highcharts (licensing), ECharts (weight and complexity), Shiny (server-side needs), or React or any other tool with a build step, without talking to comms.

## CDNs and Dependencies

- Prefer jsDelivr for third-party libraries unless there is a reason to use an official vendor CDN.
- Pin exact versions. Do not use `latest`.
- Prefer minified bundles for user-facing pages.
- Use the smallest suitable bundle, such as Plotly partial bundles instead of full Plotly when possible.
- Use Subresource Integrity (SRI) hashes when feasible, with the required `crossorigin` attribute.
- Keep the dependency list short. Every dependency should have a clear purpose.

## Data

Embed small datasets directly in the HTML file when that keeps the project simpler. As a rough default, data under 50 KB minified can stay inline.

Use a separate data file when:

- Multiple visualizations use the same data.
- The dataset is large enough to noticeably affect page load or readability.
- The data will be reviewed or updated separately from the HTML.
- The same processed data should support downloads or documentation.

Prefer preprocessed data over heavy client-side transformation. If the interactive needs many derived columns, joins, filters, or aggregates, create a script that prepares the data in advance and load the processed output.

Do not live-fetch from an API, spreadsheet, or off-site data source without comms review. Do not add automated data updates, such as via GitHub Actions, unless the maintenance plan is clear.

Absolutely no PII. If there is any uncertainty about whether data is sensitive, stop and ask before publishing.

Document data sources, transformations, dates, caveats, and licenses in the project README or a separate data note.

## Embedding

Most CGD interactives are embedded in cgdev.org via iframe. Build the interactive as the inside of that iframe, not as a standalone page with its own outer frame.

Required defaults:

- Implement the CGD iframe resize postMessage script.
- Implement analytics tracking according to `analytics-tracking-standard.md`.
- Use no outer chrome: no default border, shadow, rounded outer container, or decorative wrapper.
- Use no outer margin or padding on the root element; the host page controls spacing.
- Use a transparent background by default unless the design intentionally requires a background.
- Work responsively from about 320px to 1200px wide without horizontal scrolling.
- Avoid fixed heights; let the resize script report content height to the parent page.
- Add `rel="noopener noreferrer"` to outbound links that use `target="_blank"`.

Each separately embedded iframe should have its own unique analytics `interactive_name`.

### Iframe Resizing

An iframe runs in a separate, cross-origin browsing context, so cgdev.org cannot read the interactive's content height directly and a fixed iframe height would either clip content or leave empty space. Instead, the interactive measures its own height and reports it up to the parent page via `postMessage`. A listener on cgdev.org receives that message and sets the iframe's `height` to match. This is what lets embeds grow and shrink with their content, with no inner scrollbar or trailing gap.

This is a two-part contract:

1. **The embed side** (the interactive you build) sends its content height up to the parent. You implement this.
2. **The parent side** (cgdev.org) listens for those messages and resizes the iframe. This is already deployed; the script is included below for reference so you know exactly what message shape your interactive must send.

#### Message contract

Each message your interactive sends must be a flat object with:

- `type: 'cgd-iframe-resize'` — the discriminator the parent listener filters on.
- `height` — the content height in CSS pixels, as a finite positive number.

Send it to the target origin `https://www.cgdev.org` (the same parent origin used for analytics postMessages in `analytics-tracking-standard.md`). The parent listener only accepts messages from CGD's hosting platforms (GitHub Pages and Cloudflare Workers), so the interactive must be served from one of those origins for resizing to work in production.

#### Embed-side implementation

Include this script in every embedded interactive. It reports the height once after initial layout, again once web fonts load, and on any later change to content height (responsive reflow, expanding panels, filtered data, etc.):

```html
<script>
(function () {
  var PARENT_ORIGIN = 'https://www.cgdev.org';
  var lastHeight = -1;

  function measure() {
    // The host sets height on the <iframe> element, not on <body>, so <body>
    // is never stretched and this value shrinks as well as grows.
    return Math.ceil(document.body.getBoundingClientRect().height);
  }

  function report() {
    var height = measure();
    if (height <= 0 || height === lastHeight) return;
    lastHeight = height;
    window.parent.postMessage({ type: 'cgd-iframe-resize', height: height }, PARENT_ORIGIN);
  }

  // Initial layout, plus a re-report once web fonts settle (they can change
  // text wrapping and therefore height).
  window.addEventListener('load', report);
  if (document.fonts && document.fonts.ready) {
    document.fonts.ready.then(report);
  }

  // Any later change to content height.
  if (window.ResizeObserver) {
    new ResizeObserver(report).observe(document.body);
  } else {
    window.addEventListener('resize', report);
  }
})();
</script>
```

Key implementation notes:

- **Measure `document.body`, not `document.documentElement`.** Because the parent sets the height on the `<iframe>` element rather than on the document, `<body>` tracks the true content height and reports correctly when content both grows and shrinks. Measuring the document element instead can leave it "stuck" at the previous larger height.
- **Keep margin and padding off the root.** Per the embedding defaults above, the root element should have no outer margin. A top or bottom margin on `<body>` (or a margin that collapses through it) is not always included in the measured height and can cause slight clipping. Use padding inside a wrapper element if you need internal spacing.
- **The `lastHeight` guard** suppresses duplicate messages when the measured height has not changed, including the no-op resize events the `ResizeObserver` fires after the parent applies the new height.
- **`ResizeObserver` is the primary trigger.** It covers viewport changes and any DOM/content change that affects height, so you usually do not need to call `report()` manually after interactions. If a height change happens entirely outside the observed box, call `report()` directly after it.
- **Local testing.** `postMessage` only delivers to `https://www.cgdev.org`, so resizing will not visibly work against a `localhost` parent. To test the message flow locally, temporarily change `PARENT_ORIGIN` to `'*'` (never ship this), or log the outgoing payload and confirm it is a flat object with `type: 'cgd-iframe-resize'` and a sane numeric `height`.

#### Parent-side listener (reference — already deployed on cgdev.org)

You do not need to deploy this; it runs on the CGD website and listens for resize messages from interactives hosted on CGD's GitHub Pages organization or Cloudflare Workers tenant. It is shown here so the embed-side contract above is unambiguous.

```html
<script>
(function () {
  var exactAllowedOrigins = [
    'https://center-for-global-development.github.io'
  ];
  var maxHeight = 20000; // sanity cap against runaway height reports
  function isAllowedOrigin(origin) {
    try {
      var url = new URL(origin);
      if (url.protocol !== 'https:') return false;
      if (exactAllowedOrigins.indexOf(origin) !== -1) return true;
      if (url.hostname.endsWith('.cgdev.workers.dev')) return true;
      return false;
    } catch (err) {
      return false;
    }
  }
  window.addEventListener('message', function (e) {
    if (!e.data || e.data.type !== 'cgd-iframe-resize') return;
    if (!isAllowedOrigin(e.origin)) {
      console.warn('[cgd-iframe-resize] rejected message from origin:', e.origin);
      return;
    }
    var height = Number(e.data.height);
    if (!Number.isFinite(height) || height <= 0) return;
    height = Math.min(Math.ceil(height), maxHeight);
    document.querySelectorAll('iframe').forEach(function (iframe) {
      if (iframe.contentWindow === e.source) {
        iframe.style.height = height + 'px';
      }
    });
  });
})();
</script>
```

## Accessibility

Accessibility is a baseline requirement, not a polish pass.

At minimum:

- All controls must be usable by keyboard.
- Focus states must be visible.
- Use semantic HTML controls where possible: buttons, links, labels, inputs, selects, and tables.
- Every input must have a visible or programmatic label.
- Do not rely on color alone to communicate meaning.
- Maintain readable color contrast.
- Ensure text remains readable and layouts remain usable at mobile widths and browser zoom.
- Give SVG charts meaningful titles/descriptions where appropriate.
- For canvas-based charts, including Chart.js, provide an accessible name and useful fallback or adjacent text/table equivalent where feasible.
- Respect reduced-motion preferences for animation-heavy interfaces.

For complex charts, consider providing a short text summary, data table, or downloadable data file so the underlying information is not available only visually.

## Security and Privacy

- Do not commit secrets, private keys, tokens, credentials, or private API keys.
- Do not rely on frontend environment variables for secrets. Anything shipped to the browser is public.
- If a project truly requires a secret, backend service, or proxy, talk to comms before building.
- Sanitize or validate user input before using it in the DOM, URLs, queries, or calculations.
- Avoid `innerHTML` for user-controlled content.
- Do not collect user-entered personal information unless the project has explicit approval.

## Performance

Keep pages lightweight. Static embeds should load quickly on mobile connections and should not delay the surrounding article page.

Default practices:

- Load only the libraries and data needed for the current interactive.
- Prefer SVG or lightweight DOM rendering for simple charts.
- Minify third-party libraries and large local assets.
- Precompute expensive transformations.
- Avoid large images, full library bundles, and unused framework code.
- Test on a narrow viewport and a typical laptop viewport before launch.

If an interactive needs a large dataset or library, document why in the README.

## Documentation

Each project repository should include a README with:

- What the interactive is and where it is embedded.
- Data sources, dates, transformations, and caveats.
- How to regenerate processed data.
- Libraries and major technical choices.
- Hosting/deployment notes.
- Known limitations or maintenance notes.

Each project that sends analytics events must include a `TRACKING.md` file as described in `analytics-tracking-standard.md`.

## QA Checklist

Before publishing, verify:

- The page works at mobile and desktop widths.
- There is no horizontal scroll inside the iframe.
- The iframe resize script works after load and after user interactions that change height.
- Analytics postMessages fire with the expected event names and parameters.
- Controls work with keyboard only.
- Links, downloads, filters, and reset controls behave correctly.
- The browser console has no meaningful errors.
- Data sources and transformations are documented.
- No secrets, private data, or PII are present.
- Third-party library versions are pinned.

## Git and Maintenance

Use clear, descriptive commit messages. Commit related changes together, but avoid one giant commit that mixes data, design, code, and documentation changes without explanation.

Do not over-engineer for hypothetical future updates. Add structure when it makes the current project easier to understand, test, or maintain.

## LLM Prompt Guidance

When asking an LLM to build or revise a CGD interactive, include this document, `cgd-brand-reference.md`, and `analytics-tracking-standard.md` in the project context.

A useful prompt:

> Build this as a static CGD iframe-embedded interactive following the CGD Interactive Coding Standard. Prefer vanilla HTML/CSS/JS unless a library is clearly justified. Keep the page responsive from 320px to 1200px, with no outer chrome or fixed iframe height. Add the CGD iframe resize behavior and analytics tracking per `analytics-tracking-standard.md`. Document data sources, transformations, dependencies, and technical choices in the README.
