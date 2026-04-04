# Budget and Construction Phases

This document defines the operating rule for project lifecycle phases in SITE INDEX.

## 1) Phase model

Projects operate in two phases:

1. **Budget mode**
2. **Construction mode**

## 2) Budget mode (baseline authoring)

In budget mode, budget lines are editable and represent the planned/contractual baseline.

Typical edits in this phase:

- quantity and drivers,
- unit price and category breakdown,
- composition decisions needed to close the baseline.

## 3) Transition to construction mode

There is a transition event where the project moves from budget mode to construction mode.

From that point, the baseline budget lines are treated as locked reference values.

## 4) Construction mode (frozen baseline + deltas)

In construction mode:

- baseline budget line values are frozen,
- changes are not modeled as baseline rewrites,
- deviations are recorded as **deltas** against the frozen baseline.

Supported delta families:

- quantity deltas (for example `economias` and `demasias`),
- price variation deltas.

## 5) Refactor assessment

Do we need a major refactor now to support this?

**No.**

The current architecture can support this direction with additive evolution when implementation starts:

- add explicit project phase state,
- add immutable baseline capture (fields or snapshot table),
- add explicit delta records for quantity and price variation.

This can be delivered incrementally on top of current budget-line and yield foundations, without a major rewrite now.
