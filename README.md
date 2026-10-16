# Data Layer Validator — Validation Specs (Public)

This repository contains **documentation, rule/validation file formats, and examples** for the **Data Layer Validator** Chrome extension.

> ✅ **No extension source code is included here.**  
> This repo is intentionally limited to specs and examples so teams can review, version, and share validation logic safely.

---

## What is Data Layer Validator?

**Data Layer Validator (DLV)** helps Dev/QA, analytics engineers, and analysts validate that:

- `dataLayer` / `adobeDataLayer` events are **firing correctly**
- required fields are present and well-typed
- values follow agreed naming conventions and allowed sets
- analytics payloads (e.g., Adobe Web SDK / AppMeasurement) match expectations
- issues can be **exported** as structured reports: the findings as JSON and CSV, and a self-contained HTML report ready to print to PDF

---

## Why a “specs-only” public repo?

This repo enables:

- **Governance:** rule files can be reviewed in PRs (approval workflows, version history)
- **Consistency:** multiple projects share the same validation standards
- **Portability:** teams can re-use rule sets across domains/environments
- **Security:** no shipping of proprietary extension internals

---

## Repository structure

```
.
├─ README.md
├─ docs/
│  ├─ validation-files.md
│  ├─ rules-format.md
│  ├─ report-format.md
│  └─ glossary.md
├─ examples/
│  ├─ rules/
│  │  ├─ project.example.json
│  │  └─ expected-events.example.json
│  └─ reports/
│     ├─ report.example.json
│     └─ report.example.csv
└─ schemas/
   ├─ dlv-rules.schema.json
   └─ dlv-report.schema.json
```

---

## Quick start (how to use these files)

1. **Pick or create a rules file**
   - Start from: `examples/rules/project.example.json`
2. **Adjust the expected events and field rules**
3. In the extension (DLV), import the file under:
   - **Settings → Project → Import rules**
4. Run validation on your site, then export the report:
   - JSON/CSV (and PDF on Premium)

> The exact UI labels may vary by version, but the formats below stay consistent.

---

## Core concepts

### 1) Project rules
A single JSON file describes what “good” looks like for a project:
- event naming rules
- required fields per event
- data types, allowed values, regex patterns
- optional “severity” and “fix hints”

See: `docs/rules-format.md`

### 2) Validation sources
DLV can validate against:
- `window.dataLayer` pushes
- `window.adobeDataLayer` events
- analytics network calls (Adobe Web SDK / AppMeasurement) where applicable

See: `docs/validation-files.md`

### 3) Reports
DLV generates a structured report of findings:
- summary counts
- per-page / per-event issues
- severity, rule id, and suggested fix
- optional evidence (field path, observed value)

See: `docs/report-format.md`

---

## Advantages

- **Fast QA:** catch tagging issues early before release
- **Consistency:** enforce event contracts across teams
- **Traceability:** rules and changes live in Git
- **Actionable output:** findings include locations and fix guidance
- **Exportable:** share reports with stakeholders and vendors

---

## Versioning & compatibility

- Rule files include a `rulesVersion` field.
- New optional fields may be added without breaking older validators.
- Breaking changes increment the major version.

---

## Contributing

If you want to propose additions:
1. Add/adjust examples in `examples/rules/`
2. Update docs in `docs/`
3. Keep schema updates in `schemas/` in sync

---

## License

Documentation and examples in this repo are provided under your chosen license.
(If you publish this repo publicly, add a `LICENSE` file that matches your intent.)

---

## Contact

- Website: https://livinton.dev
- Business email: contact@livinton.dev
