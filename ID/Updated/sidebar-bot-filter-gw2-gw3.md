# Penyesuaian Navigasi Sidebar Berbasis Status Bot & Penyempurnaan Mode Terang

![Kategori](https://img.shields.io/badge/Kategori-Navigasi_Sidebar-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Sudah_Normal-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Updated/sidebar-bot-filter-gw2-gw3.md) | [EN (English)](../../EN/Updated/sidebar-bot-filter-gw2-gw3.md)

---

> [!IMPORTANT]
> **Pemberitahuan:** Pembaruan logika navigasi sidebar pada Gateway 2 dan Gateway 3 untuk menyaring menu saat pengguna belum memiliki bot aktif, serta perbaikan kontras teks pada mode terang.

---

### Informasi Pembaruan
* **Tanggal:** 10 November 2026
* **Komponen:** `layouts/default.vue` dan `pages/dashboard/index.vue`
* **Target Server:** Gateway 2 (`wapify2.zireku.com`) & Gateway 3 (`wapify3.zireku.com`)
* **Metode Deployment:** Nuxt 3 Build Safe (*Zero Downtime*)

---

## 1. Masalah yang Ditemukan

1. **Menu Sidebar Terlalu Ramai Saat Belum Memiliki Bot:**
   * Pengguna baru yang belum membuat atau menghubungkan bot WhatsApp langsung melihat menu operasional bot seperti Terminal, Grup & Chat, Kustomisasi Bot, Pengaturan, dan Layanan.
   * Alur pengguna menjadi membingungkan karena fitur-fitur tersebut membutuhkan bot yang aktif untuk dapat berfungsi.

2. **Keterbacaan Teks Judul Kartu Pada Mode Terang (Light Mode):**
   * Judul kartu panduan (`Beranda`, `List Bot`, `Panduan`, `Sewa Bot`) dan label server (`Server 2` / `Server 3`) tampil berwarna putih sehingga tidak terlihat jelas pada latar belakang putih di mode terang.

---

## 2. Langkah Solusi & Perbaikan

1. **Penyaringan Menu Sidebar Berbasis Status Bot (`botOnly`):**
   * Menambahkan komputasi reaktif `isBotReady` di `layouts/default.vue` berdasarkan status akun (`/data/account`).
   * Saat bot belum terhubung (`!isBotReady`), menu navigasi yang ditampilkan secara eksklusif hanya:
     1. **Beranda** (`/`)
     2. **Dashboard** (`/dashboard`)
     3. **Listbot** (`/listbot`)
     4. **Panduan** (`/panduan`)
     5. **Sewa Manager** (`/sewabot`)
   * Fitur manajemen lanjutan (**Terminal**, **Grup & Chat**, **Kustomisasi Bot**, **Pengaturan**, dan **Layanan**) otomatis terkunci dan baru akan terbuka secara dinamis setelah pengguna menghubungkan bot WhatsApp.

2. **Penyempurnaan Warna Teks Mode Terang:**
   * Menambahkan aturan CSS dengan selector scoped global `:global(body.light-mode)` dan `:global(html[data-theme='light'])`.
   * Memastikan seluruh teks judul (`.free-node-title`, `.free-spec-value`, `.free-nav-title`, `.free-nav-action`) tampil dengan warna gelap kontras (`#0f172a`) pada mode terang dan putih bersih (`#ffffff`) pada mode gelap.

---

## 3. Hasil Pengujian

* Navigasi sidebar tampil ringkas, bersih, dan fokus pada tahap awal registrasi akun pengguna baru.
* Teks antarmuka terbaca jelas dengan kontras optimal pada tema gelap maupun terang.
* Safe build Nuxt 3 selesai dengan status exit code 0 dan verifikasi HTTP 200 OK.
