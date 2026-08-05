# Astro usage

`TemporalDateRangePicker.astro` is a typed Astro component that wraps the [`<temporal-date-range-picker>`](usage-js.md#using-temporal-date-range-picker) custom element for use in Astro or [Astro Starlight](https://starlight.astro.build/) projects.

Haven't added the component to your project yet? See [Installation: Installing for Astro / Astro Starlight](install.md#installing-for-astro--astro-starlight) first.

## A note for newcomers to Astro

If you're new to Astro: components in Astro (`.astro` files) render to plain HTML at build time, similar to a template engine. Some Astro components need a special `client:*` directive (like `client:load`) to become interactive in the browser, because they're built on a JavaScript framework (React, Vue, etc.) that needs to "hydrate" — attach its interactive behavior — after the static HTML loads.

**This component doesn't need any of that.** `<temporal-date-range-picker>` is a native browser feature (a [custom element](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements)), not a framework component — it activates itself as soon as its own `<script>` tag runs in the browser, no `client:load` or similar directive required. This also means it works with Astro's default static site generation (SSG) output with zero extra configuration.

## Quick Start

```astro
---
import TemporalDateRangePicker from '../components/TemporalDateRangePicker.astro';
---
<TemporalDateRangePicker />
```

That's a complete, working picker. The component creates its own `<input>`, and by default it also renders the `<link>`/`<script>` tags it needs (from a CDN), so there's nothing else to add to your page.

## Configuring it with props

Props are camelCase versions of the same options described in [JavaScript usage: Attributes](usage-js.md#attributes) — the component translates them into the custom element's attributes for you.

```astro
<TemporalDateRangePicker
  timePicker
  opens="left"
  showWeekNumbers
  timePickerIncrement={15}
  minDate="2024-01-01"
  format="YYYY-MM-DD"
/>
```

The same boolean rule from [JavaScript usage](usage-js.md#attributes) applies here: most boolean props default to `false` (pass the prop to turn a feature on), but `showCustomRangeLabel`, `linkedCalendars`, and `autoUpdateInput` default to `true` — pass `{false}` explicitly to turn one of *those* off:

```astro
<TemporalDateRangePicker linkedCalendars={false} autoUpdateInput={false} />
```

### Full props list

| prop | type | notes |
|---|---|---|
| `startDate`, `endDate`, `minDate`, `maxDate` | `string` | Must match whatever `format` is in effect. |
| `opens` | `'left' \| 'right' \| 'center'` | Default `'right'`. |
| `drops` | `'down' \| 'up' \| 'auto'` | Default `'down'`. |
| `timePicker`, `timePicker24Hour`, `timePickerSeconds` | `boolean` | Default `false`. |
| `timePickerIncrement` | `number` | Minute increment for the time selects. |
| `singleDatePicker` | `boolean` | Default `false`. Shows one calendar instead of two. |
| `showDropdowns` | `boolean` | Default `false`. Adds year/month select boxes. |
| `minYear`, `maxYear` | `number` | Bounds for the dropdowns above. |
| `showWeekNumbers`, `showISOWeekNumbers` | `boolean` | Default `false`. |
| `alwaysShowCalendars` | `boolean` | Default `false`. |
| `autoApply` | `boolean` | Default `false`. Hides the Apply/Cancel buttons. |
| `showCustomRangeLabel` | `boolean` | Default `true` — pass `{false}` to opt out. |
| `linkedCalendars` | `boolean` | Default `true` — pass `{false}` to opt out. |
| `autoUpdateInput` | `boolean` | Default `true` — pass `{false}` to opt out. |
| `format` | `string` | e.g. `"YYYY-MM-DD HH:mm:ss"`. Default `"MM/DD/YYYY"`. |
| `separator` | `string` | Between the two formatted dates. Default `" - "`. |
| `applyLabel`, `cancelLabel` | `string` | Button text. |
| `placeholder`, `name`, `size` | `string \| number` | Passed to the auto-created `<input>`. Ignored if you provide your own via the `input` slot (see below). |
| `disabled` | `boolean` | Disables the (auto-created or slotted) input. |
| `id`, `class` | `string` | Applied to the `<temporal-date-range-picker>` element itself. |

For anything not on this list — `ranges`, `maxSpan`, `locale`, `isInvalidDate`, `isCustomDate`, `parentEl`, `callback` — see [Advanced: JS-only options](#advanced-js-only-options) below.

## Providing your own input

By default, the component creates a plain `<input type="text">` for you. If you want to control that input yourself — a different `type`, your own CSS classes, reusing an existing form field, a `<button>` instead — pass it into the `input` slot:

```astro
<TemporalDateRangePicker>
  <input type="text" class="my-styles" placeholder="Pick a range…" slot="input" />
</TemporalDateRangePicker>
```

This works because Astro slots compile down to plain static HTML: whatever you put in the `input` slot ends up as a direct child of `<temporal-date-range-picker>` in the page Astro generates — exactly where the custom element looks for an existing input to adopt, instead of creating its own.

## Advanced: JS-only options

`ranges`, `maxSpan`, `locale`, `isInvalidDate`, `isCustomDate`, `parentEl`, and `callback` (see [JavaScript usage: JavaScript-only properties](usage-js.md#javascript-only-properties)) aren't things you can pass as Astro props, since Astro components render to static HTML at build time and these are JavaScript values (functions, objects) that only make sense once the page is running in a browser. To use them, add a small `<script>` to the page that sets them on the element after it renders:

```astro
---
import TemporalDateRangePicker from '../components/TemporalDateRangePicker.astro';
---
<TemporalDateRangePicker id="my-picker" />

<script>
  const picker = document.getElementById('my-picker');
  picker.isInvalidDate = (date) => {
    const day = date.day(); // 0 = Sunday .. 6 = Saturday
    return day === 0 || day === 6; // disable weekends
  };
</script>
```

> Astro's `<script>` tags run once per page load, in the browser, exactly like a plain `<script>` in any HTML page — this isn't Astro-specific behavior, it's regular client-side JavaScript. It works whether the picker element was rendered before or after this script (see [JavaScript usage: JavaScript-only properties](usage-js.md#javascript-only-properties) for why).

## Loading the assets yourself

By default, each `<TemporalDateRangePicker />` renders the `<link>`/`<script>` tags it needs, pointing at the CDN build. Two props change that behavior:

- **`includeAssets`** *(default `true`)* — set to `false` to skip rendering the tags entirely. Use this if you've already added them once elsewhere (e.g. your layout's `<head>`) and don't want them repeated for every instance on the page. It's harmless either way if they *do* end up repeated — the library only registers the custom element once, no matter how many times its script runs.
- **`includePolyfill`** *(default `true`)* — set to `false` to skip the Temporal polyfill script specifically (e.g. if you only support browsers with native Temporal).

And three props change *where* the tags point:

| prop | default |
|---|---|
| `scriptSrc` | jsDelivr CDN build of `temporal-datetimerange-picker.js` |
| `styleHref` | jsDelivr CDN build of `temporal-datetimerange-picker.css` |
| `polyfillSrc` | jsDelivr CDN build of `@js-temporal/polyfill` |

Overriding these to point at files you've downloaded and placed in your project's `public/` folder lets your Astro site run without depending on any CDN at runtime — see [Installation: Using local copies instead of the CDN](install.md#using-local-copies-instead-of-the-cdn) for the full walkthrough and why you might want to.

```astro
<TemporalDateRangePicker
  scriptSrc="/vendor/temporal-datetimerange-picker.js"
  styleHref="/vendor/temporal-datetimerange-picker.css"
  polyfillSrc="/vendor/temporal-polyfill.js"
/>
```

## Using it in Astro Starlight

[Starlight](https://starlight.astro.build/) is a documentation-site template built on top of Astro — there's no special integration needed, the component is just a normal Astro component. Import and use it the same way inside a Starlight page's frontmatter (`.mdx` pages can also import and use Astro components), or inside a custom Starlight component/override.

```mdx
---
title: Book a slot
---

import TemporalDateRangePicker from '../../components/TemporalDateRangePicker.astro';

<TemporalDateRangePicker timePicker />
```

## What if a visitor's browser doesn't have Temporal?

Same fallback as everywhere else in this library: the bound input gets disabled with the text `"Requires Temporal"` instead of breaking the page. See the [main README](../README.md#behavior-without-temporal) for details. Leaving `includePolyfill` at its default (`true`) is what prevents this for browsers without native Temporal support.
