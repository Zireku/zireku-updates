# Perbaikan Error Penolakan Blokir Sesi LID pada Gateway 1

![Kategori](https://img.shields.io/badge/Kategori-Backend_Engine-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Sudah_Normal-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Updated/perbaikan-error-lid-block-status.md) | [EN (English)](../../EN/Updated/fix-lid-block-status-error.md)

---

> [!IMPORTANT]
> **Pemberitahuan:** Pembaruan logika pengamanan pemblokiran nomor mandiri langsung di dalam `handler.js` mengatasi galat Boom 400 'Unable to resolve PN JID for LID: xxx@lid' saat bot menerima pesan pribadi dari akun WhatsApp yang menggunakan format pengenal identitas LID. File non-standar `block-status.js` telah dihapus sepenuhnya sesuai arsitektur upstream Neoxr Bot v5.

---

### 1. Apa Masalahnya?
* Pengguna mendapati galat berulang pada log PM2 Gateway 1 berupa 'Error: Unable to resolve PN JID for LID: 221694031593653@lid' yang dipicu dari fungsi updateBlockStatus di Baileys dan handler.js.
* Penyebab utama: Pemeriksaan Config.blocks (daftar blokir kode negara nomor virtual seperti 994, 221, dan 212) sebelumnya memeriksa m.sender?.startsWith(no) secara langsung tanpa membedakan apakah pengirim berupa nomor telepon asli (@s.whatsapp.net) atau pengenal privasi akun WhatsApp (@lid).
* Angka pada identitas LID (misalnya 221694031593653@lid) merupakan angka acak 15 digit yang ditetapkan WhatsApp, bukan kode panggilan negara Senegal (+221).
* Akibatnya, akun pengguna normal yang kebetulan memiliki awalan angka 221 pada LID mereka secara keliru dianggap sebagai nomor spam dari Senegal dan memicu pemblokiran otomatis.
* Protokol privasi WhatsApp mewajibkan keberadaan nomor telepon (pn_jid) saat melakukan blokir. Karena pemetaan nomor belum tersedia untuk LID tersebut, Baileys menolak permintaan dan memunculkan galat Boom 400.

---

### 2. Apa yang Diperbaiki?
* Hasil pengecekan pada upstream Neoxr Bot v5 (5.0-ESM) mengonfirmasi bahwa file `lib/system/block-status.js` memang tidak ada sama sekali di repositori upstream neoxr-bot.
* File non-standar `lib/system/block-status.js` telah dihapus sepenuhnya dari Gateway 1, 2, dan 3.
* Seluruh dependensi yang sebelumnya mengimpor file tersebut (`plugins/owner/manager.js`, `plugins/_events/anti_bug.js`, dan `lib/system/listeners-extra.js`) telah dikembalikan memanggil fungsi Baileys langsung (`client.updateBlockStatus`) sebagaimana standar upstream.
* Logika perlindungan LID dibuat 100% mandiri di dalam `handler.js`:
  - Fungsi `isBlockedNumber` menyaring nomor agar format @lid tidak diuji terhadap kode negara.
  - Fungsi `safeBlockUser` membungkus pemanggilan blokir dengan isolasi try-catch dan mengabaikan LID tanpa nomor telepon.
  - Penanganan galat global catch pada `handler.js` diperkuat menyaring galat LID unresolvable.

---

### 3. Apa Efeknya ke User?
* Pengguna yang menggunakan WhatsApp dengan pengenal akun berformat LID kini dapat mengirim pesan dan menggunakan bot secara normal tanpa diblokir secara keliru.
* Bot tidak lagi mengirim balasan pesan error kepada pengguna ketika menerima interaksi dari akun bertipe LID.
* Arsitektur kode menjadi bersih, bebas dari file utilitas asing yang tidak ada di upstream Neoxr v5.
* Layanan gateway berjalan stabil dan bersih dari log galat Boom 400.

---

### 4. Hasilnya
Galat penolakan blokir LID pada Gateway 1 telah teratasi sepenuhnya. File `block-status.js` telah dihapus bersih dan seluruh penanganan disatukan di `handler.js` di seluruh cluster Gateway 1, Gateway 2, dan Gateway 3.
