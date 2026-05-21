# Logic Encoder Shop — public overview

WordPress plugin that runs the **product catalogue** for LogicEncoder’s Telegram-based software store on [logicencoder.com](https://logicencoder.com).

**Implementation (private):** [le-shop-plugin](https://github.com/logicencoder/le-shop-plugin)

---

## The problem

LogicEncoder sells downloadable tools (scripts, apps, utilities) to a technical audience. Checkout happens inside **Telegram** with on-chain payment, but marketing pages and editorial workflow live on **WordPress**. Without a bridge:

- Prices and versions would be edited in two places (WP and a Node config file).  
- The store backend would show stale products after a WP update.  
- Operators would not know if payment files (`530.zip`, etc.) are missing on the server until a buyer fails.

This plugin keeps **WordPress as the catalogue source of truth** and notifies the live store service whenever something changes.

---

## Who benefits

| Audience | Benefit |
|----------|---------|
| **Site operator** | Familiar wp-admin UI: add apps, set price/version/badge, test webhook, compare WP vs backend warehouse |
| **Buyer (indirect)** | Mini App shows the same titles, prices, and badges as the public site |
| **Developer maintaining SOL backend** | Predictable `wp-changed` events and REST shape for sync jobs |

---

## How this fits the stack (read this before confusing plugins)

| Piece | Role |
|-------|------|
| **le-shop-plugin** (this overview) | WordPress **catalogue** + webhook |
| **le-crypto-app-store** (private) | Node on SOL: orders, USDC/ETH matching, zip delivery, Telegram bot |
| **le-settings-plugin** (separate) | Site security, SEO meta, brute force — **not** the shop |

Payments never run inside PHP. If you are looking for “how does ERC20 checkout work?”, see **le-crypto-app-store-overview**.

---

## Capabilities (detailed)

### Application posts (`application` custom type)

**What:** Each sellable item is a public WordPress post with extra fields: USD price, semantic version string, display size, marketing badge (Hot/New/…), requirements text, and a **file id** that maps to a zip on the backend.

**Why:** Editors already know wp-admin; they should not SSH to SOL to change a price. Structured meta keeps the Telegram shop and REST export consistent.

**Who benefits:** Operator publishing new tools; SEO visitors reading public application pages on the site.

### Automatic backend sync (webhook)

**What:** When an application is published, updated, trashed, or deleted, WordPress sends a small JSON notification to the store backend (`wp-changed`).

**Why:** The backend maintains a DuckDB product table and serves the Mini App. Without instant notification, buyers could see old prices for minutes or hours.

**Who benefits:** Buyers see current pricing; operators do not run manual “resync” after every edit.

### REST metadata for machines

**What:** The WordPress REST API exposes a `metadata` object on each application (price, version, file id, etc.).

**Why:** The backend can bulk-import the catalogue from `logicencoder.com` for disaster recovery or initial setup, using the same fields the Mini App uses.

**Who benefits:** Automation and the SOL service’s `sync-from-wp` tooling.

### Operator dashboard in wp-admin

**What:** A dedicated **LE Shop** menu shows how many apps are live, whether the backend URL responds to a test webhook, and summary stats from the remote warehouse (how many zips exist on disk).

**Why:** Connection issues (Cloudflare tunnel down, wrong secret) are visible before a launch or sale campaign.

**Who benefits:** Operator on call; reduces “site looks fine but shop is offline” incidents.

### Items / Warehouse screen

**What:** Side-by-side view: all WordPress applications vs live list from the Node warehouse API, including a **disk OK / MISSING** flag per file id.

**Why:** A common failure mode is publishing app meta `file_id=530` without uploading `530.zip` to the backend downloads folder.

**Who benefits:** Operator and support — fix missing files before customers pay.

### Integration settings

**What:** Configure backend base URL, webhook shared secret, and admin API key (plus optional Telegram bot links for documentation in UI).

**Why:** Hostinger WordPress and SOL Node are different machines; only these settings connect them securely.

**Who benefits:** Deployer aligning WP options with `.env` on SOL.

---

## What this overview does not include

- Plugin PHP source, webhook secrets, or admin keys  
- Order lifecycle, blockchain listener, or Telegram bot logic  

Those belong to the private **le-crypto-app-store** repository.

---

## Related repositories

See [REPOS.md](REPOS.md) in this repo.

---

**Made by [logicencoder](https://github.com/logicencoder)**
