# Pembersihan Data Mockup Invoice & Pemulihan Sesi Bot Beku (Freeze)

## Ringkasan Perbaikan
Dokumentasi ini mencakup penyelesaian investigasi terhadap dua isu kritis:
1. **Pembersihan Data Mockup Invoice di SubscriptionPlan.vue**: Penghapusan data invoice palsu/mockup hardcoded yang sebelumnya muncul pada akun baru atau akun yang belum memiliki transaksi.
2. **Pemulihan Bot Diam/Tidak Merespons (Frozen Zombie Socket)**: Identifikasi akar masalah bot WhatsApp yang terlihat 'Online' namun tidak merespons perintah/pesan serta pengujian QA (.ping) dan perbaikan permanen deadlock state.

---

## 1. Investigasi & Perbaikan Invoice SubscriptionPlan.vue
- **Akar Masalah**:
  Pada komponen `nuxt/pages/SubscriptionPlan.vue`, properti computed `invoiceList` memiliki data fallback hardcoded (sample data dummy berupa #INV-20241212, #INV-20241010, dan #INV-20240912). Jika sebuah akun baru dibuat atau baru saja di-reset, array transaksi bernilai kosong (`rawInvoices.value.length === 0`), sehingga computed property otomatis jatuh ke fallback mock data tersebut.
- **Tindakan**:
  - Menghapus seluruh array fallback mockup data dari computed property.
  - Mengembalikan array kosong (`[]`) secara konsisten ketika user belum memiliki transaksi pembayaran.
  - Memperbarui metadata kartu (`latestPaymentMethod`, `latestPurchaseDate`, `latestInvoiceNumber`) agar menampilkan default strip (`-`) jika riwayat transaksi kosong, bukan tanggal dan invoice palsu.
  - Menambahkan baris pesan empty-state pada tabel: *"Belum ada riwayat transaksi."* (ID) / *"No transaction history available."* (EN).
  - Menerapkan perbaikan di Gateway 1, Gateway 2, dan Gateway 3 serta melakukan zero-downtime build.

---

## 2. Investigasi Bot Diam / Tidak Merespons & Pemulihan Sesi (QA .ping)
- **Akar Masalah**:
  1. Ketika terjadi konflik sesi (misalnya nomor bot yang sama coba dihubungkan di gateway lain), sistem silent conflict interceptor memicu status `control_state = 'stopped'` dan `last_disconnect_reason = 'Stopped manually'`.
  2. Namun socket Baileys di background tetap terhubung ke WhatsApp. Karena socket masih terhubung, endpoint `/action/session-power` dengan aksi `start` menolak permintaan dengan pesan *"Bot is already connected"* (403).
  3. Di sisi lain, `handler.js` memeriksa `bot.data?.users`. Karena bot dalam keadaan 'stopped', database proxy `bot.data` tidak dimuat ke dalam memori. Akibatnya, setiap pesan WhatsApp yang masuk langsung diabaikan secara diam-diam (`return`), menghasilkan gejala bot terlihat online di WhatsApp namun log hening dan bot tidak membalas apa pun.
  4. Selain itu, route backend `/action/session-manage` sebelumnya kekurangan properti `login: true`, sehingga pengecekan kepemilikan sesi gagal dengan 403.
- **Tindakan**:
  - Menambahkan pengaman pada `/action/session-power`: Jika socket bot terdeteksi sudah terhubung namun status kontrol belum `online` (kondisi pause/stopped), aksi `start` otomatis mengaktifkan kembali bot, memuat ulang database proxy `system.proxy.init`, dan mengembalikan status aktif secara mulus.
  - Memperbaiki route `/action/session-manage` dengan menambahkan `login: true` di Gateway 1, Gateway 2, dan Gateway 3.
  - Menghidupkan kembali sesi bot target (`6285124324546.db`) di Gateway 1 VIP hingga status kontrol kembali menjadi `online` dan database aktif.
  - Menjalankan uji QA fungsional terhadap command `.ping` dengan hasil sukses (respons 0ms, Server 1, CPU/RAM/Heap termonitor normal).
