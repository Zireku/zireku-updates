# Perlindungan Otomatis Paket Berbayar saat Migrasi Server

![Kategori](https://img.shields.io/badge/Kategori-Cluster_Server-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Sudah_Normal-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Fixed/migrasi-paket-server.md) | [EN (English)](../../EN/Fixed/server-plan-migration.md)

---

> [!IMPORTANT]
> **Pemberitahuan:** Pengguna yang memindahkan bot antar-server (misal dari Server 1 ke Server 3) kini dijamin 100% membawa paket aktif (Plus/Pro) dan PIN pemulihan tanpa risiko penurunan ke paket Free.

---

### 1. Apa Masalahnya?
Sebelumnya, ketika pengguna memindahkan bot dari Server 1 ke Server 3, catatan paket langganan dan PIN pemulihan tertinggal di basis data server asal. Ketika bot tersambung di server baru, sistem server tujuan sempat menurunkannya ke paket Free karena data paket belum tersinkronisasi.

---

### 2. Apa yang Diperbaiki?
Arsitektur migrasi cluster antar-gateway telah diperbarui secara menyeluruh:
* Alur migrasi keluar kini menyertakan snapshot brankas pemulihan (plan vault), masa aktif, dan nomor PIN ke server tujuan.
* Server penerima memprioritaskan paket berbayar aktif dan langsung menguncinya ke database lokal.
* Ditambahkan saluran komunikasi langsung antar-server di seluruh cluster, sehingga pemulihan paket via PIN atau nomor telepon dapat mencari data ke seluruh node cluster secara otomatis.

---

### 3. Apa Efeknya ke User?
* Pengguna bebas memindahkan bot ke server mana pun tanpa khawatir paket langganan hilang.
* Masa aktif paket Plus atau Pro dan sisa kuota respons tetap utuh dan terbawa otomatis ke server baru.
* Pemulihan mandiri menggunakan PIN bekerja mulus di server mana saja di seluruh cluster.

---

### 4. Hasilnya
Infrastruktur cluster Gateway 1, Gateway 2, dan Gateway 3 kini terintegrasi penuh dan menjamin keamanan paket setiap pelanggan secara permanen.
