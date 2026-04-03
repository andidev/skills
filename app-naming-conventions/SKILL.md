---
name: app-naming-conventions
description: >-
  Enforces monorepo application naming (e.g. Nx) by platform or role. New or
  renamed apps use a suffix: -webapp (browser), -desktopapp (desktop), -app (iOS
  and Android), -androidapp (Android-only), -iosapp (iOS-only), or -server
  (backend API / Node server). Use when scaffolding apps, renaming projects,
  reviewing structure, or when the user mentions apps, webapp, desktop, mobile,
  or server.
---

# Application naming conventions (monorepo apps)

## Rule

Top-level **applications** (typically under `apps/` or your workspace’s app root) use a **suffix that encodes the platform or server role**. The leading part is the **application base name**: a kebab-case product or domain identifier (e.g. `checkout`, `customer-portal`). Together, `base-name` + `suffix` form the full application project name.

| Role / platform | Suffix | Example |
| :--- | :--- | :--- |
| Web (browser) | `-webapp` | `checkout-webapp` |
| Desktop | `-desktopapp` | `checkout-desktopapp` |
| Mobile (iOS and Android) | `-app` | `checkout-app` |
| Android-only | `-androidapp` | `checkout-androidapp` |
| iOS-only | `-iosapp` | `checkout-iosapp` |
| Backend API / Node (or similar) server | `-server` | `checkout-server` |

## When this applies

- **Applies:** New applications that ship a UI on web, desktop, or mobile, **and** applications that are backend APIs or app servers (use `-server`).
- **Does not apply:** Libraries, packages, workers, or other project types that are not top-level **apps** (those follow library/package naming, not this table).

## Agent behavior

1. **Scaffolding:** When generating an app, pick the suffix from the table above (including `-server` for backend APIs and app servers). Prefer `directory=apps/<application-base-name><suffix>` (e.g. `apps/checkout-webapp`) and a `package.json` `name` aligned with the workspace convention (e.g. scoped `@your-org/checkout-webapp` when the repo uses npm scopes).
2. **Ambiguity:** If the user does not state platform or role, **ask** whether the app is web, desktop, mobile (iOS and Android), Android-only, iOS-only, or a backend/server before choosing the suffix.
3. **Renames:** If an existing client app violates the rule, propose renaming the folder, monorepo project name, and `package.json` `name` together so imports and task targets stay aligned.

## Alignment with other skills in this repository

The **application base name** (the part before the suffix) should match the **canonical name stem** for that product or bounded context in code and APIs where practical—see `general-naming-conventions`.

## Quick reference

- Web → `*-webapp`
- Desktop → `*-desktopapp`
- Mobile (iOS and Android) → `*-app`
- Android only → `*-androidapp`
- iOS only → `*-iosapp`
- Backend / API server → `*-server`
