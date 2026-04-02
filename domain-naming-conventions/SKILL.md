---
name: domain-naming-conventions
description: >-
  Standardizes naming for domain entities, APIs, and persistence boundaries using
  ubiquitous language (DDD) and consistent cross-layer patterns. Use when
  defining models, designing APIs, mapping database fields to application types,
  architecting systems, refactoring toward domain vocabulary, or when the user
  mentions entity names, DDD, ubiquitous language, or domain-driven naming.
---

# Domain and entity naming conventions

Naming is not merely a technical detail; it is the bridge between business logic and executable code. Syntax must be consistent for machines; names must be meaningful for humans to reduce cognitive load and prevent “lost in translation” bugs.

For storage-specific rules (SQL `snake_case`, JOIN keys, Mongo collections and `_id`), apply the database skills in this repository and keep this skill focused on **domain vocabulary** and **layer boundaries**.

---

## 1. The Golden Rule: Ubiquitous Language

**The best name is the one your domain experts use.**

In Domain-Driven Design (DDD), aim for a vocabulary shared by developers, product owners, and stakeholders. If the business calls a user’s subscription a **Membership**, do not name the type `SubscriptionPlan` unless that is the agreed term.

> **Litmus test:** If you read the code aloud to a non-technical stakeholder, would they follow the business process? If you need a mental translation table, the name is wrong.

---

## 2. Field mapping: DB vs app vs API

Keep a clear boundary between how data is **stored**, how it is **manipulated in domain/application code**, and how it is **exposed in contracts** (REST, GraphQL, events).

| Category | Database field (persistence) | App/model field (logic) | Public API / contract | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Primary key** | e.g. `_id` (Mongo), `order_id` (SQL) | `[entity]Id` | `[entity]Id` | Map opaque storage keys to domain-specific id names at your API boundary. SQL: prefer explicit keys per `database/postgresql-naming-conventions`. |
| **Reference** | `user_id` / `userId` | `userId` + `user` when hydrated | `userId`; embed `user` only when the contract defines a sub-resource | Store references as `*Id`; reserve the entity name for populated or embedded objects. |
| **Status** | `status` / `order_status` | `[entity]Status` | Same as app when exposed | Be explicit when multiple status concepts share a context (e.g. `shipmentStatus` vs `paymentStatus`). |
| **State** | `is_active` / `isActive` | `isActive` | `isActive` | Booleans: `is`, `has`, `can`, `should` (or equivalent clear question form). |
| **Timestamps** | `created_at` / `createdAt` | `createdAt` | `createdAt` | Use one convention per layer; be consistent about UTC. |

Serialization layers should translate storage shape to contract shape so clients never depend on accidental DB field names.

---

## 3. Structural rules

### Collections, tables, and aggregates

- **Plural resource names** at the persistence level where that matches your store: e.g. `products`, `orders`, `invoices`.
- **Avoid redundant suffixes** such as `_table` or `_collection`.
- **Avoid stuttering** the parent context into every child name when the container already defines it.
  - **Bad:** `orders.order_date`
  - **Good:** `orders.placed_at` / `orders.placedAt` (or another domain-specific timestamp name)
- Prefer **meaningful event or process names** (`placedAt`, `shippedAt`) over a bare `date`, which is ambiguous in code search and reviews.

### Nested objects and DTOs

Name embedded objects or DTOs after the **concept** they represent, not their structural role alone.

```json
{
  "customerName": "Alice Smith",
  "billingAddress": {
    "street": "123 Main St",
    "city": "New York"
  }
}
```

---

## Layer-specific skills

| Concern | Skill |
| :--- | :--- |
| PostgreSQL tables, columns, JOIN keys, SQL style | `database/postgresql-naming-conventions` |
| MongoDB collections, documents, `_id`, camelCase fields | `database/mongodb-naming-conventions` |

---

## Agent checklist

1. Prefer **stakeholder vocabulary** for type and property names; rename only when modeling a different concept, not for developer convenience alone.
2. At API boundaries, use **domain-specific ids** and stable names; hide storage-only keys unless they are part of the contract.
3. Use **`*Id` for stored references**; use the **entity name** when exposing or embedding the related object per contract rules.
4. Name booleans and statuses so they **read clearly** and **search well** in the codebase (`isShipped` vs `status` alone in a crowded module).
5. Defer **SQL vs document field casing and key rules** to the database naming skills while keeping **meaning** aligned with this document.
