# Report format

DLV exports findings in structured formats.

This repo includes:
- Example JSON: `examples/reports/report.example.json`
- Example CSV: `examples/reports/report.example.csv`
- JSON Schema: `schemas/dlv-report.schema.json`

---

## JSON report

`dlv-findings-<host>-<stamp>.json`, the whole export:

```json
{
  "reportVersion": "1.0",
  "exportedAt": "2026-02-24T00:00:00.000Z",
  "extension": "data-layer-validator",
  "version": "2.0.0",
  "run": {
    "id": "run-1",
    "name": "Checkout walkthrough",
    "origin": "https://example.com",
    "startedAt": 1771891200000,
    "endedAt": 1771891800000
  },
  "verdict": { "value": "not_ready", "reason": ["errors"] },
  "counts": { "errors": 1, "warnings": 1, "affectedEvents": 2, "notEvaluated": 0, "criticalMissing": 0 },
  "coverage": { "seen": 2, "total": 2 },
  "conformity": { "clean": 10, "total": 12 },
  "findings": [
    {
      "ruleId": "evt.page_view",
      "ruleName": "page_view",
      "critical": true,
      "severity": "error",
      "event": "page_view",
      "page": "https://example.com/",
      "field": "page.type",
      "expected": "one of [home, plp, pdp, cart, checkout]",
      "observed": "homepage",
      "hint": "Use the taxonomy value: home",
      "business": "Without this event no page is counted.",
      "at": 1771891205000,
      "launchRule": "Page view | Analytics"
    }
  ]
}
```

## The verdict

`verdict.value` is `ready` only when no finding is an error **and** every rule marked `critical`
was seen in the run. Otherwise it is `not_ready`, and `verdict.reason` says why, with one or more
of `errors`, `critical_missing` and `incomplete`. A judgement that could not be completed is never
`ready`: there is no score and no hidden weighting.

- `coverage`: rules seen over rules in the file, or `null` when the file has no rules.
- `conformity`: judged events without errors over judged events, or `null` when nothing was judged.
- `at` and the run times are milliseconds since the epoch, not strings.
- `launchRule` is empty when the event came from no named Adobe Launch rule.

---

## CSV report

`dlv-findings-<host>-<stamp>.csv`, the same findings, one per row, with a UTF-8 byte-order mark
and CRLF line endings so a spreadsheet opens it without a dialog. The columns, in order:

`ruleId`, `ruleName`, `critical`, `severity`, `event`, `page`, `field`, `expected`, `observed`,
`hint`, `business`, `at`, `launchRule`

In the CSV, `at` is written as an ISO date and a value that a spreadsheet would read as a formula
is quoted so it cannot run.

---

## HTML report

`dlv-report-<view>-<host>-<stamp>.html` is the same data as one self-contained page, in an
executive, analytics or engineering view. It carries no script and makes no request, and it is
meant to be printed to PDF.
