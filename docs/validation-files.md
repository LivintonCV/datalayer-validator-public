# Validation files and inputs

This document describes **what the validator reads** and how to reference it from rules.

## 1) dataLayer (Google Tag Manager style)

DLV inspects pushes like:

```js
window.dataLayer.push({
  event: "add_to_cart",
  ecommerce: { ... }
});
```

In rules, you reference the **event name** and **field paths**:
- `event` (string)
- nested paths such as `ecommerce.currency`, `user.id`, `page.type`

## 2) adobeDataLayer (Adobe Client Data Layer)

DLV can inspect events emitted to `window.adobeDataLayer`.
Typical patterns include objects containing:
- `event`
- `eventInfo`
- `page`
- `user`
- `productListItems` / `commerce` etc.

Field paths are expressed in dot notation, for example:
- `event`
- `eventInfo.eventName`
- `page.pageInfo.pageName`
- `user.profile.profileID`

## 3) Network validations (optional)

Depending on the configuration, DLV can validate analytics network calls, for example:
- Adobe Web SDK (`/ee/` calls)
- AppMeasurement / `b/ss` calls

Network validations should be treated as **optional** and used as a “second proof”.
When enabled, rules may target:
- query parameters
- payload fields
- specific endpoints

> The public spec does not include vendor-specific decoding logic; it only describes the rule format used to express expectations.

---

## Field path syntax

DLV uses a compact path syntax:
- Dot notation for objects: `page.type`
- Array indexing: `items[0].sku`
- Wildcards for arrays (if supported by the validator): `items[*].sku`

---

## Data types

Supported type keywords (recommended):
- `string`
- `number`
- `integer`
- `boolean`
- `object`
- `array`
- `null`

If the validator supports richer types, treat those as extensions (e.g., `email`, `url`).

---

## Severity

A rule can declare severity:
- `error` (blocks release)
- `warning` (should be fixed)
- `info` (visibility / best practice)

Severity is used for report summaries and filtering.
