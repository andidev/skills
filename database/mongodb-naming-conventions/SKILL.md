---
name: database/mongodb-naming-conventions
description: >-
  Applies MongoDB and application-layer naming conventions for documents,
  collections, fields, and ID mapping. Use when designing schemas, writing
  Mongoose/ODM models, API DTOs, migrations, indexes, or when the user mentions
  MongoDB naming, collection names, _id mapping, or document field conventions.
---

# MongoDB naming conventions

MongoDB is schemaless, but consistent naming matters for indexing, queries, and clarity. Follow these rules when modeling documents, APIs, and app types.

## Field mapping: DB vs app

| Category | Database field | App/model field | Notes |
| :--- | :--- | :--- | :--- |
| **Primary key** | `_id` | e.g. `productId` | Mongo requires `_id`; expose a clear name in code and APIs. |
| **Reference** | `userId` | `user` | Store the ObjectId/string as `userId`. Use `user` only when populated or embedded. |
| **Classification** | `productType` | `productType` | Prefer over `type` to avoid clashing with JS `type` in destructuring. |
| **Status** | `status` | e.g. `orderStatus` | Prefer explicit names in app layer when multiple status concepts exist. |
| **State** | `isActive` | `isActive` | Booleans: `is` / `has` / `can` prefixes for clarity. |
| **Timestamps** | `createdAt` | `createdAt` | Standardize on camelCase (JS-native) in DB and app unless team chooses otherwise. |

## `_id` mapping

- **Database:** `{ "_id": ObjectId("...") }`
- **API/code:** `{ "productId": "..." }` (or domain-specific id name)

Map `_id` to a domain-specific id in serializers and public responses so payloads are self-describing for clients and integrations.

## Collections

- **Plural, lowercase:** `products`, `users`, `orders`
- **Avoid:** singular PascalCase, SQL-style suffixes (`user_table`)

## Avoid stuttering in documents

Documents are already in a collection context; do not repeat the collection name in every field.

- **Bad:** `{ "product_name": "Laptop", "product_price": 1000 }`
- **Good:** `{ "name": "Laptop", "price": 1000 }`

## Nested objects

Name the embedded object after the entity it represents:

```json
{
  "name": "iPhone 15",
  "dimensions": { "length": 147, "width": 71 }
}
```

## Agent checklist

When adding or changing MongoDB-related code:

1. Use plural lowercase collection names.
2. Map `_id` ↔ domain id consistently in layers that cross the DB boundary.
3. Use `*Id` for stored references; reserve populated object names for expanded data.
4. Prefer camelCase field names aligned with the application unless legacy data forces otherwise.
