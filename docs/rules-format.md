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
