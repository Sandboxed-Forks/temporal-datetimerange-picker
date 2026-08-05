# Installation

This library has **no build step** and is **not published to npm**. "Installing" it means one of two things:

1. **Link to it from a CDN** — the browser downloads the files for you; you write no extra setup. Easiest for trying things out or for small/simple sites.
2. **Download the files and host them yourself** — you copy the files into your own project/site and serve them the same way you serve any other image, stylesheet, or script. A bit more setup, but you're not depending on a third-party CDN being available at runtime.

Both options give you the exact same library — pick whichever fits how you already manage static files.

> New to this? "CDN" stands for Content Delivery Network — a third-party service that hosts common files (like this library) so lots of different websites can share one copy instead of everyone hosting their own. [jsDelivr](https://www.jsdelivr.com/), used below, is a free CDN that can serve any file straight out of a public GitHub repository.

## What you need

Two files, always:

- **`temporal-datetimerange-picker.js`** — the picker itself. This one file defines *both* the `new DateRangePicker(...)` function *and* the `<temporal-date-range-picker>` custom element — you don't choose between them at install time, you get both either way, and can use whichever fits a given page. See [JavaScript usage](usage-js.md) for the difference.
- **`temporal-datetimerange-picker.css`** — the picker's styles (the calendar popup, buttons, etc.).

And, for browsers that don't have [the Temporal API](https://tc39.es/proposal-temporal/docs/) built in yet:

- **The official Temporal polyfill** (`@js-temporal/polyfill`) — gives the picker date/time math to work with on browsers that don't have it natively. As of writing, Temporal ships natively in Firefox 139+ and Chrome/Edge 144+; Safari doesn't have it yet. If you skip the polyfill and a visitor's browser lacks native Temporal, the picker doesn't crash your page — it just disables its input and shows `"Requires Temporal"` instead (see the [main README](../README.md#behavior-without-temporal) for details). Including the polyfill is what makes the picker actually work everywhere.

Optional:

- **`temporal-datetimerange-picker-dark.css`** (in this repo's [`extra/`](../extra/) folder) — a dark-theme stylesheet, used *instead of* the regular CSS file, not alongside it.

---

## Option 1: CDN

Add these tags to your page — typically the stylesheet in `<head>`, and the scripts right before the closing `</body>` tag:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/Sandboxed-Forks/temporal-datetimerange-picker@latest/dist/temporal-datetimerange-picker.css">

<!-- Only needed for browsers without native Temporal support yet (e.g. Safari) -->
<script src="https://cdn.jsdelivr.net/npm/@js-temporal/polyfill/dist/index.umd.js"></script>
<script src="https://cdn.jsdelivr.net/gh/Sandboxed-Forks/temporal-datetimerange-picker@latest/dist/temporal-datetimerange-picker.js"></script>
```

That's it — no `npm install`, no build tool, no package manager. Once these three tags are on the page, both `new DateRangePicker(...)` and `<temporal-date-range-picker>` are ready to use. Continue to [JavaScript usage](usage-js.md).

A couple of notes:

- The polyfill script is always safe to include, even on browsers that already have Temporal natively — the picker detects native support first and only uses the polyfill's copy if it needs to.
- `@latest` always points at the newest version, which means your site picks up updates automatically (and could change unexpectedly if you'd rather it didn't). See jsDelivr's own docs on [versioning GitHub-sourced files](https://www.jsdelivr.com/documentation#id-github) if you'd rather pin to a specific version.
- Want the dark theme instead? Swap the `.css` link for `.../extra/temporal-datetimerange-picker-dark.css` — see the [main README](../README.md#a-dark-version-css).

## Option 2: Download the files

If you'd rather not depend on an external CDN at runtime (your site needs to work offline, behind a corporate firewall, or you just prefer to control every file your site serves), download the files yourself and host them as part of your own site.

1. Get the files. There are two ways to do this:

   - **From a [GitHub Release](https://github.com/Sandboxed-Forks/temporal-datetimerange-picker/releases) (recommended).** Each release has the key files attached as individual downloads — `temporal-datetimerange-picker.js`, `temporal-datetimerange-picker.css`, and the Astro component (see below) — pinned to that exact version. This is also the best way to *know about* new versions: watch the repository (GitHub's "Watch" button, with "Releases only" if you don't want every discussion/issue) or just check the Releases page occasionally. Each release also has a matching entry in [`CHANGELOG.md`](../CHANGELOG.md) describing what changed.
   - **Straight from the repository.** Open [`dist/temporal-datetimerange-picker.js`](../dist/temporal-datetimerange-picker.js) or [`dist/temporal-datetimerange-picker.css`](../dist/temporal-datetimerange-picker.css) on GitHub, click the "Raw" button, then use your browser's Save Page As (or right-click and Save Link As). Optional dark theme: [`extra/temporal-datetimerange-picker-dark.css`](../extra/temporal-datetimerange-picker-dark.css). Unlike a release, these links always reflect whatever is currently on the default branch — handy if you specifically want the latest in-progress code, but not pinned to a known-working version the way a release is.

   Either way, you can also clone the whole repository if you'd rather have everything at once.

2. Also download the Temporal polyfill, if you need it (see "What you need" above) — save a copy of `https://cdn.jsdelivr.net/npm/@js-temporal/polyfill/dist/index.umd.js`.

3. Put the files somewhere in your own site — there's no required folder name or structure. A common convention is a `vendor/` or `lib/` folder alongside your other static assets, e.g.:

   ```
   my-site/
     css/
       temporal-datetimerange-picker.css
     js/
       temporal-datetimerange-picker.js
       temporal-polyfill.js
   ```

4. Reference them with regular relative (or absolute) paths instead of the CDN URLs:

   ```html
   <link rel="stylesheet" href="/css/temporal-datetimerange-picker.css">

   <script src="/js/temporal-polyfill.js"></script>
   <script src="/js/temporal-datetimerange-picker.js"></script>
   ```

Everything else works exactly the same as the CDN option — continue to [JavaScript usage](usage-js.md).

> **Keeping up to date:** since these are just static files you copied, they won't update themselves. Watch this repository's [Releases](https://github.com/Sandboxed-Forks/temporal-datetimerange-picker/releases) to find out when a new version is available, then re-download the file(s) to get the changes. The CDN option (`@latest`) updates automatically instead — that's the main tradeoff between the two approaches.

---

## Installing for Astro / Astro Starlight

The Astro component is a bit different from the JS/CSS files: it's not something a browser loads directly, it's *source code* that the Astro build tool compiles for you. Because of that, there's no CDN option for it — you always copy the file into your project.

1. Download `TemporalDateRangePicker.astro` — either attached to a [GitHub Release](https://github.com/Sandboxed-Forks/temporal-datetimerange-picker/releases) (recommended, pinned to a version) or as [`astro/TemporalDateRangePicker.astro`](../astro/TemporalDateRangePicker.astro) straight from the repository (always the current default branch). See "Get the files" above for more on the difference.
2. Place it in your Astro project's components folder — typically `src/components/`:

   ```
   my-astro-site/
     src/
       components/
         TemporalDateRangePicker.astro   <-- put it here
       pages/
         index.astro
   ```

3. Import and use it in any `.astro` page or component:

   ```astro
   ---
   import TemporalDateRangePicker from '../components/TemporalDateRangePicker.astro';
   ---
   <TemporalDateRangePicker />
   ```

That's the whole install. By default, the component still loads the picker's JS/CSS (and the Temporal polyfill) **from the same jsDelivr CDN** shown in Option 1 above — copying the `.astro` file only gets you the Astro *wiring*, not a fully self-hosted setup. See below if you want to avoid the CDN entirely.

For full usage details (props, the input slot, etc.), see [Astro usage](usage-astro.md).

### Using local copies instead of the CDN

The Astro component accepts three optional props that control where it loads its assets from:

| prop | what it's for | default |
|---|---|---|
| `scriptSrc` | URL for the picker's JS | jsDelivr CDN build |
| `styleHref` | URL for the picker's CSS | jsDelivr CDN build |
| `polyfillSrc` | URL for the Temporal polyfill's JS | jsDelivr CDN build |

If you followed "Option 2: Download the files" above and want your Astro site to use those local copies instead of the CDN, put the downloaded files in Astro's `public/` folder (anything placed there is served as-is, at the same path, by Astro) and point the props at them:

```
my-astro-site/
  public/
    vendor/
      temporal-datetimerange-picker.js
      temporal-datetimerange-picker.css
      temporal-polyfill.js
  src/
    components/
      TemporalDateRangePicker.astro
```

```astro
<TemporalDateRangePicker
  scriptSrc="/vendor/temporal-datetimerange-picker.js"
  styleHref="/vendor/temporal-datetimerange-picker.css"
  polyfillSrc="/vendor/temporal-polyfill.js"
/>
```

**Why you might want to do this:**

- **No third-party network dependency at page-load time.** If jsDelivr is ever slow, blocked (some corporate/school networks block CDNs), or down, your picker keeps working regardless — everything comes from your own domain.
- **Works fully offline / on an intranet.** Useful for internal tools, air-gapped environments, or local development without internet access.
- **Predictable versions.** The CDN default tracks `@latest`, which can change when a new version is released. A local copy only changes when *you* update it.
- **One fewer external request** for visitors, which can matter for strict Content-Security-Policy setups or privacy-conscious sites that avoid pulling resources from third-party domains.

The tradeoff is the same as Option 2 above: you're responsible for re-downloading the files yourself when you want a newer version.

If a page uses the component many times, you can also render the tags once (in your layout's `<head>`) instead of per-instance — see the `includeAssets` prop in [Astro usage](usage-astro.md).
