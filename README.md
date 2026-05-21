# Logic Encoder Shop (public overview)

WordPress plugin that maintains the **application catalogue** on logicencoder.com and notifies the **Crypto App Store backend** when products change.

---

## Scope (this plugin only)

- Product posts (`application` CPT): name, price, version, download file id, status badges  
- Admin UI to manage apps and connection settings (backend URL, webhook secret)  
- Webhook to SOL Node service — **no** in-plugin crypto checkout  

## Related systems

| System | Repo |
|--------|------|
| Telegram shop + payments | [le-crypto-app-store](https://github.com/logicencoder/le-crypto-app-store) |
| Site-wide LE settings (SEO, security, alerts) | [le-settings](https://github.com/logicencoder/le-settings) — **different plugin** |

Implementation: [le-shop](https://github.com/logicencoder/le-shop) · [`ARCHITECTURE.md`](https://github.com/logicencoder/le-shop/blob/main/ARCHITECTURE.md)

---

**Made by [logicencoder](https://github.com/logicencoder)**
