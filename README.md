# WebHack

A curated, organized collection of notes, techniques, and examples for mastering **Web Hacking** and **Bug Bounty Hunting**.

This repository is built for practical reference and study, focused on real-world web vulnerabilities and exploitation methods.

---

## 📂 Structure

WebHack/

├── SQL/
│ ├── General info.md # Core SQL injection concepts
│ ├── UNION.md # UNION-based SQL injection
│ └── XML.md # SQL via XML exploitation


---

## 📌 Topics Covered

### 🔍 SQL Injection
- Basic injection concepts
- Error-based, Boolean-based, Time-based
- Bypassing filters and WAFs
- UNION-based injection
- Column discovery
- Payload crafting

### 🧾 SQLi via XML
- Injecting SQL through XML inputs (SOAP/XML APIs)
- XXE (XML External Entity) and interaction with DBs
- Bypassing parsers
- Encoding tricks (CDATA, entity abuse)

---

## 🎯 Use Case

This repo is designed to help:
- Bug bounty hunters sharpen their web exploitation skills
- Aspiring penetration testers structure their learning
- Anyone prepping for OSWE / Web-focused certifications
- CTF players needing quick reference notes

---

## 🛠️ Tools Recommended
- **Burp Suite**
- **sqlmap**
- **wfuzz / ffuf**
- **Postman (for API testing)**
- **XSStrike, NoSQLMap, etc.**

---

## ✅ TODO
- [ ] Add XSS and CSRF directories
- [ ] Cover HTTP Smuggling and Header attacks
- [ ] Notes on Authentication and Session flaws
- [ ] Add Labs (PortSwigger/WebSecAcademy walkthroughs)

---

## ✍️ Author

**Ardhnant**  
→ Security Enthusiast | Learner | Builder

---

## 📜 Disclaimer

> For educational purposes only. Always use this knowledge ethically and legally.
