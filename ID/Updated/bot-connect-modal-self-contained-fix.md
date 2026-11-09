# Investigasi & Perbaikan Loading Berputar pada Modal Koneksi Bot (Gateway 2 & Gateway 3)

## Masalah yang Terjadi
Pengguna melaporkan bahwa saat mencoba menghubungkan nomor bot (contoh: `6285124324546`) melalui tombol "Buat Bot Sekarang" di halaman dashboard Gateway 2 (`https://wapify2.zireku.com/dashboard`), modal tertahan sangat lama dengan animasi berputar pada pesan:
> "Membuat Koneksi WhatsApp... Mohon tunggu sebentar, sedang mengambil Scan QR Code dari server..."

## Akar Masalah (Root Cause)
1. Pada komponen `CreateBotModal.vue` di Gateway 2 dan Gateway 3, fungsi `handleSubmit` sebelumnya hanya menjalankan `emit('submit', payload)` tanpa melakukan eksekusi HTTP API request sendiri.
2. Pada halaman pemanggil `pages/dashboard/index.vue`, komponen dideklarasikan sebagai:
   `<CreateBotModal ref="createBotModalRef" modal-id="dashboardCreateBotModal" @connected-success="onBotConnectedSuccess" />`
   Halaman dashboard **tidak memiliki pendengar event `@submit`**.
3. Akibatnya, ketika pengguna menekan tombol koneksi, event `submit` terlempar tanpa ada yang menangkap. Request ke `/action/connect` **tidak pernah dikirimkan ke server**.
4. Komponen modal tetap berada pada status `isConnectingSocket = true` menunggu event WebSocket dari Baileys (`connect.<number>`) yang tidak akan pernah tiba karena proses inisiasi sesi di backend tidak pernah dipicu.

## Solusi & Perbaikan yang Diterapkan
1. **Self-Contained Connect Execution pada CreateBotModal.vue:**
   Menambahkan logika eksekusi mandiri (self-contained fallback) di dalam `CreateBotModal.vue` seperti implementasi pada Gateway 1:
   * Menggunakan `getCurrentInstance()` untuk memeriksa apakah parent component menyediakan handler `onSubmit` (`hasParentSubmit`).
   * Jika parent memiliki `onSubmit` (misal pada `BotConnectSection.vue`), modal tetap mendelegasikan event via `emit('submit', payload)`.
   * Jika parent tidak memiliki `onSubmit` (misal pada `pages/dashboard/index.vue`), modal secara mandiri:
     * Mengambil kredensial login via `fetchCredential()` (`/data/credential`).
     * Menyusun payload koneksi dan mengenkripsi security challenge jika diverifikasi.
     * Mengirimkan request POST langsung ke `/action/connect`.
     * Menghubungkan listener WebSocket untuk nomor terkait (`listenToSocket(response.number)`).
     * Menangani respons tantangan verifikasi bot (HTTP 428 challenge) dan galat secara tepat.

## Berkas yang Dimodifikasi
* `nuxt/components/CreateBotModal.vue` pada Gateway 2 (`192.154.111.198:62000`)
* `nuxt/components/CreateBotModal.vue` pada Gateway 3 (`49.12.82.34:61000`)

## Verifikasi Deployment
* Menjalankan `node scripts/build-safe.js` pada Gateway 2 (exit code 0).
* Menjalankan `node scripts/build-safe.js` pada Gateway 3 (exit code 0).
* Verifikasi endpoint HTTP 200:
  * `https://wapify2.zireku.com/dashboard` (200 OK)
  * `https://wapify3.zireku.com/dashboard` (200 OK)
