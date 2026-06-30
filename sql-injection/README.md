<div align="center">

# 💉 SQL Injection

Lab write-ups from [PortSwigger's SQL Injection learning path](https://portswigger.net/web-security/sql-injection).

![Progress](https://img.shields.io/badge/Progress-11%2F16-success)

</div>

---

## 📋 Lab Index

| Lab | Difficulty | Status |
|:--|:--:|:--:|
| [SQLi in WHERE clause — retrieve hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data) | 🟢 Apprentice | ✅ |
| [SQLi login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass) | 🟢 Apprentice | ✅ |
| [Querying DB version — Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle) | 🟡 Practitioner | ✅ |
| [Querying DB version — MySQL & Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft) | 🟡 Practitioner | ✅ |
| [Listing DB contents — non-Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle) | 🟡 Practitioner | ✅ |
| [Listing DB contents — Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle) | 🟡 Practitioner | ✅ |
| [UNION attack — number of columns](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns) | 🟡 Practitioner | ✅ |
| [UNION attack — find text column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text) | 🟡 Practitioner | ✅ |
| [UNION attack — data from other tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables) | 🟡 Practitioner | ✅ |
| [UNION attack — multiple values, single column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column) | 🟡 Practitioner | ✅ |
| [Blind SQLi — conditional responses](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses) | 🟡 Practitioner | ⬜ |
| [Blind SQLi — conditional errors](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors) | 🟡 Practitioner | ⬜ |
| [Visible error-based SQLi](https://portswigger.net/web-security/sql-injection/blind/lab-sql-injection-visible-error-based) | 🟡 Practitioner | ✅ |
| [Blind SQLi — time delays](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays) | 🟡 Practitioner | ✅ |
| [Blind SQLi — time delays + info retrieval](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval) | 🟡 Practitioner | ⬜ |
| [Blind SQLi — out-of-band interaction](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band) | 🟡 Practitioner | ⬜ |
| [Blind SQLi — out-of-band data exfiltration](https://portswigger.net/web-security/sql-injection/blind/lab-out-of-band-data-exfiltration) | 🟡 Practitioner | ⬜ |
| [Filter bypass via XML encoding](https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding) | 🟡 Practitioner | ✅ |

---

## 🧠 What I Learned

### Basic injection & login bypass
User input concatenated directly into a SQL query lets an attacker close out the intended string with a quote and append their own logic. `' OR 1=1--` works because it turns the WHERE clause into something that's always true, returning rows it was never meant to — including bypassing a login check entirely.

### Fingerprinting the database
Oracle, MySQL, and MSSQL expose version info differently. Oracle requires querying from a special table (`FROM dual`) since it doesn't allow `SELECT` without a `FROM`, while MySQL/MSSQL allow direct queries like `@@version`. Knowing the engine determines available syntax, comment style (`--`, `#`), and system tables for the rest of the attack.

### Listing database contents
Before extracting data, the table/column names are unknown — so the real skill is enumerating schema metadata first: `information_schema.tables` / `information_schema.columns` on non-Oracle systems, or `all_tables` / `all_tab_columns` on Oracle. This is what separates "found an injection point" from "can actually pull credentials."

### UNION-based attacks
To pull data from other tables into visible output, the number of columns and their types must match the original query exactly — found via `ORDER BY` or `UNION SELECT NULL,NULL...` trial and error. A text-compatible column is then used to host exfiltrated data, with multiple values smuggled into one column via string concatenation (`CONCAT(username, ':', password)`).

### Visible error-based SQLi
When raw database errors leak to the page, the error message itself becomes an oracle — forcing a type conversion error causes the database to print the queried value inside its own error text. Faster than UNION when column alignment isn't easy.

### Blind SQLi with time delays
With no visible difference and no error output, the only signal left is response time. Conditional delays (`IF(condition, SLEEP(5), 0)`) turn a true/false condition into a measurable timing difference, extracting data one character at a time by stopwatch.

### Filter bypass via XML encoding
Blocklist filters on raw SQL keywords can be defeated by encoding the payload (e.g. XML entity encoding) so it passes as harmless text, then gets decoded back into executable SQL by the backend parser. Confirms blocklist filtering is fundamentally fragile — parameterized queries are the real fix.

### Not yet solved
Conditional response/error-based blind SQLi (inferring true/false from subtle page differences instead of timing) and out-of-band (OAST) techniques (DNS/HTTP callbacks to exfiltrate data when there's no visible, timing, or error channel at all).

---

## ⚠️ Disclaimer

All testing performed exclusively on PortSwigger's intentionally vulnerable lab environments.
