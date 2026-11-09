# Audit of Invoice CS-012IZXKO, Vault Plan Verification, and Stepper Button Polish

![Category](https://img.shields.io/badge/Category-Billing_&_Wizard-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Normalized-22c55e?style=for-the-badge)

---

> Language / Bahasa : [ID (Bahasa Indonesia)](../../ID/Updated/invoice-audit-stepper-gw1.md) | [EN (English)](../../EN/Updated/invoice-audit-stepper-gw1.md)

---

> [!IMPORTANT]
> **Notice:** Audit findings for invoice `CS-012IZXKO` on Gateway 1 VIP, vault account plan verification, stepper waiting button refinement, and bot onboarding workflow sync.

---

### Update Information
* **Date:** November 10, 2026
* **Components:** `components/CreateBotModal.vue` and `core/routes/api/webhook/pakasir.js`
* **Target Server:** Gateway 1 VIP (`165.101.18.83`)
* **Deployment Method:** Nuxt 3 Build Safe (*Zero Downtime*)

---

## 1. Audit Findings: Invoice & Account State

1. **Invoice `CS-012IZXKO` Status:**
   * **Invoice ID:** `CS-012IZXKO`
   * **Amount:** Rp 10,150 (Core Plan 15 Days + Fee Rp 150)
   * **Gateway:** Midtrans (`c142961b-75ae-4555-8b49-81e1eff350f8`)
   * **Status:** `settlement` / `Paid` (Successfully received and recorded in database).

2. **Vault Account State (`745436`):**
   * **Username:** `ricco` (`riccoalamsyah881@gmail.com`)
   * **Plan Status:** Updated to `core` plan (Not free).
   * **Response Limit:** 20,000 responses.
   * **Validity:** Active until November 25, 2026 (`expired: 1795401994012`).
   * **Why Bot Was Not in Active Bots List:** The user stopped at Step 4 of the wizard modal and had not completed Step 5 (WhatsApp Pairing/QR connection). Once WhatsApp is linked in Step 5, `action-auth-connect.js` automatically assigns the `core` plan to the newly connected bot.

---

## 2. UI Issues & Resolutions

1. **Stepper Waiting Button Refinement:**
   * **Issue:** The disabled "Waiting for Payment" button on Step 4 wrapped into two lines on small screens/modals, forming an oversized clumsy pill.
   * **Fix:** Introduced the `.stepper-btn-waiting` styling with compact proportions (`font-size: 0.72rem`, `padding: 0.28rem 0.65rem`, `border-radius: 9999px`, `white-space: nowrap`), rendering as an elegant single-line pill indicator.

2. **Instant Sync for Paid Invoices:**
   * When restoring draft data in the wizard modal (`restoreDraft`), an immediate backend poll (`pollPayment`) is triggered.
   * When an existing active plan is found in the user vault (`checkExistingActivePlan`), payment is immediately verified (`isPaymentComplete = true`), cleanly unblocking the "Continue to Connect Bot" button.

3. **Pakasir Webhook Fallback for Vault Accounts:**
   * Added `vaultPlanHandler` to `core/routes/api/webhook/pakasir.js` ensuring pre-paid onboarding registrations without existing bot records are persisted directly to vault accounts.

---

## 3. Verification

* Direct cURL test against `/action/check-payment` confirmed status `Payment already received` (HTTP 200).
* Stepper waiting button displays in clean, single-line compact dimensions.
* Nuxt 3 safe build completed cleanly with exit code 0 on Gateway 1 VIP.
