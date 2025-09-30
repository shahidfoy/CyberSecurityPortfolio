# Directory Traversal

Directory Traversal (also known as path traversal) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application. This might include application code and data, credentials for back-end systems, and sensitive operating system files.

## Exploitation

An attacker can exploit a directory traversal vulnerability by manipulating variables that reference files with "dot-dot-slash (../)" sequences and its variations or by using absolute file paths. It may be possible to access arbitrary files from the web root directory and other parts of the file system.

For a comprehensive list of payloads, refer to the [directory-traversal-cheatsheet.txt](directory-traversal-cheatsheet.txt) file.

### Example

Let's say an application uses a URL like this to display images:
`https://insecure-website.com/loadImage?filename=image.png`

An attacker could change the URL to:
`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

If the application is vulnerable, it will display the content of the `/etc/passwd` file, which contains a list of users on the server.