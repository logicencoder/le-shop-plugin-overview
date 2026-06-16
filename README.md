# LE Shop — WordPress catalogue plugin

LE Shop is the catalogue control panel for the Logic Encoder software store. Operators create and edit app listings in WordPress, and every publish/update/delete event is pushed to the checkout backend so Telegram Mini App buyers always see the current title, price, version, and download mapping.

## Tech stack

| Layer | Technologies |
|-------|--------------|
| Platform | WordPress plugin (PHP) |
| Product model | Custom post type `application` + post meta |
| Sync | Webhook calls from WordPress to Node backend |
| Admin UX | wp-admin pages + AJAX actions |
| Public data | WordPress REST API (`wp/v2/application`) |

## Catalogue editing in wp-admin

The plugin adds an `application` post type and structured metadata for each product entry: pricing, version, badge/status, requirements, file ID, and update label. This keeps product marketing and storefront data in one editorial workflow instead of scattered JSON files.

Operators can update product cards without touching backend code. The frontend apps consume the same structured metadata, so catalogue changes are visible consistently in website listings and the Telegram storefront.

## Automatic backend sync

When an application changes state (created, updated, deleted, status changed), LE Shop sends a fire-and-forget webhook to the checkout backend (`/webhook/wp-changed`). This removes the old manual "remember to sync store data" step.

The webhook layer is built for operational safety: the secret is configurable in Settings, and operators can run a test call from wp-admin to validate backend reachability before publishing large catalogue updates.

## Dashboard, warehouse, and settings screens

LE Shop ships three operator screens in wp-admin:

- **Dashboard** for quick counts and backend health checks.
- **Items / Warehouse** to compare WordPress catalogue rows against backend warehouse rows and detect missing files.
- **Settings** for backend URL, webhook secret, admin API key, and Telegram bot pointers used in the buying flow.

This split makes incident work faster: if buyers report delivery issues, operators can check backend health and warehouse mismatches immediately from WordPress.

## REST metadata for storefront consumers

The plugin enriches `application` REST responses with normalized metadata used by other surfaces:

- Mini App product cards
- Theme app listings
- Backend import/sync flows

That means one product definition in WordPress can drive multiple storefront UIs without duplicate data entry.

## Product boundaries

LE Shop owns catalogue content and sync orchestration. It does **not** process payments or send delivery files. Those steps are handled by the dedicated checkout backend.

Private code: [logicencoder/le-shop-plugin](https://github.com/logicencoder/le-shop-plugin)  
Related checkout service: [le-crypto-app-store](https://github.com/logicencoder/le-crypto-app-store)  
See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
