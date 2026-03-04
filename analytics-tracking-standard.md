# CGD Interactive Analytics Tracking Standard

## Purpose

This document defines how we track user interactions across CGD's embedded interactive tools (hosted on GitHub Pages, embedded via iframe on cgdev.org). It serves two purposes:

1. **Internal team reference** for anyone implementing or maintaining analytics on an interactive.
2. **LLM/AI context document** — include this in your prompt when asking an LLM to add event tracking to a new or existing interactive, so it implements tracking consistent with our standard.

---

## Architecture

CGD's main site runs Google Analytics 4 (GA4). Our interactives are single-page apps hosted on GitHub Pages and embedded in cgdev.org pages via `<iframe>`.

Because iframes run in their own browsing context, GA on the parent page cannot see events inside the iframe. Rather than adding a separate GA tag to each interactive (which would create fragmented sessions), we use **`postMessage`** to send event data from the iframe up to the parent page, where the CGD site's existing GA4 tag fires the events. This keeps all analytics within the user's CGD session.

### Data flow

```
[Interactive in iframe]
        |
        | window.parent.postMessage(...)
        v
[CGD parent page listener]
        |
        | gtag('event', ...)
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

## Event Schema

We use exactly **two event names** for all interactives. All specificity goes into event parameters.

### Event 1: `interactive_view`

Fired once when the interactive loads (or enters the viewport, if lazy-loaded).

| Parameter | Required | Description |
|---|---|---|
| `interactive_name` | Yes | Unique slug for the tool, e.g. `green-skills-map`, `quoda`, `visa-calculator` |

This event exists so we can calculate engagement rates (engagements / views).

### Event 2: `interactive_engagement`

Fired on each discrete user interaction worth tracking.

| Parameter | Required | Description |
|---|---|---|
| `interactive_name` | Yes | Same slug as above |
| `action_type` | Yes | One of the standard values below |
| `action_label` | Yes | Identifies the specific control, e.g. `country_filter`, `sort_by_rank`, `year_range_slider` |
| `action_value` | No | The value selected or set. Include when meaningful (e.g. a country name, preset name, slider value). Omit when it adds noise or doesn't apply. |

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
- If in doubt about whether a value set is too large, omit `action_value`.

The per-repo `TRACKING.md` files enumerate exactly which values each tool sends, which is where this is enforced in practice.

---

## Implementation: Iframe Side (Interactive)

Add a tracking utility to each interactive. The exact integration depends on the tool's framework, but the contract is the same.

### Utility module

Create a file (e.g. `tracking.js`) that all event calls go through:

```javascript
// tracking.js

const PARENT_ORIGIN = 'https://www.cgdev.org';
const INTERACTIVE_NAME = 'your-tool-slug'; // change per tool

/**
 * Send a view event. Call once on load.
 */
export function trackView() {
  window.parent.postMessage({
    type: 'cgd_analytics',
    event_name: 'interactive_view',
    event_params: {
      interactive_name: INTERACTIVE_NAME
    }
  }, PARENT_ORIGIN);
}

/**
 * Send an engagement event.
 *
 * @param {string} actionType  - One of: filter, preset, detail_open, detail_close,
 *                                view_control, navigate, compare, external_link
 * @param {string} actionLabel - Specific control identifier, e.g. 'country_filter'
 * @param {string} [actionValue] - Optional value, e.g. 'Kenya' or '2023'
 */
export function trackEngagement(actionType, actionLabel, actionValue) {
  const params = {
    interactive_name: INTERACTIVE_NAME,
    action_type: actionType,
    action_label: actionLabel
  };
  if (actionValue !== undefined && actionValue !== null) {
    params.action_value = String(actionValue);
  }
  window.parent.postMessage({
    type: 'cgd_analytics',
    event_name: 'interactive_engagement',
    event_params: params
  }, PARENT_ORIGIN);
}
```

### Usage in application code

```javascript
import { trackView, trackEngagement } from './tracking.js';

// On load
trackView();

// Filter change
countryDropdown.addEventListener('change', (e) => {
  trackEngagement('filter', 'country_filter', e.target.value);
});

// Slider (fire on change, not input)
yearSlider.addEventListener('change', (e) => {
  trackEngagement('filter', 'year_range_slider', e.target.value);
});

// Preset selection
presetDropdown.addEventListener('change', (e) => {
  trackEngagement('preset', 'scenario_preset', e.target.value);
});

// Reset button
resetBtn.addEventListener('click', () => {
  trackEngagement('preset', 'reset');
});

// Accordion expand
accordion.addEventListener('toggle', (e) => {
  const action = e.target.open ? 'detail_open' : 'detail_close';
  trackEngagement(action, 'project_detail', projectId);
});

// External link
externalLink.addEventListener('click', () => {
  trackEngagement('external_link', 'project_website', url);
});
```

### Naming conventions for `action_label`

- Use `snake_case`.
- Be descriptive but concise: `country_filter`, `year_range_slider`, `sort_by_rank`, `chart_type_toggle`.
- Prefix with the section or component name if the same control type appears in multiple places: `sidebar_agency_checkbox` vs `modal_theme_accordion`.
- Document all `action_label` values used in each tool (see appendix).

### Naming conventions for `interactive_name`

- Use `kebab-case`.
- Should match the GitHub repo name or a clear short slug.
- Examples: `green-skills-map`, `quoda`, `visa-calculator`.

---

## Implementation: Parent Page (CGD Site)

A single global listener script on the CGD site receives postMessages from any embedded interactive and forwards them to GA4. It has zero cost on pages without iframes and does not interfere with existing DOM-based event tracking (accordion clicks, PDF downloads, etc.) — it only listens for `message` events from iframes, which are a completely separate browser event type.

The script validates incoming messages against:

1. **Origin suffix allowlist** — only messages from known hosting platforms are accepted.
2. **Message namespace** — only messages with `type: 'cgd_analytics'` are processed.
3. **Event name allowlist** — only `interactive_view` and `interactive_engagement` are forwarded to GA4. Sub-parameters (`action_type`, `action_label`, `action_value`) are passed through unvalidated.

### Hosting platform allowlist

The listener accepts messages from any origin ending in one of these suffixes:

| Suffix | Platform |
|---|---|
| `.github.io` | GitHub Pages |
| `.shinyapps.io` | ShinyApps |
| `.amplifyapp.com` | AWS Amplify |
| `.vercel.app` | Vercel |
| `.pages.dev` | Cloudflare Pages |
| `.workers.dev` | Cloudflare Workers |

**If a new hosting platform is added**, its domain suffix must be added to the `ALLOWED_SUFFIXES` array in the listener script on the CGD site. Without this, events from interactives on the new platform will be silently dropped. Deploying a new interactive to an *existing* platform requires no changes to the listener.

---

## Adding Tracking to a New Interactive: Checklist

1. **Choose an `interactive_name` slug.** Kebab-case, unique, matches the repo name where possible.
2. **Copy `tracking.js` into the project** and update `INTERACTIVE_NAME`.
3. **Audit the interactive's controls.** Walk through every interactive element and classify each as one of the standard `action_type` values, or decide it's not worth tracking (hover, pan/zoom, etc.).
4. **Add `trackView()` on load.**
5. **Add `trackEngagement()` calls** at each relevant interaction point. Use `change` not `input` for sliders and continuous controls.
6. **Create or update `TRACKING.md`** in the repo root, documenting all `action_label` values (see the Per-Repo Tracking Documentation section for the required format).
7. **Test locally.** Open browser devtools, go to the Console, and verify postMessages are firing with correct payloads. You can intercept them with a temporary listener:
   ```javascript
   window.addEventListener('message', (e) => console.log('postMessage:', e.data));
   ```
8. **Deploy and verify in GA4.** Use GA4's DebugView (or the Realtime report) to confirm events and parameters are arriving correctly.

---

## LLM Prompt Guidance

When asking an LLM (e.g. Claude) to add analytics tracking to a new interactive, include this document as context and use a prompt like:

> I'm adding analytics tracking to a new interactive called `[slug]`. Here is the codebase: [attach files or describe structure]. Following the tracking standard in the attached document, add the `tracking.js` utility and instrument all interactive elements with appropriate event tracking. Classify each interaction using the standard `action_type` values. Do not track hover events, map pan/zoom, or slider drag (only slider change). Fire `trackView()` once on load. Generate a `TRACKING.md` file for this repo documenting all tracked events (see the format specification in the standard).

The LLM should:

- Use the exact `postMessage` contract defined here (message `type: 'cgd_analytics'`, with `event_name` and `event_params`).
- Only use `action_type` values from the standard list.
- Fire slider events on `change`, not `input`.
- Skip hover, pan/zoom, and other excluded interaction types.
- Generate a `TRACKING.md` file in the repo root following the format below.

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

### Maintenance rule

Any PR that adds, removes, or renames a tracked interaction must update `TRACKING.md` in the same PR.
