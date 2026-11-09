# Penyesuaian Detail Visual & Badge Dashboard Gateway 2 dan Gateway 3

![Kategori](https://img.shields.io/badge/Kategori-Tampilan_Web-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Sudah_Normal-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Updated/dashboard-free-gw2-gw3.md) | [EN (English)](../../EN/Updated/dashboard-free-gw2-gw3.md)

---

> [!IMPORTANT]
> **Pemberitahuan:** Pembaruan detail tata letak visual, penyelarasan ukuran badge, reposisi watermark ikon robot, dan standardisasi palet warna gelap putih monokrom pada antarmuka dashboard Gateway 2 dan Gateway 3.

---

### Informasi Pembaruan
* **Tanggal:** 10 November 2026
* **Komponen:** `pages/dashboard/index.vue`
* **Target Server:** Gateway 2 (`wapify2.zireku.com`) & Gateway 3 (`wapify3.zireku.com`)
* **Metode Deployment:** Nuxt 3 Build Safe (*Zero Downtime*)

---

## 1. Masalah yang Ditemukan

1. **Ketidaksamaan Ukuran & Warna Badge:**
   * Badge pertama berpenampilan hijau terang dengan ukuran berbeda dari badge status di sampingnya.
   * Format teks masih menampilkan label server lama.

2. **Posisi Ikon Watermark Robot Terlalu Tinggi:**
   * Watermark ikon robot pada kartu sambutan awal berada di posisi tengah atas (`top: 50%`), sehingga bertabrakan dengan penanda chip server di sudut kanan atas.

3. **Aksen Warna Hijau pada Kotak Spesifikasi Server:**
   * Lingkaran ikon robot, teks spesifikasi tipe node, dan pil status klaster masih menggunakan warna hijau terang, belum selaras dengan tema gelap putih monokrom.

4. **Jarak Antara Header Pengguna dan Kartu Utama:**
   * Jarak vertikal bawah dari komponen header pengguna (`UserProfileHeader`) terlalu renggang (`mb-4`).

---

## 2. Langkah Solusi & Perbaikan

1. **Penyelarasan Ukuran & Warna Badge:**
   * Kedua badge (`free-server-badge` dan `free-status-pill`) kini menggunakan ukuran identik (`font-size: 0.72rem`, `padding: 0.28rem 0.65rem`, `border-radius: 9999px`, `gap: 7px`).
   * Palet warna diseragamkan ke tema gelap putih transparan (`rgba(255, 255, 255, 0.04)`, border `1px solid rgba(255, 255, 255, 0.08)`, teks putih netral) tanpa warna hijau.
   * Teks badge pertama dinamis menampilkan nama server aktif (`Server 2 Aktif` / `Server 3 Aktif`), dan badge kedua menampilkan status `Siap Dihubungkan`.

2. **Reposisi Ikon Watermark Robot:**
   * Posisi ikon robot (`.free-watermark-icon`) dipindahkan ke sudut kanan bawah (`bottom: -35px; right: -20px; top: auto; transform: none`) dengan opasitas halus (`0.03`).

3. **Standardisasi Kotak Spesifikasi Server (Dark White Monochrome):**
   * Lingkaran ikon robot diubah menjadi `56px` dengan warna netral putih (`rgba(255, 255, 255, 0.04)`).
   * Teks tipe node, persentase uptime (`99.9%`), dan ketersediaan slot (`Tersedia`) ditampilkan dalam warna putih bersih.
   * Pil status di bawah kotak spesifikasi diubah menjadi `Cluster Ready` dengan border putih transparan dan dot putih.

4. **Penyesuaian Jarak Vertikal Header:**
   * Margin bawah baris `UserProfileHeader` dirapatkan dari `mb-4` menjadi `mb-2`.

---

## 3. Hasil Pengujian

* Seluruh komponen antarmuka terkompilasi bersih tanpa peringatan runtime.
* Tampilan visual selaras, konsisten, dan rapi pada mode gelap maupun terang.
