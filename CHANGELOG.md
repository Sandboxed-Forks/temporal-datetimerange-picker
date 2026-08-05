# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [1.0.0] - 2026-08-05

Initial release under the `temporal-datetimerange-picker` name — a fork of
[Alumuko's `vanilla-datetimerange-picker`](https://github.com/alumuko/vanilla-datetimerange-picker)
with Moment.js replaced by the [Temporal API](https://tc39.es/proposal-temporal/docs/), plus a new
custom element, an Astro component, and expanded documentation. See [Credits](README.md#credits)
for the full lineage back to Dan Grossman's original jQuery-based `bootstrap-daterangepicker`.

### Added
- Temporal-backed date engine, replacing Moment.js entirely. Uses native `Temporal` where the
  browser supports it (Firefox 139+, Chrome/Edge 144+), falling back to the official
  [`@js-temporal/polyfill`](https://github.com/js-temporal/temporal-polyfill) elsewhere.
- Graceful degradation when neither native Temporal nor the polyfill is available: the bound
  `<input>` is disabled and shows `"Requires Temporal"` instead of the page breaking.
- Self-registering `<temporal-date-range-picker>` custom element — a declarative, thin wrapper
  around the same `DateRangePicker` engine, configurable via HTML attributes and JS properties,
  usable side-by-side with the classic `new DateRangePicker(...)` API.
- `astro/TemporalDateRangePicker.astro` — a typed Astro component wrapping the custom element for
  Astro / Astro Starlight projects, with an `input` slot for providing your own input/button, and
  `scriptSrc`/`styleHref`/`polyfillSrc` props for self-hosting the picker's assets instead of
  using the CDN.
- New example pages: `comprehensive-example.html`, `custom-element-example.html`, and
  `missing-temporal-example.html`.
- A `docs/` folder with beginner-friendly install and usage guides: CDN vs. download installation,
  plain JavaScript usage (both APIs), and Astro/Astro Starlight usage.

### Changed
- Project and all distributed files renamed from `vanilla-datetimerange-picker` to
  `temporal-datetimerange-picker`, so this fork isn't confused with Alumuko's still-active,
  Moment.js-based original.

### Fixed
- `remove()` no longer throws when called on a picker that never fully initialized (e.g. because
  Temporal wasn't available), and now actually removes its own popup element from the page instead
  of leaking it.
- `updateRanges()` no longer throws when called on a picker that started with no `ranges` option
  configured.
- The custom element's `disabled` attribute is now value-checked (`disabled="false"` correctly
  means *not* disabled) rather than presence-only, matching how frameworks like Astro serialize
  boolean props.

[Unreleased]: https://github.com/Sandboxed-Forks/temporal-datetimerange-picker/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/Sandboxed-Forks/temporal-datetimerange-picker/releases/tag/v1.0.0
