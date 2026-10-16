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

DLV also reads what the page sends, and a rule says which of those it applies to with `source`:

| `source` | What it matches |
| --- | --- |
| `gtm` | a push to `window.dataLayer` |
| `adobe` | a push to `window.adobeDataLayer` |
| `adobe_websdk` | an Adobe Web SDK request (`…/v<N>/interact` or `…/v<N>/collect`), recognised by the shape of its address, so a datastream served from a first-party domain counts |
| `adobe_appmeasurement` | an AppMeasurement call |

A rule without `source` matches an event from any of them. For a Web SDK request the field paths
are those of the payload, under `xdm.`: `xdm.eventType`, `xdm.commerce.order.priceTotal`,
`xdm.productListItems[*].SKU`.

> The public spec does not include vendor-specific decoding logic; it only describes the rule format used to express expectations.

---

## Field path syntax

DLV uses a compact path syntax:
- Dot notation for objects: `page.type`
- Array indexing: `items[0].sku`
- Wildcards for arrays: `items[*].sku` — the rule holds when every element matches

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

These seven are the whole list. There is no `email` or `url` type: express those with a
`pattern` on a `string`.

---

## Severity

A rule can declare severity:
- `error` (blocks release)
- `warning` (should be fixed)
- `info` (visibility / best practice)

Severity is used for report summaries and filtering.
