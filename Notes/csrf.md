# Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery (CSRF) is an attack that forces an end user to execute unwanted actions on a web application in which they're currently authenticated. CSRF attacks specifically target state-changing requests, not theft of data, since the attacker has no way to see the response to the forged request.

## How it Works

1.  **Victim logs in:** The victim logs into a web application, and the application issues a session cookie.
2.  **Attacker crafts a malicious request:** The attacker creates a malicious request that performs an action on the web application (e.g., changing the user's email address, transferring funds). This request is embedded in a link, a form, or an image tag on a website controlled by the attacker.
3.  **Victim visits the attacker's site:** The victim visits the attacker's website.
4.  **Browser sends the malicious request:** The victim's browser automatically includes the session cookie with the malicious request to the web application.
5.  **Application processes the request:** The web application processes the request as if it were a legitimate request from the victim, because it includes the correct session cookie.

## Exploitation in CTFs

In a CTF challenge, you might be asked to craft a CSRF attack to change another user's password, email, or some other setting.

### Example

Let's say a web application allows users to change their email address by sending a POST request to `/change-email` with the new email address in the `email` parameter.

An attacker could create a web page with the following form:

```html
<html>
  <body>
    <form action="https://vulnerable-website.com/change-email" method="POST">
      <input type="hidden" name="email" value="pwned@example.com" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

If a logged-in user visits this page, their browser will automatically submit the form, changing their email address to `pwned@example.com`.

### Identifying CSRF Vulnerabilities

*   **Look for state-changing requests:** Any request that changes data on the server is a potential target for CSRF (e.g., changing passwords, updating profiles, making purchases).
*   **Check for anti-CSRF tokens:** If a request does not require a unique, unpredictable token (often called a CSRF token or synchronizer token), it may be vulnerable to CSRF. These tokens are often sent in a hidden input field or as a custom HTTP header.
*   **Check SameSite cookie attribute:** The `SameSite` cookie attribute can be used to mitigate CSRF attacks. If it's set to `Strict` or `Lax`, it may prevent the attack. If it's set to `None` or not present, the application may be vulnerable.

### Bypassing CSRF Protection

*   **Token is not validated:** Sometimes, the application includes a CSRF token but doesn't actually validate it on the server.
*   **Token is tied to a non-session cookie:** If the CSRF token is tied to a cookie that is not the session cookie, it may be possible to set the cookie on the victim's browser.
*   **Token is predictable:** If the CSRF token is generated in a predictable way, an attacker may be able to guess the token.
*   **Change request method:** Some applications only validate CSRF tokens for POST requests. If you can change the request method to GET, you may be able to bypass the protection.