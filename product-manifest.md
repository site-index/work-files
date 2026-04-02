# SITE INDEX — Product Manifest (Execution Version)

_(Construction intelligence SaaS; Argentina-first; canonical app: https://app.siteindex.com; tenancy by studio slug.)_

## 1) Purpose

SITE INDEX is an active assistant for construction cost control.  
The system does not wait for perfect data: it keeps the project moving with assumptions, then asks users to validate decisions asynchronously.

Core promise:

- Protect margin by focusing on the highest-impact 10% of items.
- Make uncertainty explicit instead of hiding it.
- Let users act as auditors, not clerks.

## 2) Non-Negotiable Product Principles

- **Progressive obsession:** Fast defaults first; deep controls available on demand.
- **Truth transparency:** Every assumed or weak datum must be visibly marked and linked to action.
- **Asynchronous operations:** No blocking modal workflows for core project progress.
- **Pareto-first workflow:** Prioritize by economic impact by default, then allow custom weighting.
- **Friendly capture UX:** Expense/invoice entry must minimize typing and maximize guided actions.

## 3) Locked Decisions

### Platform and market

- Product name: **SITE INDEX**.
- Primary market: **Argentina**.
- Tenancy: single hostname + studio slug (`X-Studio-Slug`), no per-studio subdomains.
- Client shape: browser-first **PWA**, offline-capable from day one (local-first + sync).
- Notifications: in-app only (v1-v2).

### Currency and accounting

- Multi-currency is required.
- USD must support at least two operational contexts (official and informal).
- Budget, spend and reporting must preserve source currency and conversion context.

### Roles and permissions

- Roles are fixed to: `OWNER`, `MEMBER`, `VIEWER`, `DATA_ENTRY`.
- Permission baseline:
  - `VIEWER`: read-only.
  - `DATA_ENTRY`: create/update operational records, no admin configuration.
  - `MEMBER`: full project operations.
  - `OWNER`: member permissions + studio administration.

### Assumptions model

- Assumptions do not expire automatically.
- Every system-imputed decision is traceable in Assumptions.
- Assumptions queue sorting uses weighted mix: **money**, **time**, **impact**.

### Dosificaciones strategy (resolved)

- v1 uses a **hybrid** model:
  - seeded base library of common items with initial dosages,
  - plus full user customization and project-specific overrides.

### Reliability model (resolved)

- Reliability is a **graded score** (0-100), not binary.
- Standard bands:
  - 0-39: low reliability,
  - 40-69: medium reliability,
  - 70-100: high reliability.
- Score is driven by source quality, freshness, and confirmation evidence.

### Invoice/remito accounting rule (resolved)

- Every logged expense creates a fact record.
- Matching engine attempts to link each fact to existing assumed spend buckets.
- If matched: assumption is reconciled (partially or fully).
- If unmatched: record is treated as incremental spend and creates/updates deviation assumptions.

## 4) System Capability Model

### APU engine (3 pillars)

Each budget line is decomposed into:

- Materials,
- Labor,
- Equipment.

The system supports ownership split per pillar and compares planned vs real at the owned scope.

### Context layer

- Schedule context is optional in early phases.
- When present, schedule can drive cost imputation and urgency scoring.

### Assumptions layer

- Main operator workflow.
- Card types in v1/v2:
  - imputation confirmation,
  - spend vs budget deviation,
  - stale/weak price quality,
  - unknown/unplanned cost classification.

### Deviations and learning

- Progress and expenses continuously update variance signals.
- Recurrent drift can propose baseline updates for future budgets.

## 5) Roadmap with Exit Criteria

### Phase 1 — Core Budget Engine

Scope:

- Budget lines and project structure.
- APU pillars with editable dosages.
- Seeded base item library + project-level customization.
- Basic certification/progress tracking.
- Assumptions generation for spend-over-budget and manual overrides.
- PWA foundation with offline read/write + sync scaffolding.
- Fast mobile expense logging (manual, no OCR dependency).

Exit criteria:

- User can create project, build baseline budget, and update progress.
- User can log expenses from mobile in under 30 seconds per entry.
- Dashboard and assumptions update consistently after budget/spend changes.
- Offline edits sync without data loss in normal reconnect scenarios.

### Phase 2 — Science Engine

Scope:

- Assumptions queue UX (ranking, bulk actions, card-specific resolution).
- Matching engine for expense facts vs assumed spend.
- OCR MVP for invoices/remitos:
  - extract amount/date/currency/vendor/reference,
  - mandatory human confirmation before final posting.
- Optional schedule integration for assisted imputation.

Out of scope in this phase:

- Full automatic OCR posting without human validation.
- Advanced Revit automation beyond basic mapping assist.

Exit criteria:

- At least 80% of logged expense rows are matched or intentionally classified.
- Assumptions queue median time-to-resolution improves week over week.
- OCR MVP reaches stable operator flow with correction loop.

### Phase 3 — Financial Intelligence Layer

Scope:

- Labor efficiency (jornales vs contracted output).
- Price quality history and supplier intelligence.
- Cash-flow scenarios from confirmed facts plus unresolved assumptions.
- Reliability score progression and coaching/gamification cues.

Exit criteria:

- Project-level reliability score trend is visible and explainable.
- Cash-flow projections include confidence ranges.
- Users can identify top 3 margin risks in one screen.

## 6) Explicit Non-Goals (Now)

- No separate per-studio subdomain deployment model.
- No blocking wizard that requires perfect upfront data.
- No mandatory Gantt dependency for basic product value.
- No fully autonomous accounting decisions without human review.

## 7) Risks and Mitigations

- **Queue overload risk:** assumptions grow faster than resolution.
  - Mitigation: default Pareto sort, bulk low-impact resolution, SLA indicators.
- **Responsibility split complexity:** mixed ownership across pillars.
  - Mitigation: explicit ownership toggles and split-line modeling when needed.
- **Unknown spend risk:** expense not mapped to existing budget taxonomy.
  - Mitigation: unplanned bucket + high-priority classification assumption.
- **Low-evidence cash spend:** informal expenses without receipts.
  - Mitigation: frictionless mobile cash log with mandatory minimal metadata.
- **OCR confidence risk:** extraction errors propagate.
  - Mitigation: human-in-the-loop confirmation and low-confidence assumptions.

## 8) Glossary (Product -> Code)

- **Cómputo y Presupuesto** -> budget lines / quantity takeoff.
- **Dosificaciones** -> item yields / mix definitions.
- **Rubro** -> work category.
- **Supuestos** -> assumptions.
- **Desvíos** -> deviations.
- **Certificación** -> progress certification.

## 9) Open Questions (Limited, Explicit)

- Which minimal vendor identity fields are mandatory in OCR MVP?
- What is the default weighting preset for money/time/impact by role?
- Which seeded item families are mandatory for first commercial pilots?
