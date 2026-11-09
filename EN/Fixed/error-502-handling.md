# Fix for 502 Bad Gateway Error Display on Dashboard & Terminal

![Category](https://img.shields.io/badge/Category-Dashboard-6366f1?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Resolved-22c55e?style=for-the-badge)

---

> Bahasa / Language : [ID (Bahasa Indonesia)](../../ID/Fixed/pesan-error-502.md) | [EN (English)](../../EN/Fixed/error-502-handling.md)

---

> [!IMPORTANT]
> **Notice:** The raw technical 502 error popup when clicking Start or control buttons has been replaced with clear, friendly guidance for users.

---

### 1. What was the Issue?
When the gateway server was restarting or handling busy connection queues, users clicking the Start button or bot controls in the terminal dashboard received a raw technical popup stating [POST] "/action/control": 502. This technical message caused confusion and led users to believe their accounts or bots were broken.

---

### 2. What was Fixed?
The frontend interface has been updated with an intelligent error handler. Raw technical status codes like 502, 503, and 504 are now automatically converted into user-friendly messages explaining that the server is currently processing requests and advising users to wait a few seconds before trying again.

---

### 3. How does this Affect Users?
* Users no longer see confusing technical error codes like 502.
* Clear, calming notifications inform users that the server is actively processing the connection attempt.
* The dashboard user experience is now significantly smoother and more reassuring.

---

### 4. The Result
All action controls across the terminal and dashboard now present clear, human-readable notifications in both Indonesian and English.
