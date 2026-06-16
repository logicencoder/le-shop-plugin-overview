# LE Shop — WordPress catalogue for Logic Encoder Crypto App Store for Logic Encoder Crypto App Store

**Edit products in WordPress. Buyers see them in Telegram. Checkout and delivery stay automatic.**

LE Shop is the WordPress companion to **[Logic Encoder Crypto App Store](https://github.com/logicencoder/le-crypto-app-store-overview)**. Operators manage application listings, prices, versions, and file mappings in **wp-admin** — the same data drives the **Telegram Mini App** storefront and the browser shop. When you publish or update a product, a **webhook** notifies the Node backend instantly so buyers never see stale catalogue data.

LE Shop owns **catalogue content and sync**. It does **not** process payments, watch the chain, or deliver files — that is the checkout backend's job.

**Made by [Logic Encoder](https://logicencoder.com)**

**Product name:** LE Shop · pairs with **Logic Encoder Crypto App Store** · private repo `le-shop-plugin`

---

## Where LE Shop fits

LE Shop edits catalogue in WordPress. **Logic Encoder Crypto App Store** (LE CAS) runs checkout, chain payment, and auto-delivery. Buyers see WP data in the Telegram Mini App — money and files never touch WordPress.

| You do in WordPress | What happens automatically |
|---------------------|----------------------------|
| Publish new application | Webhook → backend refreshes catalogue |
| Change price or version | Mini App shows updated card on next load |
| Set file ID `530` | Backend expects `downloads/530.zip` for delivery |
| Draft or trash item | Webhook removes item from live shop |

---

## Custom post type: `application`

LE Shop registers a public **`application`** post type with REST exposure:

- Standard WP fields: title, editor (description), featured image, excerpt
- Archive and single templates for marketing pages on logicencoder.com
- Menu icon: smartphone dashicon under **⚡ LE Shop** in wp-admin

Each application is one sellable product in the crypto store.

---

## Application technical metadata

The **🚀 Application Technical Data** metabox on every application edit screen stores storefront fields:

| Meta key | Purpose | Example |
|----------|---------|---------|
| `_app_price` | USD price shown in shop | `29.99` |
| `_app_version` | Version label on product card | `2.1.0` |
| `_app_status` | Catalogue status | `active`, `beta`, `live`, `unstable`, `old` |
| `_app_size` | Display size | `2.4 MB` |
| `_app_badge` | Promotional badge | `Hot`, `New` |
| `_app_reqs` | Requirements text | `Windows 10+` |
| `_app_last_upd` | Last update date | `2026-06-01` |
| `_app_file` | **File ID** — maps to backend `downloads/{id}.zip` | `530` |

The file ID is the critical link between WordPress catalogue and automatic delivery. If `530` is set but `530.zip` is missing on the backend server, delivery fails — both LE Shop **Items / Warehouse** and the LE CAS admin **File Checker** surface this.

---

## Feature examples — two per capability

### Application post type & marketing page

1. Publish `Gas Tracker Pro` — public permalink on logicencoder.com for SEO; Telegram shop pulls same title.
2. Draft mode — product hidden from REST until you click Publish and webhook fires.

### Technical metabox — price, version, file ID

1. Set `_app_file` `530` and price `29.99` — Mini App shows $29.99 and backend expects `530.zip`.
2. Badge `NEW` + status `beta` — card shows beta badge in Telegram without code change.

### Webhook auto-sync

1. Update price from $19 to $24 — single **Update** in WP; backend catalogue invalidates within seconds.
2. Trash old product — webhook `deleted`; buyers no longer see retired tool in cart.

### REST metadata for storefronts

1. Theme queries `wp/v2/application` — same metadata JSON as Telegram Mini App cards.
2. Mobile app prototype fetches CORS-enabled REST — no duplicate product database.

### Dashboard — backend health

1. **Refresh** ping — HTTP 200 before Black Friday catalogue push.
2. Stats show 12 active items, 2 ZIPs missing — fix files before announcing sale.

### Items / Warehouse — WP vs backend

1. WP table shows file `712`; backend **Fetch Live** shows ✘ MISSING — you upload ZIP to LE CAS server.
2. Edit button jumps to metabox — fix typo in version without opening LE CAS admin.

### Settings — backend URL & webhook secret

1. **Test Connection** after tunnel URL change — catch wrong secret before publish.
2. Lock `LE_BACKEND_URL` in `functions.php` — editors cannot accidentally point staging to prod.

### Settings — bot deep links

1. Save `https://t.me/YourBot/YourApp` — marketing copies per-item links from LE CAS warehouse UI.
2. Bot username field — documentation for support staff answering “which bot?”

### Operator workflow — new product

1. Monday: upload ZIP, publish WP post, Fetch Live ✔, post deep link in Telegram channel.
2. Friday price drop: edit metabox only — no LE CAS warehouse row edit needed when WP is source.

### Operator workflow — incident

1. Buyer reports wrong version — bump `_app_version` in WP; webhook refreshes shop cards.
2. Delivery failed — LE Shop shows disk OK but WP file ID wrong; fix `_app_file` and re-publish.

---

## Automatic backend sync (webhook)

On every relevant application change, LE Shop sends a **fire-and-forget** `POST` to:

```
{BACKEND_URL}/webhook/wp-changed
```

**Headers:** `Content-Type: application/json`, `X-Webhook-Secret: {secret}`

**Body:** `{ "action": "created"|"updated"|"deleted"|"status_changed"|"ping", "post_id": 123 }`

**Triggers:**

- Save published application (create or update)
- Delete application
- Status transition involving publish, draft, private, or trash

The backend uses this signal to invalidate caches and optionally reload WordPress-sourced products. Operators no longer "remember to sync the store JSON."

**Test ping:** Settings page → **Send Test Ping** — validates URL, secret, and backend reachability before a large catalogue push.

---

## REST API for storefront consumers

LE Shop registers a `metadata` field on `wp/v2/application` responses:

```json
{
  "price": "29.99",
  "version": "2.1.0",
  "status": "active",
  "size": "2.4 MB",
  "badge": "New",
  "reqs": "Windows 10+",
  "last_upd": "2026-06-15",
  "file_id": "530"
}
```

**Consumers:**

- Telegram Mini App (when catalogue source = WordPress)
- Logic Encoder theme app listings
- Backend `/admin/warehouse/sync-from-wp` import

CORS on GET is open (`Access-Control-Allow-Origin: *`) so browser storefronts can fetch catalogue cross-origin.

---

## wp-admin: Dashboard

**Menu:** ⚡ LE Shop → Dashboard

**Quick stats (WordPress):**

- Published applications count
- Draft applications count
- Backend status (ONLINE/OFFLINE after ping)
- Orders today placeholder (live when backend connected)

**Backend connection card:**

- Shows configured backend URL
- **Refresh** pings webhook test endpoint
- Live warehouse summary via AJAX: active items, ZIPs on disk vs missing, current catalogue source (`wordpress` / `warehouse`)

**Quick links:**

- + New Application
- All Applications
- Items / Warehouse
- Settings

Use the dashboard for a **30-second health check** before announcing a new product in Telegram.

---

## wp-admin: Items / Warehouse

**Menu:** ⚡ LE Shop → Items / Warehouse

Two-pane view — WordPress editorial truth vs backend operational truth.

### WordPress applications table

All `application` posts (publish, draft, private):

- ID, name, price, version, file ID, badge, status badge
- Post status indicator (draft/private when not live)
- **Edit** → application metabox
- **View** → public permalink

### Backend warehouse (live fetch)

**Fetch Live** button calls backend `/admin/warehouse` via AJAX (uses Admin API Key from Settings):

- Product ID, name, price, version, file ID
- **Disk column:** ✔ OK or ✘ MISSING — whether `downloads/{file_id}.zip` exists on the Node server
- Status from backend warehouse row
- Current source mode

**Why both tables:** WordPress might show `file_id: 530` while the ZIP was never uploaded to the backend. This screen catches that **before** a buyer pays and hits `PAID_NO_FILES`.

---

## wp-admin: Settings

**Menu:** ⚡ LE Shop → Settings

### Backend integration

| Field | Purpose |
|-------|---------|
| **Backend URL** | Base URL of LE CAS Node service (e.g. tunnel or public HTTPS endpoint) |
| **Webhook Secret** | Must match backend `WEBHOOK_SECRET` — authenticates `/webhook/wp-changed` |
| **Admin API Key** | `x-admin-key` for read-only warehouse fetch (or session auth on newer backends) |

`LE_BACKEND_URL` and `LE_WEBHOOK_SECRET` can be locked as constants in `functions.php` — fields then show as disabled with a note.

**Test Connection:** Send Test Ping → shows HTTP status and response snippet.

### Telegram bot

| Field | Purpose |
|-------|---------|
| **Bot Deep Link** | `https://t.me/YourBot/YourApp` — used for shop and per-item links in marketing |
| **Bot Username** | `@YourBotUsername` — display reference |

**Save All Settings** persists via AJAX (`le_save_settings`).

---

## Operator workflow — add a new product end to end

1. **Upload ZIP** to the LE CAS backend `downloads/` folder (e.g. `530.zip`) and verify in admin **File Checker** or register via warehouse.

2. **WordPress → Applications → Add New**
   - Title and description (marketing copy)
   - Featured image
   - Metabox: price, version, status `active`, file ID `530`, badge if needed

3. **Publish** — LE Shop fires webhook → backend catalogue updates.

4. **Optional:** LE CAS admin → Warehouse → **Import from WP** if using local warehouse mode.

5. **Verify:** LE Shop → Items / Warehouse → **Fetch Live** — confirm ✔ OK on disk column.

6. **Share:** Copy per-item deep link from LE CAS Warehouse → Shop & Item Links, or full bot link from Settings.

7. **Sell:** Buyer pays in Telegram → chain watcher → auto delivery. Operator sees order in Telegram alert + LE CAS Orders tab in real time.

**Price change only:** edit metabox → Update post → webhook → done. No backend redeploy.

**Unlist product:** set status to draft or `inactive` in metabox, or trash post — webhook removes from live catalogue.

---

## What LE Shop does not do

| Not in this plugin | Handled by LE CAS backend |
|--------------------|---------------------------|
| Payment processing | USDC/ETH on-chain matching |
| Order creation | `/test-data` checkout endpoint |
| Chain watching | Embedded watchdog |
| File delivery | Telegraf bot + download tokens |
| Order admin UI | Admin dashboard `/admin` |
| Buyer live status | Buyer WebSocket hub |

Keeping boundaries strict prevents WordPress from becoming a payment surface and keeps delivery logic in one auditable Node process.

---

## Tech stack

| Layer | Technologies |
|-------|----------------|
| Platform | WordPress plugin (PHP 8.0+) |
| Product model | CPT `application` + post meta |
| Admin UI | wp-admin pages (Dashboard, Items/Warehouse, Settings) + AJAX |
| Sync | `wp_remote_post` webhook (non-blocking) |
| Public data | WordPress REST API `wp/v2/application` + `metadata` field |
| Styling | Scoped admin CSS (`.le-wrap`, `.le-card`, `.le-tbl`) |

---

## Related repositories

| Repository | Role |
|------------|------|
| [le-shop-plugin](https://github.com/logicencoder/le-shop-plugin) | Private plugin code |
| [le-shop-plugin-overview](https://github.com/logicencoder/le-shop-plugin-overview) | This product overview |
| [le-crypto-app-store](https://github.com/logicencoder/le-crypto-app-store) | Private checkout backend |
| [le-crypto-app-store-overview](https://github.com/logicencoder/le-crypto-app-store-overview) | Telegram shop + auto-delivery platform overview |

See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
