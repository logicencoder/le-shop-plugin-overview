# LE Shop — WordPress catalogue plugin

WordPress **catalogue authority** for the Logic Encoder Telegram Mini App store. Operators manage **`application`** custom post types in wp-admin; every publish, update, or delete syncs to the crypto checkout backend automatically.

Private plugin: [logicencoder/le-shop-plugin](https://github.com/logicencoder/le-shop-plugin). Checkout and delivery: [le-crypto-app-store](https://github.com/logicencoder/le-crypto-app-store) — [store overview](https://github.com/logicencoder/le-crypto-app-store-overview).

## Split of responsibilities

| Layer | Owns |
|-------|------|
| **LE Shop (this plugin)** | Product copy, pricing metadata, version badges, warehouse comparison in wp-admin |
| **Crypto App Store backend** | USDC/ETH payments, unique amount matching, zip delivery, Telegram bot |
| **le-settings-plugin** | Site security/SEO — unrelated to payments |

## Operator workflow

**LE Shop** admin menu:

- **Dashboard** — application counts, backend ping health
- **Items / Warehouse** — side-by-side WordPress posts vs backend warehouse rows (missing files flagged)
- **Settings** — backend URL, webhook secret, admin API key, Telegram bot link/username

On `save_post_application`, `before_delete_post`, and status transitions → fire-and-forget **`POST {backend}/webhook/wp-changed`**.

## Public catalogue

REST **`GET /wp-json/wp/v2/application?_embed&per_page=100`** returns enriched metadata:

- Price, version, badge text
- File IDs for delivery mapping
- Marketing fields consumed by the Mini App and theme app cards

Public archives render through [logic-encoder-theme](https://github.com/logicencoder/logic-encoder-theme-overview) — the plugin does not implement checkout UI.

## Admin AJAX

`le_save_settings`, `le_test_webhook`, `le_fetch_warehouse` — settings persistence and warehouse reconciliation without leaving wp-admin.

See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
