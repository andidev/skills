---
name: mongodb-naming-conventions
description: >-
  Applies MongoDB-specific naming: collections, document fields, _id mapping
  (UUID v7), and nested documents. Use when designing schemas, Mongoose/ODM
  models, migrations, indexes, or when the user mentions MongoDB naming,
  collection names, _id mapping, UUID v7, or document field conventions. Pair
  with general-naming-conventions and domain-naming-conventions for cross-stack
  vocabulary and DB↔app↔API boundaries.
---

# MongoDB naming conventions

MongoDB is schemaless, but consistent naming matters for indexing, queries, and clarity.

## Read first

Apply these **after** shared rules so vocabulary and layer boundaries stay aligned:

| Skill | Role |
| :--- | :--- |
| [general-naming-conventions](../general-naming-conventions/SKILL.md) | One concept across the stack; searchability; casing (MongoDB stored fields are typically **camelCase** in this repo’s pairing with JS). |
| [domain-naming-conventions](../domain-naming-conventions/SKILL.md) | Ubiquitous language; **DB vs app vs API** field mapping (`*Id` vs populated entities, timestamps, statuses). |

Do not re-derive those topics here—use the linked skills.

## MongoDB-specific rules

### `_id` and public identifiers

- **Always use UUID v7** for `_id` on every document and for any stored reference to another document’s `_id` (e.g. `userId`, `orderId`). Do not use `ObjectId`, UUID v4, or auto-increment numbers for new data unless you are extending a legacy schema that already committed to another type.
- **Storage:** Pick one canonical representation app-wide—typically the standard string form (`"018f1234-5678-7abc-8def-123456789abc"`) or BSON UUID (`Binary` subtype 4)—and use it for both `_id` and `*Id` reference fields so values round-trip without conversion bugs.
- **Why v7:** Time-ordered UUIDs improve index locality and range-query behavior versus random UUIDs while staying opaque and URL-safe as strings.
- **API/code:** `{ "productId": "<same-uuid-v7-string>" }` (or another domain-specific id name). Map `_id` to that name in serializers and public responses so payloads are self-describing; the value is still the UUID v7.

### Collections

- **Plural, lowercase:** `products`, `users`, `orders`
- **Avoid:** singular PascalCase, redundant suffixes (`user_table`, `users_collection`)

### Document fields

- Prefer **camelCase** in stored documents when the application is JS/TS-heavy (matches [general-naming-conventions](../general-naming-conventions/SKILL.md)).
- Store references as `*Id` (e.g. `userId`); use the entity name when the value is populated or embedded—see [domain-naming-conventions](../domain-naming-conventions/SKILL.md).

### Avoid stuttering in documents

The collection already provides context; do not prefix every field with the collection name (e.g. avoid `product_name` on `products`). Same idea as table/collection rules in [domain-naming-conventions](../domain-naming-conventions/SKILL.md).

### Nested / embedded documents

Name embedded objects after the **concept** they represent (e.g. `dimensions`), not only their structural role. Examples and DTO shape guidance: [domain-naming-conventions](../domain-naming-conventions/SKILL.md).

## Agent checklist (Mongo-only)

1. Plural lowercase collection names; no redundant `_table`-style suffixes.
2. `_id` and all cross-collection `*Id` references use UUID v7 in one consistent stored representation (string or BSON UUID).
3. Map `_id` ↔ domain id consistently across DB boundary and APIs.
4. Stored fields: camelCase and `*Id` for references unless legacy data forbids—details in general + domain skills above.
