# CGD Interactive Analytics Tracking Standard

## Purpose

This document defines how we track user interactions across CGD's embedded interactive tools (hosted on GitHub Pages, embedded via iframe on cgdev.org). It serves two purposes:

1. **Internal team reference** for anyone implementing or maintaining analytics on an interactive.
2. **LLM/AI context document** — include this in your prompt when asking an LLM to add event tracking to a new or existing interactive, so it implements tracking consistent with our standard.

---

## Architecture

CGD's main site runs Google Analytics 4 (GA4). Our interactives are single-page apps hosted on various platforms (most often GitHub Pages) and embedded in cgdev.org pages via `<iframe>`.

Because iframes run in their own browsing context, GA on the parent page cannot see events inside the iframe. Rather than adding a separate GA tag to each interactive (which would create fragmented sessions), we use **`postMessage`** to send event data from the iframe up to the parent page, where **Google Tag Manager (GTM)** receives the dataLayer event and fires GA4 Event tags. This keeps all analytics within the user's CGD session.

### Data flow

```
[Interactive in iframe]
        |
        | window.parent.postMessage(...)
        v
[CGD parent page — GTM Custom HTML listener tag]
        |
        | window.dataLayer.push(...)
        v
[GTM Custom Event triggers]
        |
        | GA4 Event tags
        v
[GA4 property]
```

---

## GA4 Custom Dimensions

The following event-scoped custom dimensions are registered in our GA4 property (Admin > Custom definitions). They allow us to break down events by tool, interaction type, and specific control in reports and explorations.

| Custom dimension name | Event parameter | Description |
|---|---|---|
| Interactive Name | `interactive_name` | Which tool generated the event |
| Action Type | `action_type` | Category of interaction |
| Action Label | `action_label` | Specific control interacted with |
| Action Value | `action_value` | Value selected/set (optional) |

If these are ever missing or need to be re-created (e.g. in a new GA4 property), note that GA4 cannot retroactively apply dimensions to events collected before the dimension was registered.

---

## postMessage Payload Contract

Every postMessage from an interactive to the parent page must be a **flat object** with a `type` discriminator and the event data at the top level. Do not nest parameters inside a sub-object.

### `interactive_view`

```json
{
  "type": "cgd_analytics",
  "event": "interactive_view",
  "interactive_name": "uk-visa-reform-figure1"
}
```

### `interactive_engagement`

```json
{
  "type": "cgd_analytics",
  "event": "interactive_engagement",
  "interactive_name": "uk-visa-reform-figure1",
  "action_type": "filter",
  "action_label": "sector_filter",
  "action_value": "Financial Services"
}
```

The `type: "cgd_analytics"` field is required. It acts as a namespace discriminator so the parent-side listener can efficiently ignore unrelated postMessages (e.g. from Flourish embeds, Tag Assistant, browser extensions).

---

## Event Schema

We use exactly **two event names** for all interactives. All specificity goes into event parameters.

### Event 1: `interactive_view`

Fired once when the interactive loads (or enters the viewport, if lazy-loaded).

| Parameter | Required | Description |
|---|---|---|
| `interactive_name` | Yes | Unique slug for the tool, e.g. `green-skills-map`, `quoda`, `uk-visa-reform-figure1` |

This event exists so we can calculate engagement rates (engagements / views).

### Event 2: `interactive_engagement`

Fired on each discrete user interaction worth tracking.

| Parameter | Required | Description |
|---|---|---|
| `interactive_name` | Yes | Same slug as above |
| `action_type` | Yes | One of the standard values below |
| `action_label` | Yes | Identifies the specific control, e.g. `country_filter`, `sort_by_rank`, `year_range_slider` |
| `action_value` | No | The value selected or set. Include when meaningful (e.g. a country name, preset name, slider value). Omit when it adds noise or doesn't apply. |

**Note:** `action_value` should be omitted entirely (not sent as an empty string) when it does not apply.

### Standard `action_type` values

Use only these values. Do not invent new ones without updating this document.

| `action_type` | When to use | Examples |
|---|---|---|
| `filter` | Any control that changes which data is shown or what a model calculates | Dropdown selection, checkbox toggle, slider adjustment, theme toggle, agency checkbox |
| `preset` | Bulk-setting multiple parameters at once, including resets | Scenario preset selection, group selector, "Select All", "Clear All", "Reset" |
| `detail_open` | Drilling into a specific item or expanding a disclosure | Accordion expand, modal open, map feature click that opens detail, `<details>` expand |
| `detail_close` | Closing a drilled-in view | Modal close, accordion collapse |
| `view_control` | Changing the display without changing the underlying data | Sort toggle, chart type switch, zoom/focus dropdown, time horizon toggle, detail-mode switch |
| `navigate` | Moving between sections or steps within the tool | Next/back buttons, tab/step navigation |
| `compare` | Adding, switching, or removing a comparison scenario | Add scenario, remove scenario, switch active scenario |
| `external_link` | Clicking a link that leaves the interactive | Project website link, source data link |
| `download` | User-initiated file export or data download | Download CSV button, Export to Excel, Download chart image |

### What NOT to track

- **Hover/tooltip events.** High volume, low signal, not actionable.
- **Map pan/zoom gestures.** Continuous input, not meaningful as discrete events.
- **Slider `input` events during drag.** Fire on `change` (mouseup/touchend) only, not on every pixel of movement.
- **Standard browser interactions** like scrolling, text selection, window resize.

### Cardinality principle

GA4 reporting degrades when event parameters contain large numbers of unique values (dimensions with 500+ unique values get bucketed into "(other)" in standard reports). All parameter values must come from bounded, controlled sets. Specifically:

- Never pass raw URLs, free-text input, arbitrary database IDs, or any value that could produce hundreds of unique strings.
- For `external_link` events, use a semantic destination label (e.g. `project_website`, `source_data`, `methodology_pdf`) instead of the literal URL. If detailed outbound click reporting is needed, rely on GA4's built-in outbound click tracking.
- If an interactive contains hundreds or thousands of distinct drillable items (e.g. a searchable project database), do not pass individual item IDs as `action_value`. Either omit `action_value` and rely on `action_label` alone, or pass a category-level value instead.
- If a parameter could exceed ~100–200 distinct values in normal usage, consider omitting or categorising it.
- If in doubt about whether a value set is too large, omit `action_value`.

The per-repo `TRACKING.md` files enumerate exactly which values each tool sends, which is where this is enforced in practice.

---

## Implementation: Iframe Side (Interactive)

Add a tracking utility to each interactive. The exact integration depends on the tool's framework, but the postMessage contract is the same.

### `interactive_name` and multi-interactive projects

Each distinct interactive (i.e. each separately embedded iframe) must have its own unique `interactive_name`. This is true even when multiple interactives share a single codebase or `tracking.js` file — for example, a project with six charts that are each embedded as separate iframes on the same cgdev.org page.

When a shared `tracking.js` serves multiple interactives, each HTML file must set `window.CGD_INTERACTIVE_NAME` before loading the shared script:

```html
<script>window.CGD_INTERACTIVE_NAME = 'uk-visa-reform-figure1';</script>
<script src="tracking.js"></script>
```

The tracking utility reads from this global and falls back to a default:

```javascript
const INTERACTIVE_NAME = window.CGD_INTERACTIVE_NAME || 'your-default-slug';
```

This approach keeps the interactive's identity explicit and co-located with the HTML file that defines it, rather than deriving it from filenames or query strings.

### Utility module

Create a file (e.g. `tracking.js`) that all event calls go through:

```javascript
// tracking.js

(() => {
  const PARENT_ORIGIN = 'https://www.cgdev.org';
  const INTERACTIVE_NAME = window.CGD_INTERACTIVE_NAME || 'your-default-slug';
  const VALID_ACTION_TYPES = new Set([
    'filter',
    'preset',
    'detail_open',
    'detail_close',
    'view_control',
    'navigate',
    'compare',
    'external_link',
    'download'
  ]);

  let viewTracked = false;

  function send(eventName, params) {
    if (typeof window === 'undefined' || !window.parent) return;
    window.parent.postMessage(
      Object.assign({ type: 'cgd_analytics', event: eventName }, params),
      PARENT_ORIGIN
    );
  }

  function trackView() {
    if (viewTracked) return;
    viewTracked = true;
    send('interactive_view', {
      interactive_name: INTERACTIVE_NAME
    });
  }

  function trackEngagement(actionType, actionLabel, actionValue) {
    if (!VALID_ACTION_TYPES.has(actionType)) return;
    const params = {
      interactive_name: INTERACTIVE_NAME,
      action_type: actionType,
      action_label: actionLabel
    };
    if (actionValue !== undefined && actionValue !== null && actionValue !== '') {
      params.action_value = String(actionValue);
    }
    send('interactive_engagement', params);
  }

  window.CGDTracking = {
    INTERACTIVE_NAME,
    trackView,
    trackEngagement
  };
})();
```

Key implementation notes:

- **`PARENT_ORIGIN`** is set to `https://www.cgdev.org`. postMessages will only be delivered when the parent frame is on that exact origin.
- **`viewTracked`** prevents duplicate view events if `trackView()` is called more than once.
- **`VALID_ACTION_TYPES`** rejects any action type not in the standard list, catching typos and non-standard values at the source.
- **Flat payload.** The `send()` function merges event parameters into the top-level postMessage object alongside `type` and `event`. Do not nest parameters inside a sub-object like `event_params`.

### Usage in application code

```javascript
// On load
CGDTracking.trackView();

// Filter change
countryDropdown.addEventListener('change', (e) => {
  CGDTracking.trackEngagement('filter', 'country_filter', e.target.value);
});

// Slider (fire on change, not input)
yearSlider.addEventListener('change', (e) => {
  CGDTracking.trackEngagement('filter', 'year_range_slider', e.target.value);
});

// Preset selection
presetDropdown.addEventListener('change', (e) => {
  CGDTracking.trackEngagement('preset', 'scenario_preset', e.target.value);
});

// Reset button
resetBtn.addEventListener('click', () => {
  CGDTracking.trackEngagement('preset', 'reset');
});

// Accordion expand
accordion.addEventListener('toggle', (e) => {
  const action = e.target.open ? 'detail_open' : 'detail_close';
  CGDTracking.trackEngagement(action, 'project_detail');
});

// External link
externalLink.addEventListener('click', () => {
  CGDTracking.trackEngagement('external_link', 'project_website');
});
```

If the project uses ES modules rather than the IIFE/global pattern, you can export `trackView` and `trackEngagement` directly instead of attaching them to `window.CGDTracking`.

### Naming conventions for `action_label`

- Use `snake_case`.
- Be descriptive but concise: `country_filter`, `year_range_slider`, `sort_by_rank`, `chart_type_toggle`.
- Prefix with the section or component name if the same control type appears in multiple places: `sidebar_agency_checkbox` vs `modal_theme_accordion`.
- Document all `action_label` values used in each tool's `TRACKING.md`.

### Naming conventions for `interactive_name`

- Use `kebab-case`.
- For single-interactive repos, should match the GitHub repo name or a clear short slug. Examples: `green-skills-map`, `quoda`, `visa-calculator`.
- For multi-interactive repos (multiple charts/tables in one project), use a shared prefix with a descriptive suffix like `uk-visa-reform-salary` or you can rely on figure and table numbering if available `uk-visa-reform-figure1`. This enables later grouping using a filter on the shared prefix.

---

## Implementation: Parent Page (CGD Site) — GTM Configuration

The parent-side listener runs entirely within **Google Tag Manager**. It consists of a Custom HTML tag that listens for postMessages, validates them, and pushes clean events into the GTM data layer, plus standard GTM triggers and GA4 Event tags that forward the data to GA4.

### GTM components

The full setup consists of five elements:

1. **One Custom HTML tag** — the postMessage listener
2. **Four Data Layer Variables** — one per event parameter
3. **Two Custom Event triggers** — one per event name
4. **Two GA4 Event tags** — one per event name

### 1. Custom HTML tag: postMessage listener

Tag name: `Listener - postMessage analytics for custom interactives via iframe`
Trigger: All Pages

```html
<script>
(function() {
  if (window.__cgdInteractiveListenerInstalled) return;
  window.__cgdInteractiveListenerInstalled = true;

  var ALLOWED_SUFFIXES = [
    '.github.io',
    '.shinyapps.io',
    '.amplifyapp.com',
    '.vercel.app',
    '.pages.dev',
    '.workers.dev'
  ];

  function isAllowedOrigin(origin) {
    try {
      var hostname = new URL(origin).hostname;
      return ALLOWED_SUFFIXES.some(function(suffix) {
        return hostname === suffix.slice(1) || hostname.endsWith(suffix);
      });
    } catch (e) {
      return false;
    }
  }

  window.addEventListener('message', function(messageEvent) {
    if (!isAllowedOrigin(messageEvent.origin)) return;

    var d = messageEvent.data;
    if (!d || typeof d !== 'object') return;
    if (d.type !== 'cgd_analytics') return;

    var eventName = d.event;
    if (eventName !== 'interactive_view' && eventName !== 'interactive_engagement') return;
    if (!d.interactive_name) return;
    if (eventName === 'interactive_engagement' && (!d.action_type || !d.action_label)) return;

    window.dataLayer = window.dataLayer || [];
    var pushObj = {
      event: eventName,
      interactive_name: d.interactive_name
    };

    if (eventName === 'interactive_engagement') {
      pushObj.action_type = d.action_type;
      pushObj.action_label = d.action_label;
      if (d.action_value != null && d.action_value !== '') {
        pushObj.action_value = d.action_value;
      }
    }

    window.dataLayer.push(pushObj);
  });
})();
</script>
```

Validation chain:

1. **Origin suffix allowlist** — only messages from known hosting platforms are accepted.
2. **Message namespace** — only messages with `type: 'cgd_analytics'` are processed. This efficiently ignores unrelated postMessages (Flourish resize events, Tag Assistant pings, browser extensions, etc.).
3. **Event name allowlist** — only `interactive_view` and `interactive_engagement` are forwarded.
4. **Required fields** — `interactive_name` is always required; `action_type` and `action_label` are required for engagement events.
5. **Duplicate listener guard** — the `__cgdInteractiveListenerInstalled` flag prevents duplicate listeners if GTM fires the tag more than once.

These checks ensure that:
- only trusted sources can send analytics events
- malformed or incomplete events are dropped early
- the dataLayer remains clean and predictable for GTM

### 2. Data Layer Variables

Create four User-Defined Variables in GTM, each of type **Data Layer Variable** (Version 2, no default value, no format):

| Variable name | Data Layer Variable Name |
|---|---|
| (your naming convention) | `interactive_name` |
| (your naming convention) | `action_type` |
| (your naming convention) | `action_label` |
| (your naming convention) | `action_value` |

### 3. Custom Event Triggers

Create two triggers of type **Custom Event**:

| Trigger name | Event name |
|---|---|
| `interactive_view` trigger | `interactive_view` |
| `interactive_engagement` trigger | `interactive_engagement` |

### 4. GA4 Event Tags

Create two tags of type **Google Analytics: GA4 Event**, both using the existing GA4 Configuration tag / Measurement ID.

**Tag: GA4 — interactive_view**

| Setting | Value |
|---|---|
| Event name | `interactive_view` |
| Parameter: `interactive_name` | `{{your interactive_name variable}}` |
| Trigger | `interactive_view` trigger |

**Tag: GA4 — interactive_engagement**

| Setting | Value |
|---|---|
| Event name | `interactive_engagement` |
| Parameter: `interactive_name` | `{{your interactive_name variable}}` |
| Parameter: `action_type` | `{{your action_type variable}}` |
| Parameter: `action_label` | `{{your action_label variable}}` |
| Parameter: `action_value` | `{{your action_value variable}}` |
| Trigger | `interactive_engagement` trigger |

### Hosting platform allowlist

The listener accepts messages from any origin whose hostname ends in one of these suffixes:

| Suffix | Platform |
|---|---|
| `.github.io` | GitHub Pages |
| `.shinyapps.io` | ShinyApps |
| `.amplifyapp.com` | AWS Amplify |
| `.vercel.app` | Vercel |
| `.pages.dev` | Cloudflare Pages |
| `.workers.dev` | Cloudflare Workers |

**If a new hosting platform is added**, its domain suffix must be added to the `ALLOWED_SUFFIXES` array in the GTM Custom HTML tag and the GTM container must be re-published. Without this, events from interactives on the new platform will be silently dropped. Deploying a new interactive to an *existing* platform requires no changes to GTM.

---

## Adding Tracking to a New Interactive: Checklist

1. **Choose an `interactive_name` slug.** Kebab-case, unique, matches the repo name where possible. For multi-interactive projects, use a shared prefix with a descriptive suffix or match to the figure/table name (e.g. `uk-visa-reform-salary` or `uk-visa-reform-figure1`).
2. **Copy `tracking.js` into the project** and set the default `INTERACTIVE_NAME`. If the project contains multiple interactives sharing one `tracking.js`, set `window.CGD_INTERACTIVE_NAME` in each HTML file before the script tag.
3. **Audit the interactive's controls.** Walk through every interactive element and classify each as one of the standard `action_type` values, or decide it's not worth tracking (hover, pan/zoom, etc.).
4. **Add `trackView()` once when the interactive is first rendered** (ensure it does not fire multiple times).
5. **Add `trackEngagement()` calls** at each relevant interaction point. Use `change` not `input` for sliders and continuous controls.
6. **Create or update `TRACKING.md`** in the repo root, documenting all `action_label` values (see the Per-Repo Tracking Documentation section for the required format).
7. **Test locally.** Open browser devtools, go to the Console, and verify postMessages are firing with correct payloads. Use a temporary listener:
   ```javascript
   window.addEventListener('message', (e) => console.log('postMessage:', e.data));
   ```
   Confirm each message is a flat object with `type: 'cgd_analytics'`, `event`, and the expected parameters at the top level.
8. **Deploy and test with GTM Preview.** Open GTM Preview mode, load the cgdev.org page with the embedded interactive, and verify that `interactive_view` and `interactive_engagement` events appear in the Tag Assistant timeline with correct parameter values.
9. **Verify in GA4.** Check GA4's Realtime report or DebugView to confirm events and parameters are arriving correctly.

---

## LLM Prompt Guidance

When asking an LLM (e.g. Claude Code, Codex) to add analytics tracking to a new interactive, include this document as context and use a prompt like:

> I'm adding analytics tracking to a new interactive called `[slug]`. Here is the codebase: [attach files or describe structure]. Following the tracking standard in the attached document, add the `tracking.js` utility and instrument all interactive elements with appropriate event tracking. Classify each interaction using the standard `action_type` values. Do not track hover events, map pan/zoom, or slider drag (only slider change). Fire `trackView()` once on load. Generate a `TRACKING.md` file for this repo documenting all tracked events (see the format specification in the standard).

For multi-interactive projects, add:

> This project has multiple interactives sharing one `tracking.js`. Each HTML file must set `window.CGD_INTERACTIVE_NAME` before loading the script. The names are: `[list slugs]`.

The LLM should:

- Use the exact flat `postMessage` contract defined here: top-level `type: 'cgd_analytics'` and `event` field, with all parameters (`interactive_name`, `action_type`, `action_label`, `action_value`) at the top level. **Do not nest parameters inside `event_params` or any other sub-object.**
- Only use `action_type` values from the standard list.
- Fire slider events on `change`, not `input`.
- Skip hover, pan/zoom, and other excluded interaction types.
- For multi-interactive projects, set `window.CGD_INTERACTIVE_NAME` in each HTML file and read it in `tracking.js` via `window.CGD_INTERACTIVE_NAME || 'default-slug'`.
- Generate a `TRACKING.md` file in the repo root following the format below.

The LLM must not introduce additional event names beyond `interactive_view` and `interactive_engagement`.

---

## Per-Repo Tracking Documentation (`TRACKING.md`)

Each interactive's repo should contain a `TRACKING.md` file in its root that inventories every tracked event. This keeps the documentation next to the code it describes and ensures it gets updated in the same PR that changes the tracking.

### Required format

```markdown
# Event Tracking: [Interactive Name]

`interactive_name`: `your-tool-slug`

Tracking implemented per [CGD Interactive Analytics Tracking Standard](link-to-this-doc).

## Tracked Events

| `action_type` | `action_label` | `action_value` | Notes |
|---|---|---|---|
| `filter` | `country_filter` | country name | Dropdown or map click |
| `preset` | `reset` | | Clears all inputs |
| ... | ... | ... | ... |

## Not Tracked

Brief note on what was intentionally excluded and why (e.g. "Map pan/zoom — continuous gesture, not actionable").
```

For multi-interactive projects, list all interactives and their events in a single `TRACKING.md`, with a separate table per `interactive_name`.

### Maintenance rule

Any PR that adds, removes, or renames a tracked interaction must update `TRACKING.md` in the same PR.
