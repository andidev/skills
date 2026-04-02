---
name: general-naming-conventions
description: >-
  Enforces one conceptual name for every identifier across front-end, back-end,
  and database: same tokens and meaning everywhere, varying only by required
  casing per context (camelCase, kebab-case, snake_case, PascalCase, etc.) and
  purposeful affixes (e.g. Vm, Dto).
  Improves searchability (grep, IDE search, code review) by keeping one
  vocabulary across the stack. Treat as high-priority and strict. Use for any
  naming, refactors, new APIs, models, components, routes, columns, or when the
  user asks for consistent naming, ubiquitous identifiers, cross-stack
  alignment, or searchable code.
---

# General naming conventions (cross-stack)

**Priority:** High. **Strictness:** Treat inconsistent names as defects unless an approved exception exists.

**Goal:** Maximize **searchability**—a single search (grep, IDE symbol search, scoped find) should surface every layer that touches the same concept, without hunting alternate terms.

One **concept** must yield one **name stem** in the codebase. Developers should grep one token and find the same idea in UI, services, SQL, and events—not a parallel vocabulary (`user` in React, `customer` in SQL, `Account` in the API).

---

## 1. The rule: same name everywhere

For every field, type, resource, flag, and action:

1. Choose a single **canonical sequence of name parts** (words / abbreviations agreed for that concept).
2. Reuse that sequence **in every layer**, changing only what each layer requires:
   - **Casing and separators:** e.g. `orderStatus` (TS/JS), `order_status` (PostgreSQL), `order-status` (kebab URL segments when applicable).
   - **Lawful structural variants from storage or routing skills:** e.g. SQL `product_id` and app `productId` share the same parts `product` + `id`; do not rename the concept per layer.

The **meaning** of each part is fixed across the stack. Serialization, ORMs, and mappers translate **shape**, not **vocabulary**. That stability is what makes cross-layer search reliable.

---

## 2. Allowed exceptions

### 2.1 Layer-mandated casing and format

**Always** apply the **idiomatic or documented casing** for where the code lives: language defaults, framework conventions, library APIs, linter/style-guide rules, and team standards. Typical patterns (adapt to the stack in use):

| Context | Common convention | Examples |
| :--- | :--- | :--- |
| JS/TS variables, functions, object keys (app code) | camelCase | `orderStatus`, `fetchUserById` |
| JS/TS classes, components, types, enums | PascalCase | `OrderCard`, `UserService` |
| HTTP paths, CSS custom properties, many static assets, some config | kebab-case | `/api/order-status`, `--order-status` |
| Source file names | Project / framework convention | e.g. `OrderCard.tsx` (common for React components) or kebab-case file names where the repo standard requires it |
| Python modules/functions (PEP 8), Ruby, SQL identifiers per project | snake_case | `order_status`, `fetch_user_by_id` |
| Constants (many languages) | SCREAMING_SNAKE | `MAX_RETRIES` |

- Prefer the **official style** of the runtime or library (e.g. framework docs, ORM column mapping conventions) over personal preference.
- URLs, file names, and config keys may use kebab-case or other **required** formats; the **word order and words themselves** still match the canonical name.

### 2.2 Purposeful prefixes and suffixes

Affixes are allowed **only** when they encode a real distinction the type system or API cannot express alone:

- **Examples:** `Product` (domain) vs `ProductVm` (view-specific shape), `CreateOrderRequest` vs `Order`, `UserEntity` vs `UserDto`—only if `Vm`, `Request`, `Dto`, etc. carry documented meaning.
- **Requirement:** The **root** must still match (`Product`, `Order`, `User`). Do not use affixes to smuggle alternate vocabulary (`CustomerVm` for a `User` concept).

If an affix does not add information (redundant `Data`, `Info`, `Object` on every type), remove it.

---

## 3. Forbidden patterns

| Pattern | Why |
| :--- | :--- |
| Different synonyms per layer (`client` / `customer` / `account` for one entity) | Breaks search, reviews, and mental model. |
| Different abbreviations per layer (`qty` vs `quantity`, `usr` vs `user`) | Same rule as synonyms. |
| Singular/plural drift for the **same** property concept (`itemsCount` vs `itemCount`) | Pick one stem and pluralization convention per concept. |
| “Convenience” renames at boundaries without a new concept | Boundaries need mapping of **shape**, not **dictionary**. |
| Hungarian or role markers that replace the root (`strName` vs `name`) | Casing and types supply type information. |

When a **genuine second concept** exists (e.g. **Account** vs **User** as different bounded contexts), document it in domain terms—do not treat it as a casual alias.

---

## 4. Alignment with other skills in this repository

| Skill | Role |
| :--- | :--- |
| `domain-naming-conventions` | Chooses **what** the business calls things (ubiquitous language). This skill enforces **one** of those choices everywhere. |
| `database/postgresql-naming-conventions` | SQL casing, `entity_id`, column explicitness. Apply those rules **on top of** the same logical parts (e.g. `product_id` ↔ `productId`). |
| `database/mongodb-naming-conventions` | Document and field casing. Same logical names; storage format per that skill. |

If two skills appear to conflict, **preserve one conceptual vocabulary** and satisfy each layer’s formatting rules without renaming the concept.

---

## 5. Examples

**Good — one concept, shape varies**

- Concept: placement time for an order.
  - DB: `placed_at`
  - API / app: `placedAt`
  - UI variable: `placedAt`

**Bad — synonym drift**

- DB: `client_id`, REST: `accountId`, TypeScript: `customerId` for the same foreign key.

**Good — purposeful suffix**

- Types: `Product`, `ProductVm`; API returns `Product`, client view model is `ProductVm` with the same property names where semantics align.

**Bad — suffix hides different vocabulary**

- `ShipmentDto` built from `Order` rows without a domain distinction named Shipment.

---

## 6. Agent checklist

1. When adding or renaming anything, define the **canonical part sequence** once and mirror it across UI, server, DB, and contracts—using **context-appropriate casing** (camelCase, kebab-case, snake_case, PascalCase, etc.) per language, framework, and lib—not inventing mixed styles in the same layer.
2. Reject or flag **synonym substitutions** across layers unless the user explicitly introduces a **new** domain concept.
3. Allow **affixes** only when they encode a real, stable role (`Dto`, `Request`, `Vm`, etc.) and keep the **root** identical.
4. After mapping layers, **grep** or search the root token: results should read as one story, not three dialects—this is the practical check for searchability.
5. Defer **SQL/Mongo formatting and key rules** to the database skills without renaming the underlying concept.
