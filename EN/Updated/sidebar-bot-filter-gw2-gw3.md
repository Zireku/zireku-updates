# Sidebar Navigation Bot-State Filtering & Light Mode Polish

![Category](https://img.shields.io/badge/Category-Sidebar_Navigation-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Normalized-22c55e?style=for-the-badge)

---

> Language / Bahasa : [ID (Bahasa Indonesia)](../../ID/Updated/sidebar-bot-filter-gw2-gw3.md) | [EN (English)](../../EN/Updated/sidebar-bot-filter-gw2-gw3.md)

---

> [!IMPORTANT]
> **Notice:** Navigation sidebar logic update for Gateway 2 and Gateway 3 filtering menus when no bot is connected, along with light mode text contrast refinement.

---

### Update Information
* **Date:** November 10, 2026
* **Components:** `layouts/default.vue` and `pages/dashboard/index.vue`
* **Target Servers:** Gateway 2 (`wapify2.zireku.com`) & Gateway 3 (`wapify3.zireku.com`)
* **Deployment Method:** Nuxt 3 Build Safe (*Zero Downtime*)

---

## 1. Identified Issues

1. **Cluttered Sidebar for Accounts Without Active Bots:**
   * New users without a connected WhatsApp bot were exposed to advanced bot operations such as Terminal, Group & Chat, Bot Customization, Settings, and Services.
   * This created unnecessary complexity since those actions require an operational bot session.

2. **Card Title Visibility in Light Mode:**
   * Navigation card titles (`Beranda`, `List Bot`, `Panduan`, `Sewa Bot`) and server labels (`Server 2` / `Server 3`) were styled with white text, blending into the white background in light mode.

---

## 2. Solutions & Fixes Applied

1. **Bot-State-Driven Sidebar Navigation (`botOnly` Filter):**
   * Implemented reactive `isBotReady` computation in `layouts/default.vue` evaluated from the account state (`/data/account`).
   * When no bot is connected (`!isBotReady`), the sidebar exclusively renders:
     1. **Beranda** (`/`)
     2. **Dashboard** (`/dashboard`)
     3. **Listbot** (`/listbot`)
     4. **Panduan** (`/panduan`)
     5. **Sewa Manager** (`/sewabot`)
   * Advanced management modules (**Terminal**, **Group & Chat**, **Bot Customization**, **Settings**, and **Services**) are hidden until a WhatsApp bot is connected.

2. **Light Mode High-Contrast Typography:**
   * Added scoped global CSS rules via `:global(body.light-mode)` and `:global(html[data-theme='light'])`.
   * Ensures all card headers (`.free-node-title`, `.free-spec-value`, `.free-nav-title`, `.free-nav-action`) display crisp dark slate (`#0f172a`) in light mode and white (`#ffffff`) in dark mode.

---

## 3. Verification

* Clean and intuitive navigation flow for onboarding users.
* High contrast and legibility across all themes.
* Nuxt 3 safe build completed with exit code 0 and verified HTTP 200 OK.
