# Hacking Cheat Sheet

---

## Reconnaissance

### Map Out the Attack Surface

*Parts of the range may be hidden. These techniques will help you find them.*

#### Check `robots.txt`

The `robots.txt` file (in a site's web root) tells well‑behaved web crawlers what parts of the site to ignore. You're not a well‑behaved web crawler, so you can look at those pages — you may find pages the rest of the site doesn't link to.

#### Try some common URLs

By guessing common page and directory names you might discover more content. A tool like `dirbuster` (or `ffuf`, `gobuster`) can help.

#### Look for HTML comments & hidden elements

Search the page source for forms, form fields or links that aren't visible on the page. The CSS rule `display: none;` hides elements — remove the styling to reveal them. Check HTML comments too.

---

### System Fingerprinting

*Identify what components the system is using.*

#### Questions to ask

* Which web server — Apache, nginx, IIS?
* Which web framework — .NET, Django, Struts?
* Which database — MSSQL, MySQL, PostgreSQL, etc.?
* Version numbers for web server and other components — are they up to date?
* How do they handle session management? Did they use a framework or roll their own?

#### Where to look

* **HTTP response headers** — look for `Server` and `X-Powered-By`.
* **Error messages** — look for version info and stack traces.
* **Cookies** — cookie names can reveal framework info. If cookies are homegrown, consider how they're generated and whether they're predictable.

---

### Open Source Intelligence (OSINT)

*Gather information from public sources.*

#### What to look for

* Known vulnerabilities in frameworks/components
* Default credentials
* Employee contact info / personal info

#### How to find it

* Google error messages, cookie names, version headers, password hashes (careful — see note below)
* Read framework/component documentation
* Read framework/component security advisories
* Look up company employees on social media

> **Privacy note:** Your Google searches aren't private. When testing real applications, don't Google password hashes or other highly sensitive information — not even in incognito mode.

---

## Attacks

### Cross-Site Scripting (XSS)

Inject malicious JavaScript into a webpage.

XSS allows an attacker to inject client‑side code (HTML, JavaScript, etc.) into a page so it executes in a victim's browser. XSS appears when user input isn't properly output‑encoded. Types of XSS:

* **Reflected:** script is provided by the caller, included in the server response and executed in the browser (e.g., search term in a URL rendered on the results page).
* **Persistent (Stored):** script is stored in a datastore and served later (e.g., a forum comment).
* **DOM‑based:** script is injected by client‑side JavaScript and never sent to the server (e.g., `example.com/welcome/#name=Bob`).

#### What to look for

Data you provided is reflected back to the page. If it isn't properly encoded, XSS might be present.

Examples:

* Search for `foo` and the page says: “Your search for 'foo' returned no results.”
* Visiting `example.com/fakePage` shows: “Sorry, page "fakePage" not found.”
* Login as `bob` and the error reads: "The user 'bob' does not exist."

#### Discovery test cases

Try inputs where your data is reflected back:

* `<marquee>` — causes content to scroll if not encoded.
* `<plaintext>` — can reveal raw HTML.

#### Exploitation test cases

A flag for XSS is usually opening an alert box. Example payloads:

```html
<script>alert(1)</script>
```

```html
<img src="javascript:alert('xss')"/>
```

```html
<body onload="alert('xss')"/>
```

Event handlers and image `src` techniques can sometimes bypass filters that block `<script>` tags.

---

### SQL Injection (SQLi)

Execute arbitrary SQL commands on the server when user input is concatenated with SQL code.

#### What to look for

Any dynamic page that likely queries a database. Consider what SQL the application might be running (login, search, profile pages, etc.). Example query for login:

```sql
SELECT * FROM Users
WHERE Username = '[user input]'
AND password = '[user input]';
```

#### Special characters to try

* `'` — single quote delimits strings in SQL and is the classic starting point.
* `#`, `--` — comment signs to truncate the rest of the query.
* `;` — statement terminator (for chaining queries, if supported).
* `OR`, `AND` — boolean operators.

#### Test cases

* `'` — a simple test; if it causes a SQL error, you likely found injection.
* `\'` — some DBs escape with backslashes.
* `' OR 1=1#` — forces a `WHERE` clause to be true (useful for bypassing authentication).

---

### Forceful Browsing

Hidden pages may be accessible by just navigating directly to them. Lack of proper access control checks is common.

#### What to look for

Monitor the URL structure while authenticated as different roles. Save URLs you discover for testing with other accounts.

#### Test cases

Try to access authenticated URLs (including query parameters) while logged out or as a lower‑privileged user. If sensitive information appears, access control is broken.

---

### Parameter Tampering

Any client‑controlled parameter (URL, form field, cookie) can be modified. Changing a user ID, price, or other fields might expose or allow modification of data.

#### What to look for

All inputs sent to the server:

* URL parameters
* Dropdowns
* Checkboxes
* Radio buttons
* Hidden form fields
* Cookies

#### Test cases

* Edit URL parameters in the address bar and reload.
* Use browser developer tools to modify form fields (hidden fields included), then submit.

---

### File Upload Abuse

Insecure file uploads can allow attackers to upload executable code, overwrite files, or store malware that later executes.

#### What to look for

Any file upload functionality (profile images, document upload, import features, etc.).

#### Test cases

* Tamper with filenames, paths and extensions.
* Upload scripts or disallowed file types to see whether they can be executed.

---

## More Tools & Resources

* [SQL Injection Cheat Sheet (PentestMonkey)](http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)
* [OWASP XSS Filter Evasion Cheat Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)
* [ASCII to Hex (asciitohex.com)](http://www.asciitohex.com/) — general text encoding/decoding
* [CrackStation](https://crackstation.net/) — online password hash cracker
* [hashcat](https://hashcat.net/hashcat/) — offline password hash cracker

---

*Use this cheat sheet for learning and legal, authorized security testing only.*
