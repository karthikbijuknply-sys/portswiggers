<div align="center">

# 🔓 Broken Access Control

Lab write-ups from [PortSwigger's Access Control learning path](https://portswigger.net/web-security/access-control).

![Progress](https://img.shields.io/badge/Progress-7%2F13-yellow)

</div>

---

## 📋 Lab Index

| Lab | Difficulty | Status |
|:--|:--:|:--:|
| [Unprotected admin functionality](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality) | 🟢 Apprentice | ✅ |
| [Unprotected admin functionality — unpredictable URL](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url) | 🟢 Apprentice | ✅ |
| [User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter) | 🟢 Apprentice | ⬜ |
| [User role modifiable in user profile](https://portswigger.net/web-security/access-control/lab-user-role-can-be-modified-in-user-profile) | 🟢 Apprentice | ⬜ |
| [User ID controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter) | 🟢 Apprentice | ✅ |
| [User ID controlled, unpredictable user IDs](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids) | 🟢 Apprentice | ✅ |
| [User ID controlled, data leakage in redirect](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect) | 🟢 Apprentice | ✅ |
| [User ID controlled, password disclosure](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure) | 🟢 Apprentice | ✅ |
| [Insecure direct object references](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references) | 🟢 Apprentice | ✅ |
| [URL-based access control circumvented](https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented) | 🟡 Practitioner | ⬜ |
| [Method-based access control circumvented](https://portswigger.net/web-security/access-control/lab-method-based-access-control-can-be-circumvented) | 🟡 Practitioner | ⬜ |
| [Multi-step process — missing check on one step](https://portswigger.net/web-security/access-control/lab-multi-step-process-with-no-access-control-on-one-step) | 🟡 Practitioner | ⬜ |
| [Referer-based access control](https://portswigger.net/web-security/access-control/lab-referer-based-access-control) | 🟡 Practitioner | ⬜ |

---

## 🧠 What I Learned

### Unprotected admin functionality
Some admin panels are reachable simply because nothing checks if the logged-in user is actually an admin — relying on "security through obscurity." Even making the URL unpredictable (found via `robots.txt` or JS files) isn't real access control — it's hiding the door, not locking it. Any secret-looking path is eventually discoverable and should never replace a server-side permission check.

### User ID controlled by request parameter
When an app identifies "which user's data to show" purely from a client-controlled parameter (`?id=123`), changing that value lets one account view another's data — classic **IDOR** (Insecure Direct Object Reference). The variations matter: even with unpredictable IDs (random GUIDs), the flaw persists if an ID leaks elsewhere — like in a redirect URL or another API response. The "data leakage" and "password disclosure" labs showed the backend also returning full account objects, including password fields, instead of only what the requesting user should see.

### Insecure direct object references
The core lesson across all ID-based labs: access control must be enforced based on the **authenticated session**, not client-supplied data. The server should always ask "does this logged-in user own this resource?" — not "does the requested ID look valid?"

### Not yet solved — what's next
- **Role controlled by client-editable parameter/profile field** — trusting any client-supplied value for authorization (hidden `role=user` field, cookie) means it can simply be edited to `role=admin`.
- **URL/method-based circumvention** — access checks that only validate the URL path or HTTP method (blocking `GET /admin` but not `POST`, or not normalizing `/ADMIN` vs `/admin`).
- **Multi-step process flaw** — access checks enforced only on step 1 of a multi-step action, letting an attacker skip straight to a later step.
- **Referer-based access control** — trusting the `Referer` header for authorization is unsafe since it's fully client-controlled and trivially spoofed.

---

## ⚠️ Disclaimer

All testing performed exclusively on PortSwigger's intentionally vulnerable lab environments.
