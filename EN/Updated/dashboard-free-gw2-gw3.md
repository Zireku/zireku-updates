# Visual Polish & Badge Alignment for Gateway 2 and Gateway 3 Dashboard

![Category](https://img.shields.io/badge/Category-Web_Interface-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Normalized-22c55e?style=for-the-badge)

---

> Language / Bahasa : [ID (Bahasa Indonesia)](../../ID/Updated/dashboard-free-gw2-gw3.md) | [EN (English)](../../EN/Updated/dashboard-free-gw2-gw3.md)

---

> [!IMPORTANT]
> **Notice:** Visual refinement, badge dimension unification, watermark robot icon relocation, and monochrome dark white palette standardization for Gateway 2 and Gateway 3 dashboards.

---

### Update Information
* **Date:** November 10, 2026
* **Component:** `pages/dashboard/index.vue`
* **Target Servers:** Gateway 2 (`wapify2.zireku.com`) & Gateway 3 (`wapify3.zireku.com`)
* **Deployment Method:** Nuxt 3 Build Safe (*Zero Downtime*)

---

## 1. Identified Issues

1. **Badge Sizing & Color Inconsistency:**
   * The first badge was rendered in bright green with different padding and font size compared to the adjacent pill.
   * Text retained old naming conventions.

2. **Watermark Robot Icon Position Too High:**
   * The background robot watermark sat high up at `top: 50%`, overlapping the server chip at the top right.

3. **Green Color in Server Specification Box:**
   * Robot circle, node type text, and cluster status pill used bright green accents rather than clean dark white monochrome styling.

4. **Vertical Gap from UserProfileHeader:**
   * Distance between `UserProfileHeader` and the welcome hero card was overly spacious (`mb-4`).

---

## 2. Solutions & Fixes Applied

1. **Badge Sizing & Dark White Palette Unification:**
   * Both badges (`free-server-badge` and `free-status-pill`) share identical dimensions (`font-size: 0.72rem`, `padding: 0.28rem 0.65rem`, `border-radius: 9999px`, `gap: 7px`).
   * Color palette unified to transparent dark white (`rgba(255, 255, 255, 0.04)`, border `1px solid rgba(255, 255, 255, 0.08)`, pure white text) with zero green accents.
   * Dynamic server name badge displays `Server 2 Aktif` / `Server 3 Aktif`, with second badge showing `Ready to Connect` (`Siap Dihubungkan`).

2. **Watermark Robot Icon Relocation:**
   * Moved `.free-watermark-icon` to the bottom right corner (`bottom: -35px; right: -20px; top: auto; transform: none`) with subtle opacity (`0.03`).

3. **Dark White Server Spec Box Standardization:**
   * Robot icon circle resized to `56px` with dark white styling (`rgba(255, 255, 255, 0.04)`).
   * Node type, `99.9%` uptime, and `Available` slots formatted in crisp white typography.
   * Bottom pill updated to `Cluster Ready` with transparent border and white glowing dot.

4. **Header Vertical Spacing Refinement:**
   * Tightened margin below `UserProfileHeader` from `mb-4` to `mb-2`.

---

## 3. Verification

* Clean compilation on both gateways with zero downtime.
* Visually harmonious layout matching dark and light mode designs.
