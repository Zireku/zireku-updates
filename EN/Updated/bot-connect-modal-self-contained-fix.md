# Investigation & Fix for Infinite Loading Spinner on Bot Connection Modal (Gateway 2 & Gateway 3)

## Issue
Users reported that when trying to connect a bot number (e.g., `6285124324546`) using the "Buat Bot Sekarang" button on the Gateway 2 dashboard (`https://wapify2.zireku.com/dashboard`), the modal was stuck in an infinite loading spinner stating:
> "Membuat Koneksi WhatsApp... Mohon tunggu sebentar, sedang mengambil Scan QR Code dari server..."

## Root Cause
1. In `CreateBotModal.vue` on Gateway 2 and Gateway 3, `handleSubmit` only executed `emit('submit', payload)` and relied entirely on the parent component to make the network request.
2. In `pages/dashboard/index.vue`, `CreateBotModal` was declared without an `@submit` listener:
   `<CreateBotModal ref="createBotModalRef" modal-id="dashboardCreateBotModal" @connected-success="onBotConnectedSuccess" />`
3. Because `@submit` was not caught by `index.vue`, the POST request to `/action/connect` was **never transmitted to the server**.
4. The modal stayed indefinitely in `isConnectingSocket = true` waiting for Baileys WebSocket events that were never initiated.

## Solution & Applied Fix
1. **Self-Contained Connect Execution in CreateBotModal.vue:**
   Implemented self-contained connect fallback logic in `CreateBotModal.vue` matching Gateway 1:
   * Inspected `getCurrentInstance()` to detect if `hasParentSubmit` is present.
   * If `hasParentSubmit` exists, delegates via `emit('submit', payload)`.
   * If `hasParentSubmit` is missing, the modal directly executes the connection flow: fetches credentials, encrypts challenge headers, posts to `/action/connect`, and registers the socket listener.

## Files Modified
* `nuxt/components/CreateBotModal.vue` on Gateway 2 (`192.154.111.198:62000`)
* `nuxt/components/CreateBotModal.vue` on Gateway 3 (`49.12.82.34:61000`)

## Verification
* Safe zero-downtime build executed on Gateway 2 (exit code 0).
* Safe zero-downtime build executed on Gateway 3 (exit code 0).
* Verified HTTP 200 on:
  * `https://wapify2.zireku.com/dashboard` (200 OK)
  * `https://wapify3.zireku.com/dashboard` (200 OK)
