# Project Status & Manifesto: SITE INDEX

_(Construction intelligence SaaS — Argentina-first; canonical app at https://app.siteindex.com; studio slug tenancy.)_

## 1. The Vision & "The Spirit" (For Clients & Stakeholders)

Construction management software is broken. It usually falls into two traps: it’s either a glorified spreadsheet that requires endless manual data entry, or it's a rigid ERP that doesn't survive the messy reality of a construction site. Ultimately, users get "data fatigue" and abandon it.

**We are building an "Active Assistant," not a passive database.**
Our app flips the traditional model. Instead of waiting for the user to tell it what happened, the app _assumes_ what happened based on the schedule and the budget, keeps the project moving, and simply asks the user to validate its assumptions asynchronously.

We protect profit margins by focusing fiercely on the **Pareto Principle (10/90)**: 10% of the items drive 90% of the cost. The app actively tracks the "Big Fish" (like structural concrete or steel) and doesn't paralyze the user over a box of screws. this is key

## 2. Core Product Philosophies (The "Rules of the App")

- **Progressive Obsession:** The app must accommodate both the "Quick & Dirty" contractor and the "Obsessive" Architect. **Defaults favor Quick & Dirty** (simple, global inputs); the Obsessive path is always available by drilling into granular detail (dosages, waste percentages, labor hours, custom items).
- **Art vs. Science (Truth Transparency):** Budgets based on old, scraped, or unverified data are "Art." Budgets based on confirmed invoices and reality are "Science." _Flaky data will visually show itself as flaky._ Whether it's a total cost, a material quantity, or a price—if it's an unconfirmed assumption, it will look uncertain and provide a direct link to resolve it. i think that data is not either flaky or perfect (binary), but rather qualificable on a scale from more to less robust, based on whether it is, for example, based om public indexes rather than real quotations, old, out-of-the-box or customized, etc. A piece of data can de qualified on a scale based on its reliability.
- **The User as Navigator, Not Clerk:** The app does the data entry through automated logic. The user acts as the auditor who approves or corrects the app's decisions. Yes! data entry, customization of items, loading of payments or invoices, should be **very** friendly (less typing, more toggling).

### Locked product & platform decisions

- **Name:** SITE INDEX.
- **Primary market:** Argentina. **Multi-currency** is required, including USD with **distinct treatment of informal vs. official contexts** (_USD negro / USD blanco_, also spoken of as _blue / oficial_) — product and reporting must allow both where the business needs them.
- **Tenancy:** Canonical app URL **https://app.siteindex.com**. Studios are identified by **slug** (not per-studio subdomains), e.g. `X-Studio-Slug` on API calls — one app hostname for DNS/TLS and simpler client onboarding.
- **Roles:** Role-based access will exist; specific roles and permissions are **TBD**. keep it simple (project manager and member)
- **Assumptions & conflicts:** Concrete rules (e.g. schedule vs. manual assignment) are refined during implementation. **Guiding principle:** anything the system _assumes_ is always **surfaced in the Assumptions tab** for asynchronous human review—not silently overwritten without a trail. The **Assumptions tab** is a **primary, high-volume flow** (users spend a lot of time here); the backlog is **sorted by a user-entered mix** of **money**, **time** and **impact**.
- **Dosificaciones (v1):** User-entered only (no imported standards catalog in Phase 1). why not a base library of dosified items? all out-of-the-box items should already be dosified.
- **Notifications:** In-app only for now.
- **Unconfirmed assumptions:** They **do not expire**; they remain visible and truth-transparent until the user acts.
- **Facturas & remitos:** **Full OCR** ingestion (extract structured fields from scans/PDFs). yes. also a very user friendly expense logging interface (date, amount currency and item number).
- **Client shape:** **Browser-based PWA**, **offline-capable from day one** (local-first / sync when online — “PWA max” as the ceiling for the web app).

---

## 3. The Technical Engine (For Developers)

### A. The 3-Pillar APU (Análisis de Precios Unitarios)

Every item in a Cómputo (whether imported from Revit or entered manually) is built on three pillars: **Materiales, Mano de Obra (Labor), and Equipo (Equipment).**

- **Parametric Conversions:** Items are deep-linked to _Dosificaciones_ (mix designs). The UI lets the user input $100m^2$ of slab. The backend calculates the depth ($m^3$), computes the exact $kg$ of cement needed, and translates that into purchasing units (e.g., 50kg bags). yes. this requires an item by item analysys and to group them by the type and number of options the item has at the moment of adding an item instance (for example, concrete slab has a primary unit, like m2, but the user can also specify slab height and type of reinforcement, allowing the app to calculate other item units, like m3, and therefore computing resources not based on the primary unit). other type of items have a very different set of options, like paint (only m2 matters, but quantity of hands is a relevant factor). for phase 1, building the item library is very time consuming. evaluate AI tools to help on this task.
- **Flexible Subcontracting:** A Subcontractor isn't just "Labor." They can assume responsibility for 1, 2, or all 3 pillars of an item. If a contractor provides labor and tools, but the user provides materials, the app tracks only the material deviations for the user, and treats the rest as a fixed unitary cost.
- **Labor Tracking (Jornales vs. Contracts):** Contractors are paid proportionally to output (Certificación). Direct labor (Jornales) is time-based. The app back-calculates daily wage costs against actual progress to flag if the _real_ unit price is exceeding the _budgeted_ unit price.

### B. The Context Layer (The Touch-Gantt & Imputation Logic)

The schedule isn't just for visualization; it is the **Logic Engine**.

- Users build a schedule via a simple finger-drag Gantt interface (with sub-tasks inheriting dates from parents).
- **The "When" drives the "Where":** Because the app knows _what_ is supposed to be happening today, it uses this context to automatically impute costs. If a delivery of cement arrives on Tuesday, and Tuesday is scheduled for "Foundation," the app automatically assigns that cost to the Foundation item. nice but not critical.

### C. The Assumptions Tab (The Heart of the App)

This is the central interaction model of the software—expect it to be a **huge flow**: much of the PM/architect’s week is clearing, editing, and confirming what the system assumed. **The app never stops the user with annoying pop-ups.** Instead, it makes intelligent assumptions to keep the project moving and logs them in a prioritized "Decision Backlog." we need to fucus on how this works.

- **User-entered sort mix:** The backlog is **not** fixed to “largest dollar first.” The user **enters** (or adjusts) a **mix** across **money** (financial exposure), **time** (urgency / schedule sensitivity), and **impact** (how wide the blast radius is if the assumption is wrong). Sliders, presets, or similar let different people—or the same person on different days—emphasize margin vs. deadlines vs. cleaning up flaky numbers.

- **Asynchronous Auditing:** The user (Project Manager/Architect) opens the app and sees a list of "Trillion Decisions" the app has already made:
  - _Imputation:_ "I assigned $5,000 of steel to the Slab. **[Confirm / Edit]**"
  - _Waste Detection:_ "You finished 100% of the wall, but used 8% more bricks than the dosage dictates. Update future waste margins? **[Yes / No]**"
  - _Price Alerts:_ "The concrete price is 6 months old. Update with today's market average? **[Update / Ignore]**"
- **Pareto as a default bias, not a cage:** Out of the box, a **money-heavy** sort approximates 10/90 (e.g. a $10,000 unconfirmed concrete delivery rises above a $15 box of nails), but the user can always rebalance the mix so schedule-critical or quality-critical cards float up instead.

### D. Dynamic Deviations & Machine Learning "Lite"

- **Automated Consumption:** As progress is certified (e.g., "Slab is 50% done"), the app automatically "spends" the theoretical materials (via _Dosificaciones_).
- **Reality Check:** When actual invoices (_Facturas_) or deliveries (_Remitos_) exceed the theoretical limit + accepted waste, the app flags a **Deviation**. if i log an invoice, how does the app know if it is a new spenditure that adds over what the app assumes i have alredy spended (given the compeltion percentage of the task) or it is a spenditure included in the prior assumption?
- **Self-Correcting Data:** If a user consistently wastes 5% more material or takes 10% more labor hours (_Jornales_) than planned, the app proposes updating the core database so the _next_ budget is based on reality ("Science"), not theory ("Art"). nice feature but not critical.

### E. Price Quality & History

- Prices have a "Quality Grade." yes!
- If an item uses a scraped price from a year ago, it is heavily flagged as "Flaky/Art." it is graded within a reliability scale.
- If an item uses a price from a confirmed invoice processed yesterday, it is trusted as "Science." the app should be "gamified" to encourage users to improve the projects overall reliabilty garde (more green less red)

---

## 4. Visual Language & UX Principles (For Design/Dev)

- **Truth Transparency:** Flaky data must visually identify itself. If a total budget or progress percentage is heavily reliant on _Unconfirmed Assumptions_, the UI must reflect that uncertainty (e.g., dimmed numbers, warning icons, explicit links to the Assumptions tab). The user must never trust a number more than they should.
- **Graceful Degradation:** A user who skips the Gantt, skips the Revit import, and just manually types "Global Item: Wall = $1,000" must still get value from the app. The system must not break if data is sparse; it simply tracks at a higher level and distributes costs proportionally. yes! cost incidence % shoukd also be part of the app´s out of the box library, and gradually replaced by the projects real cost incidence

---

## 5. Development Phases

**Phase 1: The Core "Art" Engine (Budget & Certification)**

- Cómputos structure (Manual input initially).
- 3-Pillar APU setup with simple _Dosificaciones_ (user-entered) and unit conversions (e.g., $m^2$ to bags of cement).
- Global progress certification.
- Basic assumption generation (flagging when spending > budget).
- **PWA foundation:** installable web app, offline read/write with sync (scaffold early even if features are thin).
- i would include a basic library of preset items (with standard dosifications)

**Phase 2: The "Science" Engine (Context & Assumptions)**

- Revit integration (Optional mapping to APU).
- The Touch-Gantt schedule.
- **Factura & remito pipeline with full OCR** (plus human correction where the model is uncertain — surfaced as assumptions where needed).
- **The Assumptions Tab** (Building the imputation engine, ranking logic, and UI for the confirmation queue).

**Phase 3: The Financial Layer**

- Subcontractor vs. Jornales efficiency tracking (Back-calculating labor costs).
- Price scraping, history indexing, and Data Quality grading.
- Cash Flow generation (mapping confirmed facts + unconfirmed assumptions to a timeline).

---

## 6. Known Risks & Mitigations (For the Dev Team)

- **Risk:** The "Assumptions" queue gets too massive, and the user declares bankruptcy on clearing it.
  - _Mitigation:_ Ruthless Pareto (10/90) filtering. Let them easily "Bulk Confirm" the bottom 90% of low-impact items so they only focus their cognitive load on the top 10% that actually dictate the profit margin.
- **Risk:** Complex Responsibility Splits (e.g., The contractor provides labor and tools, but the user provides bricks and the contractor buys the mortar).
  - _Mitigation:_ The APU architecture must have clear "Ownership Toggles" for every single component. If the user doesn't "own" a specific material or labor line, the app ignores internal waste tracking for that item and only tracks the global agreed-upon price. in those cases the simplest solution (not sure if the best) is to split the item into two different items (e.g., if plumbing works are subcontracted but the user provides materials, then there should be an item called 'full plumbing works', measured in units and only have a value for 'mano de obra', and other items like 'rainwater drainage materials', measured in units and only having a value for 'materiales').
- **Risk:** Unplanned Reality / Change Orders (e.g., An invoice arrives for a material that literally does not exist in the original _cómputos_).
  - _Mitigation:_ Never block data entry. The app must dynamically create an "Unplanned / Extras" bucket, dump the expense there, and immediately fire a high-priority card into the Assumptions Tab: _"I found an unknown cost. Is this a Change Order to be billed to the client, or a mistake?"_ Nice!
  - **Risk:** informal spenditures (e.g., an assitant drives around all day spending money from a cash reserve he holds and eventually returns, no invoices no receipts).
    - _Mitigation:_ very simple mobile loging interface.

---

## 7. Summary: What are we actually building?

We are building a system that bridges the gap between the **"Art"** of a preliminary Excel estimate and the **"Science"** of a finished, profitable building.

For the **Obsessive Architect**, it is a precision instrument that tracks exact dosages, waste percentages, and labor yields.
For the **Busy Contractor**, it is an automated assistant that looks at the schedule, guesses where the money went today, and simply asks them to tap "Confirm" while they drink their morning coffee.

By treating "Flaky Data" as a first-class citizen (visually highlighting unconfirmed assumptions) and focusing entirely on the 10% of items that matter, **SITE INDEX** helps studios and their clients avoid margin bleed from spreadsheets that never get updated.

---
