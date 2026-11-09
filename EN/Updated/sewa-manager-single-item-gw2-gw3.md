# Adjustment of Sewa Manager Menu on Gateway 2 and Gateway 3

## Summary of Changes
Streamlined the sub-menu items within the **Sewa Manager** section (`key: 'rental'`) on Gateway 2 (`wapify2.zireku.com`) and Gateway 3 (`wapify3.zireku.com`).

Previously, the Sewa Manager section contained:
1. Rental Groups (`/dashboard/groups?filter=rental`)
2. Rental Manager (`/dashboard/group#rental`)
3. Extend Rental (`/SubscriptionPlan`)
4. Rent Bot (`/sewabot`)

As Gateway 2 and Gateway 3 operate as Free Gateway clusters without the VIP paid subscription flow, the sub-items inside Sewa Manager have been cleaned up to contain **only Sewa Bot (`/sewabot`)**.

## Files Modified
* `nuxt/layouts/default.vue` on Gateway 2 (`192.154.111.198:62000`)
* `nuxt/layouts/default.vue` on Gateway 3 (`49.12.82.34:61000`)

## Code Modification
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

## Verification
* Ran `node scripts/build-safe.js` on Gateway 2 (exit code 0).
* Ran `node scripts/build-safe.js` on Gateway 3 (exit code 0).
* Verified HTTP statuses:
  * `https://wapify2.zireku.com/dashboard` (200 OK)
  * `https://wapify3.zireku.com/dashboard` (200 OK)
  * `https://wapify2.zireku.com/sewabot` (200 OK)
  * `https://wapify3.zireku.com/sewabot` (200 OK)
