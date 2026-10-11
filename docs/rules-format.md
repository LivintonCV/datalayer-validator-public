# Rules format (Project Rules)

Rules are defined as a JSON document that describes:
- metadata (project name, owner, environment)
- naming conventions
- expected events
- field requirements & constraints
- optional hints and links

This repo includes:
- Example: `examples/rules/project.example.json`
- JSON Schema: `schemas/dlv-rules.schema.json`

---

## Top-level structure

```json
{
  "rulesVersion": "1.0.0",
  "project": {
    "name": "Example Storefront",
    "id": "storefront-web",
    "owner": "Analytics Team",
    "environments": ["dev", "stage", "prod"]
  },
  "conventions": {
    "eventName": {
      "pattern": "^[a-z]+(_[a-z0-9]+)*$",
      "examples": ["page_view", "add_to_cart", "purchase"]
    }
  },
  "events": [
    {
      "id": "evt.page_view",
      "name": "page_view",
      "severity": "error",
      "required": ["page.type", "page.name"],
      "fields": {
        "page.type": { "type": "string", "allowed": ["home", "plp", "pdp", "cart", "checkout"] },
        "page.name": { "type": "string", "minLength": 3 }
      },
      "hints": [
        { "message": "Ensure page.type matches the IA taxonomy.", "link": "https://example.com/taxonomy" }
      ]
    }
  ]
}
```

---

## Field constraints

Each field can define constraints:

- **type**: `string | number | integer | boolean | object | array | null`
- **required** (per event): list of field paths
- **allowed**: allow-list of values
- **pattern**: regex pattern (string)
- **minLength / maxLength**: for strings
- **min / max**: for numbers
- **nullable**: boolean (if supported)
- **default**: informational default

---

## Rule IDs and stability

- `events[].id` should be stable across versions to preserve report continuity.
- Use a namespace prefix (e.g., `evt.` `fld.` `net.`) for clarity.

---

## Cross-event rules (optional)

Some projects add rules that apply to multiple events, such as:
- “Every event must include `page.name`”
- “Every event must include `user.id` when logged in”

This can be represented using:
- `globalRequired`: array of field paths
- `globalFields`: constraints applied globally

See the JSON schema for supported keys.

---

## Optional keys read by version 2.0 of the extension

Version 2.0 of Data Layer Validator reads four more keys. All of them are optional and additive:
a file without them is read exactly as before, and `rulesVersion` stays `1.x`.

### `source` (on an event rule)

Where the event must come from. Without it, a rule applies to every event with its `name`.

```json
{ "id": "evt.purchase.gtm", "name": "purchase", "source": "gtm" }
```

| Value | What it is |
| --- | --- |
| `gtm` | a push to `window.dataLayer` |
| `adobe` | a push to `window.adobeDataLayer` |
| `adobe_websdk` | an Adobe Web SDK request; the event is named by its `xdm.eventType` and field paths start at the event (`xdm.…`, `data.…`) |
| `adobe_appmeasurement` | an AppMeasurement request, named `aa:request`; field paths are its query parameters |

A list is accepted too: `"source": ["gtm", "adobe"]`.

### `launchRule` (on an event rule)

The name of an Adobe Launch rule. A rule that carries it applies to the events attributed to that
Launch rule, and its `name` is then only a label. Names are compared ignoring case and repeated
spaces.

```json
{ "id": "evt.pdp", "name": "Product view", "launchRule": "Rule - Product View", "source": "adobe_websdk",
  "required": ["xdm.productListItems[0].SKU"] }
```

The extension attributes a request to the Launch rule that fired just before it, so the match is
approximate.

### `business` (on an event rule)

One sentence that says what a failure of this rule means outside the tracking team. The Guide tab
shows it under "What it means".

```json
{ "id": "evt.purchase", "name": "purchase", "business": "This sale will not appear in revenue reports." }
```

### `guide` (top level)

The steps of a guided run, for someone who does not read payloads. Each step names, by `id`, the
rules it expects to see.

```json
"guide": {
  "title": "Try a purchase",
  "intro": "Follow the steps on the site. Each one is marked as it is measured.",
  "steps": [
    { "id": "product", "do": "Open a product", "done": "The product view was measured.",
      "expect": ["evt.view_item"] },
    { "id": "purchase", "do": "Complete the purchase", "expect": ["evt.purchase"] }
  ]
}
```

- `title` and `steps` are required; a guide has between 1 and 50 steps.
- A step needs `id` (unique among steps), `do` (the instruction) and `expect` (at least one rule
  id that exists in `events`). `done` is the sentence shown when the step was measured without
  findings.
- A step is judged by the last event of each rule it expects: fine, with a warning, with a
  problem, or not seen. Seeing only some of its rules counts as a problem.

---

## How version 2.0 reads a plan

- Each event gets one of four states: correct, warning, error, or no rule. Without a plan every
  event is "no rule".
- A finding of severity `info` is listed and does not change the state of the event.
- `globalRequired` and `globalFields` apply to the events that have at least one rule.
- A path in `required` fails when it is missing, `null` (unless the field is `nullable` or of
  type `null`), or a text that is empty or blank.
- `conventions` is kept in the file and is not evaluated yet.
- Field paths use dots, `[n]` for a position in a list and `[*]` for every item: `items[*].sku`.
- A rules file made for version 1.9.17 (an object with `rules`, or a list of example payloads) is
  converted when it is loaded, and the extension says what the conversion could not keep. Rules
  files in JavaScript are not read.
