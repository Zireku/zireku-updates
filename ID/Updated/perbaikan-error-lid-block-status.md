# Perbaikan Error Penolakan Blokir Sesi LID pada Gateway 1

![Kategori](https://img.shields.io/badge/Kategori-Backend_Engine-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Sudah_Normal-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Updated/perbaikan-error-lid-block-status.md) | [EN (English)](../../EN/Updated/fix-lid-block-status-error.md)

---

> [!IMPORTANT]
> **Pemberitahuan:** Pembaruan logika validasi pemblokiran nomor pada Gateway 1 mengatasi galat Boom 400 'Unable to resolve PN JID for LID: xxx@lid' saat bot menerima pesan pribadi dari akun WhatsApp yang menggunakan format pengenal identitas LID.

---

### 1. Apa Masalahnya?
* Pengguna mendapati galat berulang pada log PM2 Gateway 1 berupa 'Error: Unable to resolve PN JID for LID: 221694031593653@lid' yang dipicu dari fungsi updateBlockStatus di Baileys dan handler.js.
* Penyebab utama: Pemeriksaan Config.blocks (daftar blokir kode negara nomor virtual seperti 994, 221, dan 212) sebelumnya memeriksa m.sender?.startsWith(no) secara langsung tanpa membedakan apakah pengirim berupa nomor telepon asli (@s.whatsapp.net) atau pengenal privasi akun WhatsApp (@lid).
* Angka pada identitas LID (misalnya 221694031593653@lid) merupakan angka acak 15 digit yang ditetapkan WhatsApp, bukan kode panggilan negara Senegal (+221).
* Akibatnya, akun pengguna normal yang kebetulan memiliki awalan angka 221 pada LID mereka secara keliru dianggap sebagai nomor spam dari Senegal dan memicu pemblokiran otomatis.
* Protokol privasi WhatsApp mewajibkan keberadaan nomor telepon (pn_jid) saat melakukan blokir. Karena pemetaan nomor belum tersedia untuk LID tersebut, Baileys menolak permintaan dan memunculkan galat Boom 400.

---

### 2. Apa yang Diperbaiki?
* Menambahkan fungsi pembantu isBlockedNumber pada handler.js yang secara khusus hanya memeriksa awalan kode negara pada nomor telepon berformat @s.whatsapp.net, dan secara otomatis mengecualikan pengenal @lid yang bukan nomor telepon.
* Mengganti seluruh panggilan langsung client.updateBlockStatus pada handler.js dengan safeUpdateBlockStatus dari lib/system/block-status.js agar seluruh operasi pemblokiran dieksekusi secara aman dengan penanganan antrean dan proteksi galat.
* Memperbarui lib/system/block-status.js untuk mencoba menyelesaikan LID ke nomor telepon asli melalui database dan cache kontak. Jika pemetaan nomor tidak ditemukan, proses pemblokiran diskip secara aman tanpa memicu galat uncaught pada Baileys.
* Memperketat penanganan galat catch pada handler.js agar galat terkait LID unresolvable tidak menyebabkan pengiriman pesan galat ke pengguna atau membanjiri log PM2.

---

### 3. Apa Efeknya ke User?
* Pengguna yang menggunakan WhatsApp dengan pengenal akun berformat LID kini dapat mengirim pesan dan menggunakan bot secara normal tanpa diblokir secara keliru.
* Bot tidak lagi mengirim balasan pesan error kepada pengguna ketika menerima interaksi dari akun bertipe LID.
* Layanan gateway berjalan stabil, bersih dari log galat Boom 400, dan pemblokiran nomor spam tetap bekerja akurat untuk nomor virtual asli.

---

### 4. Hasilnya
Galat penolakan blokir LID pada Gateway 1 telah teratasi sepenuhnya. Penyesuaian juga telah diterapkan ke Gateway 2 dan Gateway 3 untuk pencegahan menyeluruh di seluruh cluster gateway.
