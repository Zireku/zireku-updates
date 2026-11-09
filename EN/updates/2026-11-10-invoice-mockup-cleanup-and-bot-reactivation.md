# Invoice Mockup Cleanup & Frozen Bot Session Reactivation

## Fix Summary
This documentation covers the resolution of two critical issues:
1. **SubscriptionPlan.vue Mockup Invoice Cleanup**: Removal of hardcoded sample invoice data that erroneously appeared for newly created or reset accounts.
2. **Unresponsive / Silent Bot Reactivation (Frozen Zombie Socket)**: Root cause analysis of WhatsApp bots appearing 'Online' while remaining completely unresponsive to messages, functional QA verification (.ping), and permanent deadlock mitigation.

---

## 1. SubscriptionPlan.vue Invoice Investigation & Fix
- **Root Cause**:
  In `nuxt/pages/SubscriptionPlan.vue`, the computed property `invoiceList` had hardcoded fallback sample data (#INV-20241212, #INV-20241010, #INV-20240912). When a user account was deleted and recreated (or had zero transactions), `rawInvoices.value.length === 0` caused the component to fall back to the dummy mock data.
- **Resolution**:
  - Removed all hardcoded mock arrays from the computed property.
  - Guaranteed an empty array (`[]`) is returned when no invoices exist.
  - Updated card metadata (`latestPaymentMethod`, `latestPurchaseDate`, `latestInvoiceNumber`) to display dashes (`-`) instead of fake values when history is empty.
  - Added an empty-state row in the invoice table: *"Belum ada riwayat transaksi."* (ID) / *"No transaction history available."* (EN).
  - Deployed and compiled with zero-downtime builds across Gateway 1, Gateway 2, and Gateway 3.

---

## 2. Silent Bot Investigation & Session Reactivation (QA .ping)
- **Root Cause**:
  1. During session conflicts (e.g., attempting connection on another server), the silent conflict interceptor set `control_state = 'stopped'` and `last_disconnect_reason = 'Stopped manually'`.
  2. However, the background Baileys WebSocket remained connected. Subsequent `start` actions in `/action/session-power` returned 403 *"Bot is already connected"*.
  3. Meanwhile, `handler.js` checks `bot.data?.users`. Because the bot was flagged as stopped, its database proxy `bot.data` was never initialized in memory. Every incoming WhatsApp message hit `return` immediately, leaving the bot appearing online but dead silent with no logs.
  4. Additionally, `/action/session-manage` lacked `login: true`, causing session status checks to fail with 403.
- **Resolution**:
  - Added safeguards in `/action/session-power`: If a socket is already open but its control state is not `online`, the `start` action unpauses the bot, initializes `system.proxy.init`, and brings it back to healthy operation.
  - Added `login: true` to `/action/session-manage` across all gateways.
  - Successfully restarted and reactivated bot session `6285124324546.db` on Gateway 1 VIP to `online` status.
  - Executed QA simulation testing on `.ping` verifying 0ms response time and full server statistics reporting.
