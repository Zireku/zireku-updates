# Automatic Paid Plan Protection During Server Migration

![Category](https://img.shields.io/badge/Category-Server_Cluster-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Resolved-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Fixed/migrasi-paket-server.md) | [EN (English)](../../EN/Fixed/server-plan-migration.md)

---

> [!IMPORTANT]
> **Notice:** Users migrating bots between servers (such as from Server 1 to Server 3) are now guaranteed to retain their active paid plans (Plus/Pro) and recovery PINs without risk of downgrading to Free.

---

### 1. What was the Issue?
Previously, when users moved their bots between servers, subscription plan records and recovery PINs remained on the source server's local database. When connecting on the target server, bots were temporarily detected as Free plans because the target server lacked the vault records.

---

### 2. What was Fixed?
The cluster migration architecture has been upgraded across all nodes:
* Migration exports now package complete plan vault records, expiry timestamps, and PINs alongside bot data.
* Target servers immediately preserve active paid plans and commit them directly to persistent storage.
* Added a secure cross-cluster communication channel allowing nodes to query and verify recovery plans across all cluster servers automatically.

---

### 3. How does this Affect Users?
* Users can migrate bots between cluster servers with total peace of mind.
* Paid plans (Plus/Pro), response quotas, and expiration dates transfer automatically.
* Self-service PIN and number plan recovery works seamlessly across any server in the cluster.

---

### 4. The Result
All cluster gateways (Gateway 1, Gateway 2, and Gateway 3) now provide seamless, permanent plan continuity for every subscriber.
