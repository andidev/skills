---
name: postgresql-naming-conventions
description: >-
  Applies PostgreSQL and SQL naming conventions for tables, columns, keys, and
  booleans (explicit naming, JOIN-friendly keys, searchability). Use when
  designing schemas, writing migrations, SQL, ORM models, or when the user
  mentions PostgreSQL naming, column names, primary/foreign keys, or SQL style.
---

# PostgreSQL naming conventions

To ensure code maintainability, prevent SQL naming collisions, and improve searchability, follow an **explicit naming** convention.

## Naming standards table

| Column category | Generic (avoid) | Explicit (use) | Reasoning |
| :--- | :--- | :--- | :--- |
| **Identity** | `id` | `product_id` | Prevents ambiguity in `JOIN` results. |
| **Classification** | `type` | `product_type` | Avoids reserved keywords; clarifies context. |
| **Lifecycle** | `status` | `order_status` | Distinguishes between entity states in complex queries. |
| **State (boolean)** | `active` | `is_active` | Clearly identifies the field as a true/false flag. |
| **Time** | `date` | `created_at` | Differentiates a moment (`at`) from a calendar day (`date`). |
| **Math** | `total` | `total_amount` | Specifies whether the value is a sum, count, price, etc. |

## Best practices

### 1. The JOIN rule

Always use `entity_id` for primary keys. This supports `JOIN … USING (product_id)` and keeps joined column names distinct.

- **Bad:** `WHERE orders.id = products.id`
- **Good:** `WHERE orders.product_id = products.product_id`

### 2. Avoid stuttering (except types/status)

Do not prefix every column with the table name. If an attribute is unique within the row, keep the name short.

- **Avoid:** `product_name`, `product_description`
- **Use:** `name`, `description`

Reserve explicit prefixes for identity, classification, lifecycle, and other ambiguous or colliding names (see table above).

### 3. Boolean questioning

Boolean columns should read like a yes/no question. Use prefixes: `is_`, `has_`, `can_`, or `should_`.

Examples: `is_deleted`, `has_discount`, `can_ship`.

### 4. Searchability

Before naming a field, ask: *If I search the whole project for this term, will hits be meaningful?*

- Searching for `type` returns noise.
- Searching for `product_type` isolates product classification.

## Agent checklist

When adding or changing PostgreSQL-related schema or SQL:

1. Prefer `table_entity_id` style primary and foreign keys for JOIN clarity and `USING` compatibility.
2. Replace generic names (`id`, `type`, `status`, `date`, `total`) with explicit, domain-scoped names when ambiguity is likely.
3. Name booleans with `is_` / `has_` / `can_` / `should_` (or equivalent clear question form).
4. Avoid repeating the table name on every column unless it disambiguates types, status, or collisions.
