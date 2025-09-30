# Server-Side Request Forgery (SSRF)

Server-Side Request Forgery (SSRF) is a web security vulnerability that allows an attacker to induce the server-side application to make HTTP requests to an arbitrary domain of the attacker's choosing.

In a typical SSRF attack, the attacker might cause the server to make a connection back to itself, or to other web-based services within the organization's infrastructure, or to external third-party systems.

## How it Occurs

SSRF vulnerabilities often arise when a web application makes a request to a remote resource based on user-supplied input. For example, an application might allow a user to specify a URL to fetch an image from, or to make a request to a webhook. If the application does not properly validate the user-supplied URL, an attacker can use it to make requests to internal services.

### Example

Consider a web application that allows users to fetch an image from a URL:
`https://insecure-website.com/fetch-image?url=http://example.com/image.png`

An attacker could change the URL to point to an internal service:
`https://insecure-website.com/fetch-image?url=http://127.0.0.1/admin`

If the application is vulnerable, it will make a request to the `/admin` endpoint on the local machine. This might allow the attacker to access administrative functionality that is not normally exposed to the public.

## Common Payloads and Techniques

### Accessing Internal Services

*   **Localhost:** `127.0.0.1`, `localhost`
*   **Internal IP addresses:** `192.168.0.1`, `10.0.0.1`, etc.
*   **Metadata services:** In cloud environments, it may be possible to access metadata services that contain sensitive information, such as credentials.
    *   **AWS:** `http://169.254.169.254/latest/meta-data/`
    *   **Google Cloud:** `http://169.254.169.254/computeMetadata/v1/`
    *   **Azure:** `http://169.254.169.254/metadata/v1/`

### Using Different Protocols

Sometimes, you can use different protocols to access services that are not running on HTTP.

*   `file:///etc/passwd`
*   `dict://localhost:11211/stats` (to interact with a Memcached server)
*   `gopher://localhost:12345/_...` (to send arbitrary data to a service)

### Bypassing Filters

*   **URL encoding:** `%2F` for `/`, `%3A` for `:`, etc.
*   **IPv6:** `[::]` for `127.0.0.1`
*   **Decimal IP addresses:** `2130706433` for `127.0.0.1`
*   **Subdomains:** `127.0.0.1.xip.io`
*   **Redirects:** Use a URL shortener or a custom redirect to bypass blacklists.

## Finding SSRF Vulnerabilities

*   Look for any functionality that makes a request to a remote resource based on user input.
*   Check for parameters that contain URLs, hostnames, or IP addresses.
*   Test different protocols and IP address formats.
*   Use a tool like Burp Suite to intercept and modify requests.