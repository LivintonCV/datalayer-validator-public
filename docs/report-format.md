# Report format

DLV exports findings in structured formats.

This repo includes:
- Example JSON: `examples/reports/report.example.json`
- Example CSV: `examples/reports/report.example.csv`
- JSON Schema: `schemas/dlv-report.schema.json`

---

## JSON report (high-level)

```json
{
  "reportVersion": "1.0.0",
  "generatedAt": "2026-02-24T00:00:00Z",
  "project": { "id": "storefront-web", "environment": "stage" },
  "summary": {
    "errors": 2,
    "warnings": 3,
    "info": 1,
    "eventsObserved": 12,
    "pagesObserved": 5
  },
  "findings": [
    {
      "severity": "error",
      "ruleId": "evt.page_view",
      "eventName": "page_view",
      "pageUrl": "https://example.com/",
      "path": "page.type",
      "expected": "one of [home, plp, pdp, cart, checkout]",
      "observed": "homepage",
      "message": "Invalid value for page.type",
      "hint": "Use the taxonomy value: home",
      "evidence": {
        "source": "dataLayer",
        "timestamp": "2026-02-24T00:00:00Z"
      }
    }
  ]
}
```

---

## CSV report (recommended columns)

Typical columns:
- `severity`
- `ruleId`
- `eventName`
- `pageUrl`
- `path`
- `expected`
- `observed`
- `message`
- `hint`

CSV is useful for sharing with non-technical stakeholders.
