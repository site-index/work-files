# Catalog, yields, and pricing model

Canonical product definitions for **rubros**, **ítems** (cómputo / tasks), **rendimientos** (materials + labor + equipment per item unit), and **precios** (per resource unit). This document nails the domain; implementation can follow.

---

## 1. Three libraries (three different things). There are 3 liraries, ok, but with a difference: i think rendimientos is not a library, but rather a parameter of each item (not sure). Insumos (library of preset materials, labor units and equipment units), on the other hand, is a library which is highly customizable. A user can create a new material or a variation of an existing material but with a different packaging format (for example, the ootb libary has a material called 'clavos de 1-1/2' which are measured by kg, and a user duplicates this material but changes the unit to 100un box). to maintain logic, when a resource is assigned to a new item (or an item that is being customized -which is in essence the same thing-), the app only allows to define the rendimiento of that resource in a unit compatible with the resorce's packaging format (for example, if the user select the clavos by kg, then it must specify the kgs that are required per item's measurement unit, but if it selects the clavos by un, the user must then specify the number un unitary clavos that are necessary per  item's measurement unit. of couse, in the items that are pre-confidured ootb, everything is prevously configured.

| Library            | Question it answers                                                                        | Typical content                                                                                                                          |
| ------------------ | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Rubros / ítems** | _What task is this line of work, and in which unit do we measure it on site?_              | Semi-Fixed **rubros**; **ítems** under each rubro (name, scope, **item unit**). A new item is handled by the product team, not the user. |
| **Rendimientos**   | _Per one unit of that item, how much material, labor (MO), and equipment does it consume?_ | Quantities per **item unit**, each line in the **resource’s own UoM** (kg, bag, h, machine-day, …).                                      |
| **Precios**        | _What does one unit of each resource cost?_                                                | Money per **resource UoM** (e.g. 25 kg cement bag, m³ sand, hourly wage).                                                                |

All three are **seeded by the app** (OOTB) and **highly customizable** by the user/studio.

---

## 2. Rubros and ítems

- **Rubros** are **fixed**. Users do **not** create new rubros. If a task does not clearly fit, park it under something like **“Otros”** (or equivalent). A new rubro is handled by the product team, not the user.
- **Ítems** belong to exactly one rubro. New site reality that is not covered by an existing line → **create a new ítem** under an existing rubro (or under “Otros” if unsure).
- The **ítem library** is the richest OOTB layer: it should cover a **typical** obra; gaps are filled by **new ítems** and by **tweaked rendimientos** (see §5).

**Example rubros (illustrative):**

1. Tareas preliminares
2. Demoliciones
3. Movimiento de suelos
4. Fundaciones
5. …

each item has a longer paragraph describing the scope of the item. This are the legal conditions of the item, for example.: "cielorraso de placa de yeso de 9 mm con estructura de perfilería tradicional con buña perimetral. incluye armado, emplacado en encintado y tomado de juntas". The Name of The item is a one sentence summary of the longer scope. It would be desirable to have an AI suggestion for the items name given the description introduced by the user or picked from the item Library, and later modified. The list of the items scope description will later conform the project's briefing, but it is also a valuable resource to establish how the item is measured, and the respective dossages (rendimientos).
The library of items included in the presets of the app covers at least 90% of a standard construction project in Argentina. The user starts with a blank project. That is a project that has no items and just has all the empty rubros. The user then adds each item by picking it from the library as it is, or by picking an item that already exists and adjusting it, or by creating an item from scratch. It would be desirable that the user starts by writing the item's scope description (and the name of the item is completed autimatically) because that helps determining the measurement units the justification, the materials, labor, and equipment it requires (this is a desirable workflow, not critical to the logic of the app).
It would be nice to have project templates (p.eg., "vivienda de construcción tradicional y techo de chapa en dos plantas", "galpón industrial de estructura reticulada y platea de hormigon llaneado"), where the most standard items are already created, saving time in project setup (the cost incidence % of each item is also preset). In that case, the user starts by introducing the quantities for each item, and then has to customize to finetune the remaining % of the project.
In any case, when an item is completed (mainly by setting its quantity in the project), and given that the app knows the cost incidence % ("weight") of each item, the entire budget is completed (in pencil) and the user has a preliminary final cost for the project.

**Example ítems under “Fundaciones”:**

- 4.1 Zapata corrida de ladrillo común con azotado hidrófugo
- 4.2 Pilotines de hormigón armado
- 4.3 Bases aisladas de hormigón armado
- 4.4 Viga de fundación de hormigón armado

there are special types of items (like system families) where the user has very specific customization tools. for example, in the items related to concrete, it would be nice to have dossification tools and also tools specially designed to compute rebar and reinforcement. other special items are those relating to different types of blocks for walls or different types of tile finishing.


---

## 3. Rendimientos (per ítem)

- A **rendimiento** is **data of the ítem**: for **1 unit** of the ítem (1 m², 1 m³, 1 un., …), specify required **materials**, **mano de obra**, and **equipo**.
- OOTB ítems should ship with a sensible **materials** rendimiento at minimum; users will still override often.
- **Why heavy customization:** (1) not every ítem can be pre-listed; (2) the same ítem can have **very different** yields in real projects.

**Important:** Resource lines use **their own units**, which are **not** the ítem’s **unit of measure on site** (e.g. ítem in m² of slab; cement in bags of 25 kg).

---

## 4. Precios (per resource, not “the ítem’s magic number”)

- Prices apply to **materials, MO, and equipment** in **their purchasing or cost units**.
- Example: a **25 kg bag of cement** has a price; the app **may** suggest it (e.g. from the web) but it is **not critical**—the user can enter it.
- The **unit rate for the ítem** in the budget is **derived**: roughly, sum over rendimiento lines of _(quantity per item unit × price per resource unit)_, plus whatever the product adds (waste, margins, subcontract toggles, etc.).

---

## 5. Workflow: similar ítem vs new ítem

When the catalog line is **not exactly** what the user needs:

1. **Small gap:** Start from a **similar ítem** and **adjust the rendimiento** (e.g. more cement, remove aggregate, swap “piedra partida” for “piedra binder”).
2. **Large gap:** Create a **new ítem** (e.g. “muro ladrillo hueco 12 cm” exists but “18 cm” does not → new ítem under the right rubro).

**Crowdsourced growth (product intent):** a **fully custom ítem** created by a user **should be able to feed** a **general / public task library** so the catalog grows **without central-only effort** (e.g. a large contractor publishes a definition others can reuse). Exact rules (moderation, fork vs reference, region, versioning) are **TBD**.

---

## 6. What is shared across studios?

| Layer                                                 | Typical sharing model                                                                                                                                          |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Rubros + core ítem catalog + default rendimientos** | **Product-wide** (everyone gets the same OOTB baseline; it evolves with releases).                                                                             |
| **Studio (or project) overrides**                     | **Private** to that scope: my yields and my prices for my job, even if the ítem name matches OOTB.                                                             |
| **“General / public library”**                        | **Opt-in or published** contributions (custom ítems + their rendimientos) that enrich the **global** catalog—not “every draft visible to everyone” by default. |

So: **ítems and rendimientos are not “only dosificaciones shared.”** The mental model is: **shared baseline + private customization + optional publication** of ítems (with yields) into a broader library. **Precios** are usually **studio/project** (and time-sensitive); sharing them globally is **not** implied.

---

## 6.1. Catalog stability and production changes

The **Prisma seed** assigns **stable-looking `id`s from array position** for global **`WorkCategory`** rows (and similarly for **`MeasureUnit`** and catalog **`Item`**). **Reordering, deleting, or inserting in the middle** of those seed arrays can **change existing `id`s** and **break foreign keys** (or orphan rows). Rubro / ítem **`code`** values derived from Spanish **names** can also change if the label changes, which is equally dangerous once data points at those rows.

**Implementation (DB):** **`Item`** holds product-wide ítem names + rubro. **`ItemStudio`** stores the studio’s default **`components`** (rendimiento JSON) per ítem. **`ItemYield`** is a **per-project snapshot** copied when the project is created (and lazy-filled for legacy projects); editing **`ItemStudio` does not mutate existing `ItemYield` rows**. If the studio default changes after a snapshot, the app may open an **`Assumption`** of type **`STUDIO_YIELD_STALE`** (Spanish copy in payload).

**Rules:**

- Treat seed lists as **append-only** after a database has been initialized, unless you ship a **data migration** that updates dependent rows.
- For **new** global rubros or measure units in deployed environments, prefer the operator API: **`POST /v1/work-categories`** and **`POST /v1/measure-units`** with header **`X-Catalog-Write-Secret`** matching server env **`CATALOG_WRITE_SECRET`** (no studio JWT on those routes). Use a **new, unique English `code`** and never recycle a code for a different meaning.

See also the **STABILITY CONTRACT** banner in `be/prisma/seed.ts` and the long descriptions on those POST operations in Swagger (`/docs`).

---

## 7. Relationship diagram

```mermaid
flowchart TB
  subgraph rubros_items["Library 1: Rubros / ítems"]
    R[Rubro — fixed set]
    I[Ítem — belongs to one rubro]
    R --> I
    IU[Item unit — m2, m3, un., ml, ...]
    I --- IU
  end

  subgraph rendimientos["Library 2: Rendimientos (per ítem)"]
    Y[Rendimiento lines — per 1 item unit]
    MAT[Materials]
    MO[Mano de obra]
    EQ[Equipo]
    Y --> MAT
    Y --> MO
    Y --> EQ
  end

  subgraph precios["Library 3: Precios"]
    P[Price per resource UoM]
  end

  subgraph resources["Resources (conceptual)"]
    RES[Material / MO type / Equipment type + its UoM]
  end

  I --> Y
  MAT --> RES
  MO --> RES
  EQ --> RES
  RES --> P

  DER[Derived item unit rate in budget]
  Y --> DER
  P --> DER
```

**Reading order:** pick an **ítem** → its **rendimiento** lists **resources** and quantities per ítem unit → **precios** attach to each resource in **its** unit → the **budget line rate** for the ítem is **derived**.

---

## 8. One-line summary

**Rubro** = fixed shelf. **Ítem** = certifiable task line with an **item unit** (unit of measure on site). **Rendimiento** = M + MO + Eq **per ítem unit**. **Precio** = cost **per resource unit**. **Sharing** = OOTB for all + private overrides + optional feed into a **general ítem library**.

---

## 9. Open product decisions (not blocking this doc)

- Fork vs reference when reusing a published ítem.
- Moderation and attribution for the public/general library.
- Whether rendimiento defaults are **per studio**, **per project**, or both. neither: per item
- How “Otros” is structured (single rubro vs policy).
