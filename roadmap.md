# SITE INDEX — Roadmap

This file is the **delivery-oriented** companion to [product-manifest.md](./product-manifest.md). The manifest is the source of vision, principles, and risks; this roadmap tracks **phases**, **what ships when**, and **how the repo currently lines up**.

---

## How to use it

- **Planning:** Pick work from “Near-term backlog” or the phase lists; keep manifest principles in mind (assumptions-first, Pareto, truth transparency).
- **Status:** The “Current implementation” section should be updated when meaningful slices land (no need to tick every tiny change).

---

## Product phases (from the manifest)

### Phase 1 — Core “Art” engine (budget and certification)

- Cómputos / **budget lines** (manual input first).
- APU with **category breakdown** (materials, labor, equipment), simple **mezclas** and unit conversions.
- Global progress certification.
- Basic assumption generation (e.g. spend vs. budget signals).
- **PWA foundation:** installable app, offline-capable baseline (local-first / sync when online).
- **Preset library:** starter budget-line / mix templates with standard yields (manifest asks for this in Phase 1).

### Phase 2 — “Science” engine (context and assumptions)

- Optional Revit integration and mapping into APU.
- Touch-Gantt schedule as a logic layer (imputation “when → where” where it matters).
- Factura and remito pipeline with **full OCR**, human correction, and assumptions where the model is uncertain.
- **Assumptions tab at full strength:** imputation engine, **ranking** driven by user mix (money / time / impact), confirmation queue UX.

### Phase 3 — Financial layer

- Subcontractor vs. jornales efficiency (back-calculated labor vs. budget).
- Price history, scraping/indexing, and **data quality / reliability** grading (beyond a single flaky flag).
- Cash flow from confirmed facts plus explicit assumptions on a timeline.

---

## Current implementation (repository snapshot)

_Accurate as of the last manual update to this section; cross-check `fe/` and `be/` when in doubt._

**In place today**

- Studio **slug** tenancy (`X-Studio-Slug` on API requests).
- Projects, **budget lines** (`BudgetLine` — three category amounts + ownership toggles + per-line currency and USD rate kind), **mix designs** (user-defined components / conversions in UI), **certifications**, **assumptions** (list + resolve), dashboard aggregates.
- **Declared actual spend** on `Project` (`actualSpendToDate`): editable in app settings; when it exceeds the summed budget from lines, the API opens a **`BudgetAlert`** (`SPEND_EXCEEDS_BUDGET`) and a matching **`Assumption`** (payload tagged for idempotency). Budget-line create/patch and project PATCH re-run the check.
- **Budget line ↔ mix design (Phase 1):** optional `mixDesignId` on each line; list API includes mix name; FE picker on líneas de presupuesto.
- **PWA baseline:** `vite-plugin-pwa` (manifest + precache shell + `autoUpdate` SW). API/data remain online-first until a sync design exists.
- Simple **truth signal:** `isFlaky` on budget lines (boolean), not yet a full reliability scale.

**Not yet in the repo (or only stubbed)**

- **Assumption ranking** from a persisted user mix (money / time / impact); API ordering is still basic (e.g. by creation time).
- **Per-item parametric options** (slab height, coats of paint, etc.) beyond linking a line to one mix design.
- **OCR** and expense logging for facturas/remitos.
- **Schedule / Gantt** and automatic imputation from dates.
- **Phase 3** financial and price-quality systems.

---

## Near-term backlog (suggested order)

Order can change; this matches “close the manifest gap” without pretending Phase 2/3 are done.

1. **Assumptions ranking** — store user weights (money / time / impact), sort/filter API + UI; keep Pareto as default bias.
2. **Offline / sync** — move PWA beyond precache: queued writes and conflict handling for API data.
3. **Reliability scale** — evolve past boolean `isFlaky` toward graded quality (schema + API + dashboard / budget-line visuals).
4. **Starter catalog** — preset budget-line / mix templates when you decide how to ship them (e.g. seeds, admin UI, or external tooling).

---

## Related paths in the monorepo

| Area     | Path  |
| -------- | ----- |
| Frontend | `fe/` |
| Backend  | `be/` |

Verification: `npm run full-check --prefix fe` and `npm run full-check --prefix be` from the parent folder that contains both (see [AGENTS.md](../AGENTS.md) at repo root).
