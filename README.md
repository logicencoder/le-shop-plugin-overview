# LE Shop — WordPress catalogue plugin

WordPress **catalogue authority** for the Logic Encoder Telegram Mini App store — operators manage `application` products in wp-admin; every publish/update/delete syncs to the crypto checkout backend.

Private plugin: [logicencoder/le-shop-plugin](https://github.com/logicencoder/le-shop-plugin). Checkout and delivery: [le-crypto-app-store](https://github.com/logicencoder/le-crypto-app-store) — [store overview](https://github.com/logicencoder/le-crypto-app-store-overview).

## The problem it solves

Product copy and pricing should live in WordPress where editors already work. Payments, chain matching, zip delivery, and the Telegram bot belong in Node — not duplicated in PHP.

**LE Shop** owns the catalogue; **Crypto App Store** owns money and files.

## Operator workflow

Top-level **LE Shop** admin:

- **Dashboard** — product counts, backend connectivity ping
- **Items / Warehouse** — compare WordPress posts vs backend warehouse rows
- **Settings** — backend URL, webhook secret, admin key, bot link

On save/delete of an `application` post, a fire-and-forget webhook notifies the backend (`POST /webhook/wp-changed`).

## Public catalogue

REST **`GET /wp-json/wp/v2/application`** exposes enriched metadata (price, version, badge, file IDs) for the Mini App and marketing pages. Theme renders public application archives; the plugin does not implement checkout UI.

## Ecosystem

| Component | Role |
|-----------|------|
| **le-shop-plugin** | WordPress catalogue + webhook |
| **le-crypto-app-store** | Telegram shop, USDC/ETH payments, delivery |
| **le-settings-plugin** | Site security/SEO (separate) |

See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
