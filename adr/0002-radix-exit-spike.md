# ADR 0002: Radix Exit Spike

## Status

Accepted (Wave 1 overlay rollout)

## Context

We evaluated whether replacing Radix across the frontend is worth it, triggered by repeated friction in nested overlay flows (modal + popover + select), especially around `/budget-lines` "Nueva linea".

The spike goal was to measure real tradeoffs with code and checks, not intuition.

## Baseline Inventory

### Current Radix surface

- `32` UI wrapper files in `fe/src/components/ui` import one or more `@radix-ui/*` primitives.
- `26` feature-level files import from `@/components/ui/*`, many transitively depending on Radix behavior.
- High-friction current overlay stack appears in:
  - `fe/src/components/CreateBudgetLineDialog.tsx`
  - `fe/src/components/budget-line-create-description-field.tsx`
  - `fe/src/components/EditBudgetLinePricingSheet.tsx`
  - `fe/src/components/BudgetLineItemYieldSelect.tsx`

### Complexity tiers by primitive

- **High (overlay/focus/portal heavy):** `dialog`, `sheet`, `popover`, `select`, `command`, `toast`, `dropdown-menu`, `menubar`, `navigation-menu`, `context-menu`, `hover-card`.
- **Medium (stateful controls):** `tabs`, `accordion`, `radio-group`, `switch`, `slider`, `toggle-group`, `collapsible`.
- **Low (thin wrappers):** `label`, `separator`, `aspect-ratio`, `avatar`, `progress`, `checkbox`, `button` (uses Slot), `form` (uses Slot + Label composition).

## Candidate Selection

We scored 3 non-Radix candidates against this project's pain points.

### Evaluation criteria

1. Modal + nested suggestion list + select reliability
2. Accessibility defaults (focus trap, keyboard nav, aria semantics)
3. API and implementation complexity
4. Mobile/touch scroll lock behavior
5. Migration blast radius

### Candidates

1. **Headless UI**
   - Strengths: simple API, strong dialog primitives, good React ergonomics, low setup cost.
   - Risks: still requires careful composition for complex nested overlays.
2. **React Aria Components**
   - Strengths: very strong a11y model and broad component coverage.
   - Risks: larger conceptual/API shift, longer migration and refactor time.
3. **Ariakit**
   - Strengths: accessible low-level primitives, flexible composition.
   - Risks: steeper learning curve, less familiar patterns for this codebase.

### Candidate score (1-5)

| Candidate             | Reliability | A11y defaults | API simplicity | Migration effort (higher is easier) | Total |
| --------------------- | ----------- | ------------- | -------------- | ----------------------------------- | ----- |
| Headless UI           | 4           | 4             | 5              | 4                                   | 17    |
| React Aria Components | 5           | 5             | 3              | 2                                   | 15    |
| Ariakit               | 4           | 4             | 3              | 2                                   | 13    |

Selected POC stack: **Headless UI**.

## POC Implemented

Implemented in `fe/src/components/CreateBudgetLineDialog.tsx`.

### What changed

- Replaced Radix dialog stack in this flow with `@headlessui/react` `Dialog`.
- Removed portal-container hacks in this flow.
- Introduced internal scrolling body with fixed header/footer layout.
- Replaced Radix `Select` usage in this dialog with native `select` controls.
- Replaced Radix popover/command suggestion UI in this dialog with an inline anchored suggestion panel.

### Validation

Ran:

```bash
npm run full-check --prefix fe
```

Result: pass (format, lint, typecheck, tests).

## Side-by-Side Comparison (Radix flow vs POC flow)

### Before (Radix in this flow)

- Needed overflow handling on modal content and extra container plumbing for select overlays.
- Multiple overlay layers (dialog + popover + select portal behavior) increased fragility.
- More moving parts to debug scroll + positioning regressions.

### After (POC)

- Single modal layer with explicit scroll area in the form body.
- No select portal container handoff in this flow.
- Suggestion list anchored in-flow; fewer cross-layer interactions.
- Simpler failure modes and lower code-path coupling.

### Tradeoffs observed

- POC currently favors simplicity over feature richness in the suggestion panel behavior.
- A full migration would still require careful a11y QA for each replaced interactive primitive.

## Migration Estimate

### Estimated effort for full Radix exit

- **Phase 1 (high-risk overlays first):** `dialog/sheet/popover/select/command/toast`
  - ~2-3 weeks focused work
  - Highest regression risk, requires deep manual QA
- **Phase 2 (medium controls):** tabs/radio/switch/slider/toggle/collapsible/accordion
  - ~1-2 weeks
- **Phase 3 (low primitives and cleanup):** label/separator/avatar/progress/etc.
  - ~3-5 days
- **Total estimate:** ~4-6 weeks depending on parallelization and QA depth.

### Main migration hotspots

- Any feature with nested overlays and scroll lock behavior
- Form-heavy dialogs and sheets
- Menu/navigation patterns currently relying on Radix focus and keyboard behavior

## Decision (Wave 1)

**Accepted:** ship a **mobile-first overlay pattern** using **Headless UI `Dialog`** for the highest-friction flows, keep **Radix** for the rest of the design system (`fe/src/components/ui/*`), and migrate additional screens **incrementally** when touching overlay code.

Rationale:

1. Full Radix removal remains high cost; selective Headless UI solves scroll/portal pain where it mattered.
2. Shared primitives (`select`, `popover`, `sheet`, `command`) gain explicit contracts so feature code avoids ad-hoc portal ref threading.
3. Cursor rule [`04-overlays-mobile.mdc`](../cursor/rules/04-overlays-mobile.mdc) encodes layout and QA expectations.

### Implemented in Wave 1 (frontend)

- `fe/src/components/CreateBudgetLineDialog.tsx` — Headless dialog, internal scroll, suggestion keyboard support.
- `fe/src/components/EditBudgetLinePricingSheet.tsx` — Headless dialog (replaces sheet stack); yield select uses default body portal.
- `fe/src/components/CreateItemYieldDialog.tsx`, `fe/src/components/CreateProjectDialog.tsx` — Headless dialog + same scroll/footer pattern.
- `fe/src/components/ui/popover.tsx` — optional `container` on portal.
- `fe/src/components/ui/sheet.tsx` — `flex` / `max-h-[100dvh]` / `min-h-0` baseline for side sheets.
- `fe/src/components/ui/command.tsx` — list max-height uses dynamic viewport-friendly cap.

Longer-term full Radix exit remains optional and should be revisited only if multiple remaining flows show the same class of failures.
