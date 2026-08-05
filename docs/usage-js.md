# JavaScript usage

This library gives you **two ways** to create a picker, both doing exactly the same thing underneath:

1. **`new DateRangePicker(...)`** — a JavaScript function you call yourself. Full control from code.
2. **`<temporal-date-range-picker>`** — an HTML tag (a ["custom element"](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements)) you can write directly in your markup, configured with attributes instead of JavaScript. Good when you'd rather not write any setup code at all.

Haven't installed the library yet? See [Installation](install.md) first.

## Quick Start

The fastest way to get a working picker on the page — write the tag, done:

```html
<temporal-date-range-picker></temporal-date-range-picker>
```

That's a complete, working date range picker, with no JavaScript of your own required. It creates its own text input; clicking it opens the calendar.

If you'd rather use the JavaScript function instead, here's the equivalent:

```html
<input type="text" id="my-picker">

<script>
  new DateRangePicker('my-picker');
</script>
```

Both produce the same picker. The rest of this guide covers configuring either one in detail.

## Which one should I use?

|  | `new DateRangePicker(...)` | `<temporal-date-range-picker>` |
|---|---|---|
| Where you configure it | JavaScript code | HTML attributes (plus a little JS for a few advanced options) |
| Feels like | Calling a function | Writing HTML, e.g. `<input>` or `<select>` |
| Good for | Pages already writing JS to wire things up; dynamic/computed options | Static markup, templating languages, CMSs, minimal JS |
| Where the popup calendar lives in the page | Wherever you tell it to (the `parentEl` option) | Same; defaults to the end of `<body>` |

They're not mutually exclusive — you can use both on the same page, and they share the exact same underlying engine, options, and behavior. Everything documented under [Working with dates](#working-with-dates) below applies equally to both.

---

## Using the DateRangePicker() function

```js
new DateRangePicker(bindElement, options, callback);
```

- **`bindElement`** — the `<input>` (or any element, e.g. a `<button>`) the picker attaches to. Pass either the element's `id` as a string, or a reference to the element itself (e.g. from `document.getElementById(...)` or `document.querySelector(...)`).
- **`options`** *(optional)* — a plain JavaScript object configuring the picker. See the [options reference](#options-reference) below. Omit it (or pass `{}`) to use all the defaults.
- **`callback`** *(optional)* — a function called every time the selected range changes: `function (startDate, endDate) { ... }`. `startDate`/`endDate` are the picker's date objects — see [Working with dates](#working-with-dates).

### Example

```html
<input type="text" id="datetimerange-input1" size="24" style="text-align:center">

<script>
  window.addEventListener('load', function () {
    new DateRangePicker('datetimerange-input1', {
      timePicker: true,
      opens: 'left'
    }, function (start, end) {
      console.log(start.format('YYYY-MM-DD') + ' to ' + end.format('YYYY-MM-DD'));
    });
  });
</script>
```

> Wrapping the setup in `window.addEventListener('load', ...)` isn't strictly required, but it's a safe default — it guarantees the `<input>` element (and any polyfill script) has finished loading before the picker tries to use it. If your `<script>` tag is placed *after* the `<input>` in your HTML, you likely don't need it at all.

Runnable versions of this exist in the repo's [`examples/`](../examples/) folder — see [`datetime-example.html`](../examples/datetime-example.html) and [`datetime-example-simple.html`](../examples/datetime-example-simple.html).

### Methods

Keep a reference to the object `new DateRangePicker(...)` returns, to call these later:

```js
let drp = new DateRangePicker('datetimerange-input1');
drp.setStartDate('2014/03/01');
drp.setEndDate('2014/03/03');
```

| method | description |
|---|---|
| `setStartDate(date)` | Changes the currently selected start date. |
| `setEndDate(date)` | Changes the currently selected end date. |
| `updateRanges(ranges)` | Replaces the predefined `ranges` list (see [options reference](#options-reference)) after the picker's already been created. |
| `remove()` | Tears down the picker: removes its event listeners and its calendar popup from the page. Use this if you're removing the bound input from the page yourself and want to clean up after the picker too. |

### Events

The picker fires plain [`CustomEvent`s](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent) on the element you bound it to, and these events *bubble* — meaning you can also listen for them on an ancestor element, or on `window`/`document`:

| event name | fires when |
|---|---|
| `show.daterangepicker` | the calendar popup is shown |
| `hide.daterangepicker` | the calendar popup is hidden |
| `showCalendar.daterangepicker` | the calendar(s) become visible (e.g. after clicking "Custom Range") |
| `hideCalendar.daterangepicker` | the calendar(s) are hidden again |
| `apply.daterangepicker` | the Apply button is clicked, or a predefined range is chosen |
| `cancel.daterangepicker` | the Cancel button is clicked |

```js
window.addEventListener('apply.daterangepicker', function (ev) {
  console.log(ev.detail.startDate.format('YYYY-MM-DD'));
  console.log(ev.detail.endDate.format('YYYY-MM-DD'));
});
```

`ev.detail` is the picker instance itself (the same object `new DateRangePicker(...)` returned), so `ev.detail.startDate`/`ev.detail.endDate` always reflect the picker's current selection.

---

## Using `<temporal-date-range-picker>`

```html
<temporal-date-range-picker time-picker opens="left" show-week-numbers></temporal-date-range-picker>
```

If you don't put an `<input>` or `<button>` inside the tag, one is created for you automatically. If you'd rather supply your own — to control its other attributes, its CSS classes, or to reuse an existing form field — put it directly inside the tag:

```html
<temporal-date-range-picker>
  <input type="text" placeholder="Pick a range…">
</temporal-date-range-picker>
```

### Attributes

Most options from the [options reference](#options-reference) are available as HTML attributes — take the option's name and convert it to kebab-case (lowercase, words separated by hyphens): `timePicker24Hour` becomes `time-picker-24-hour`, `singleDatePicker` becomes `single-date-picker`, `minDate` becomes `min-date`, and so on.

**Boolean attributes work a little differently than plain HTML here.** Normally in HTML, just writing an attribute's name with no value means "true", and leaving it off means "false" — e.g. `<input disabled>`. That's exactly how it works here too, *except* for three options that default to `true` in this library (`showCustomRangeLabel`, `linkedCalendars`, `autoUpdateInput`). For those, plain HTML's "leave it off for false" rule doesn't work, since leaving it off already means their default (`true`). To turn one of those **off**, write `="false"` explicitly:

```html
<!-- turns time-picker ON (this option defaults to false) -->
<temporal-date-range-picker time-picker></temporal-date-range-picker>

<!-- turns linked-calendars OFF (this option defaults to true) -->
<temporal-date-range-picker linked-calendars="false"></temporal-date-range-picker>
```

In short: **the attribute being present means "on", and `="false"` is the only way to mean "off"** — this rule applies consistently to every boolean option, whether it defaults to `true` or `false`.

### JavaScript-only properties

A few options aren't simple text or numbers, so they can't be written as HTML attributes — set them as JavaScript properties on the element instead:

| property | matches this option |
|---|---|
| `.ranges` | `ranges` |
| `.maxSpan` | `maxSpan` |
| `.locale` | `locale` (overrides the `format`/`separator`/`apply-label`/`cancel-label` attributes entirely, if set) |
| `.isInvalidDate` | `isInvalidDate` |
| `.isCustomDate` | `isCustomDate` |
| `.parentEl` | `parentEl` |
| `.callback` | the function you'd otherwise pass as the constructor's third argument |

```html
<temporal-date-range-picker id="my-picker"></temporal-date-range-picker>

<script>
  var picker = document.getElementById('my-picker');
  picker.isInvalidDate = function (date) {
    var day = date.day(); // 0 = Sunday .. 6 = Saturday
    return day === 0 || day === 6; // disable weekends
  };
  picker.callback = function (start, end) {
    console.log(start.format('YYYY-MM-DD') + ' - ' + end.format('YYYY-MM-DD'));
  };
</script>
```

You can set these properties either before or after the element is added to the page — the element figures out the right thing to do either way.

### Escape hatch and convenience methods

- **`.picker`** gives you the underlying picker object — the exact same kind of object `new DateRangePicker(...)` returns — so anything documented under [Methods](#methods) or [Events](#events) above works here too, e.g. `myElement.picker.setStartDate(...)`.
- For convenience, `.setStartDate(date)`, `.setEndDate(date)`, and `.updateRanges(ranges)` are also available directly on the element itself, without going through `.picker`.

A fuller, interactive tour of all of this lives in [`examples/custom-element-example.html`](../examples/custom-element-example.html), and [`examples/comprehensive-example.html`](../examples/comprehensive-example.html) shows many option combinations side by side using the JavaScript function.

### What if a visitor's browser doesn't have Temporal?

Both APIs handle this the same way: the bound input is disabled and its value is set to `"Requires Temporal"`, instead of the page breaking. See [`examples/missing-temporal-example.html`](../examples/missing-temporal-example.html) for a demo, and the [main README](../README.md#behavior-without-temporal) for details. Including the official Temporal polyfill (see [Installation](install.md)) is what avoids this for browsers that don't have Temporal built in yet.

---

## Working with dates

The `start`/`end` values you get back — from a `callback`, from an event's `ev.detail.startDate`/`endDate`, or from `.startDate`/`.endDate` on a picker instance — are the library's own lightweight date/time objects, built on the [Temporal API](https://tc39.es/proposal-temporal/docs/) under the hood. The one method you'll use constantly is `.format(...)`:

```js
start.format('YYYY-MM-DD');        // "2026-08-05"
start.format('MM/DD/YYYY HH:mm');  // "08/05/2026 14:30"
start.format();                    // ISO-ish default, e.g. "2026-08-05T14:30:00"
```

Format tokens: `YYYY`/`YY` (year), `MM`/`M` (month), `DD`/`D` (day), `HH`/`H` (24-hour), `hh`/`h` (12-hour), `mm`/`m` (minute), `ss`/`s` (second), `A`/`a` (AM/PM). Anything else in the format string (spaces, dashes, slashes, colons, other characters) is kept as-is, so you can build formats like `"YYYY年MM月DD日"` if you need to.

## Options reference

See the [main README's Options table](../README.md#options) for the complete list of options accepted by `new DateRangePicker(...)`. Every option there also has a matching attribute or property on `<temporal-date-range-picker>`, exactly as described above.
