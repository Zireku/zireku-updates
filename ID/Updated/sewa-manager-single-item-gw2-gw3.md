# Penyesuaian Menu Sewa Manager pada Gateway 2 dan Gateway 3

## Ringkasan Perubahan
Menyesuaikan struktur sub-menu di dalam section **Sewa Manager** (`key: 'rental'`) pada Gateway 2 (`wapify2.zireku.com`) dan Gateway 3 (`wapify3.zireku.com`).

Sebelumnya, section Sewa Manager memuat:
1. Grup Sewa (`/dashboard/groups?filter=rental`)
2. Sewa Manager (`/dashboard/group#rental`)
3. Perpanjang Sewa (`/SubscriptionPlan`)
4. Sewa Bot (`/sewabot`)

Karena Gateway 2 dan Gateway 3 difungsikan sebagai Free Gateway cluster tanpa sistem paket langganan berbayar Midtrans VIP, maka item yang relevan di dalam section Sewa Manager disederhanakan menjadi **hanya Sewa Bot (`/sewabot`)**.

## Berkas yang Dimodifikasi
* `nuxt/layouts/default.vue` pada Gateway 2 (`192.154.111.198:62000`)
* `nuxt/layouts/default.vue` pada Gateway 3 (`49.12.82.34:61000`)

## Detail Pembaruan Kode
```javascript
   {
      key: 'rental',
      label: isLogin.value ? t('secRental') : t('rentBot'),
      icon: 'bi bi-calendar-event',
      activeIcon: 'bi bi-calendar-event-fill',
      children: [
         { label: t('rentBot'), icon: 'bi bi-calendar2-week', activeIcon: 'bi bi-calendar2-week-fill', href: '/sewabot' }
      ]
   },
```

## Verifikasi Deployment
* Menjalankan `node scripts/build-safe.js` pada Gateway 2 (exit code 0).
* Menjalankan `node scripts/build-safe.js` pada Gateway 3 (exit code 0).
* Uji status HTTP:
  * `https://wapify2.zireku.com/dashboard` (200 OK)
  * `https://wapify3.zireku.com/dashboard` (200 OK)
  * `https://wapify2.zireku.com/sewabot` (200 OK)
  * `https://wapify3.zireku.com/sewabot` (200 OK)
