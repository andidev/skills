---
name: postgresql-naming-conventions
description: >-
  Applies PostgreSQL/SQL naming: snake_case columns, explicit names for JOIN
  clarity (entity_id), UUID v7 primary/foreign keys, booleans (is_/has_/…), and
  anti-stuttering rules in relational schemas. Use when designing schemas,
  migrations, SQL, ORM models, or when the user mentions PostgreSQL naming,
  keys, or SQL style. Pair with general-naming-conventions and
  domain-naming-conventions for vocabulary and persistence↔app↔API mapping.
---

# PostgreSQL naming conventions

Use **explicit** SQL names so joins, `SELECT *`, and multi-table queries stay unambiguous.

## Read first

| Skill | Role |
| :--- | :--- |
| [general-naming-conventions](../general-naming-conventions/SKILL.md) | One vocabulary across layers; **searchability** (grep-friendly names); SQL columns use **snake_case** while app/API use the same *words* with layer casing. |
| [domain-naming-conventions](../domain-naming-conventions/SKILL.md) | Ubiquitous language; **DB vs app vs API** mapping (e.g. `order_id` ↔ `orderId`). |

Do not re-teach cross-stack naming or “why explicit names” in full here—those skills define it. This skill is **relational schema and SQL shape**.

## Naming standards (SQL layer)

| Column category | Generic (avoid) | Explicit (use) | Reasoning |
| :--- | :--- | :--- | :--- |
| **Identity** | `id` | `product_id` | Reduces ambiguity in `JOIN` result columns. |
| **Classification** | `type` | `product_type` | Avoids noise and keyword clashes in search. |
| **Lifecycle** | `status` | `order_status` | Separates multiple status concepts in one query. |
| **State (boolean)** | `active` | `is_active` | Reads as a yes/no question; aligns with [general-naming-conventions](../general-naming-conventions/SKILL.md) / [domain-naming-conventions](../domain-naming-conventions/SKILL.md). |
| **Time** | `date` | `created_at` | Distinguishes instants (`_at`) from calendar dates (`_date` where needed). |
| **Math / aggregates** | `total` | `total_amount` | States what is being summed or measured. |

## PostgreSQL-specific practices

### JOIN-friendly keys

Prefer `table_entity_id` style primary and foreign keys so names stay distinct after joins and work with `JOIN … USING (product_id)` where appropriate.

- **Bad:** relying on multiple bare `id` columns in joined results
- **Good:** `orders.product_id` matching `products.product_id`

### Primary keys and references: UUID v7

- **Always use UUID version 7** for surrogate primary keys (e.g. `product_id` on `products`) and for every foreign key column that references one (`orders.product_id` → `products.product_id`). Do not default to serial/bigserial, UUID v4, or other id types for new schemas unless you are extending legacy tables that already fixed another representation.
- **Storage:** PostgreSQL `uuid` type for all such columns; one consistent generation path app- and migration-wide.
- **Generation:** Prefer `DEFAULT uuidv7()` on PostgreSQL 18+; on older versions, generate UUID v7 in application code (or an extension) before insert. Never mix v4 and v7 for the same logical id space.
- **Why v7:** Time-ordered UUIDs improve B-tree index locality and range-query behavior versus random UUIDs while staying opaque and stable across services.

### Avoid stuttering (with SQL exceptions)

Do not prefix every column with the table name when the table already disambiguates (`name`, `description` on `products`). **Do** use explicit prefixes for identity, classification, lifecycle, and other ambiguous or colliding names (see table above). The general “no redundant parent context” rule: [domain-naming-conventions](../domain-naming-conventions/SKILL.md).

### Booleans in SQL

Use `is_`, `has_`, `can_`, or `should_` so the column reads as a question: `is_deleted`, `has_discount`, `can_ship`. Same principle as domain/general; examples here are **snake_case** for the database.

## Agent checklist (PostgreSQL-only)

1. Primary/foreign keys: `uuid` columns holding **UUID v7** values; prefer `entity_id` naming for join clarity and `USING` where used.
2. Replace overly generic column names (`id`, `type`, `status`, `date`, `total`) when they would collide or confuse in SQL or search.
3. `snake_case` for columns; keep **word choice** aligned with general + domain skills (map shape at ORM/serializer boundaries, not vocabulary).
