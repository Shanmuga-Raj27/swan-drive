# Swan Drive — Security Review & Contribution Guide

Swan Drive is designed to provide secure, multi-tenant cloud storage and AI-powered document exploration. We place high importance on data isolation, user privacy, and robust security practices.

We explicitly welcome **security engineers, security analysts, software developers, and ethical security researchers** to inspect Swan Drive, review its architecture, and help identify potential security weaknesses.

> **Note:** Security contributions are warmly welcomed even if you only identify a potential risk or vulnerability without providing a code fix or pull request.

---

## 🤖 AI Tools & Automated Scanners in Security Reviews

Security contributors and developers are welcome to utilize AI tools, automated scanners, and static analysis assistants to aid their investigations. However, please **use them with caution**:
- Manually verify all potential findings before reporting to avoid false positives.
- Do not paste proprietary or sensitive configurations into unvetted public AI models.
- Ensure automated testing tools do not cause Denial of Service (DoS) or degrade shared services.

---

## 🔍 Suggested Security Review Areas

Below are key areas where community security reviews and independent audits are particularly valuable. These topics are suggested focus areas for analysis and do not imply that vulnerabilities currently exist:

### 1. Authentication & Session Management
- **Password Hashing:** Implementation of Argon2id hashing algorithms and salt parameters.
- **Token Security:** JWT token minting, algorithm verification (`HS256`), expiration lifecycles, and secure client-side storage practices.
- **Two-Step Verification:** Registration OTP delivery, ephemeral cache expiration (TTL), and brute-force mitigation.

### 2. Multi-Tenant Isolation & Access Control
- **Database Scoping:** Ensuring relational queries (files, metadata, chunks) are strictly bound to the authenticated `user_id`.
- **Vector Store Isolation:** Verification that Qdrant vector lookups and point deletions cannot leak or access another tenant's vector points.
- **Cache Isolation:** Ensuring answer cache keys partition strictly across user IDs and revision counters.

### 3. File Upload & Object Storage Security
- **Presigned URLs:** Validation of client-requested presigned PUT and GET URLs (expiry limits, path sanitization).
- **File Validation:** Size boundaries, MIME-type verification, and protection against malicious file paths or file extension spoofing.
- **Document Processing:** Secure parsing of untrusted documents (PDF, DOCX, XLSX, etc.) to prevent parser exploits, memory exhaustion, or Denial of Service.

### 4. API & Network Security
- **Input Validation:** Strict Pydantic schema validation for request payloads and query parameters.
- **Rate Limiting & Abuse Prevention:** Mitigating automated brute-force attacks or resource-heavy RAG query floods.
- **Error Handling:** Preventing internal stack traces, system paths, or infrastructure details from leaking in HTTP error responses.
- **CORS & Headers:** Proper Cross-Origin Resource Sharing configuration and secure HTTP headers.

### 5. AI & Prompt Security
- **Prompt Injection & Untrusted Context:** Ensuring retrieved document chunks injected into prompt contexts are treated as untrusted data to mitigate indirect prompt injection.
- **Citation Integrity:** Validation that generated answers strictly cite authentic document sources and do not fabricate references.

### 6. Dependency & Supply Chain Security
- **Third-Party Libraries:** Identifying known vulnerabilities (CVEs) in Python packages (`requirements.txt`) or Node.js dependencies (`package.json`).

---

## 🛡️ Responsible Disclosure & Testing Rules

When evaluating Swan Drive for security vulnerabilities, please adhere strictly to responsible disclosure principles:

1. **Protect User Privacy:** Never attempt to access, modify, download, or expose data belonging to other users. Conduct all testing exclusively using your own test accounts and test documents.
2. **No Destructive Testing:** Do not perform Denial of Service (DoS) attacks, brute-force floods, or any testing that degrades the availability or performance of shared infrastructure.
3. **Safe Local Testing:** We encourage running and testing the application locally in an isolated development environment whenever possible.
4. **No Public Disclosure Before Remediation:** Do not publicly disclose or publish details of suspected vulnerabilities or exploitable proof-of-concepts before maintainers have had an opportunity to investigate and address the issue.
5. **No Secrets in Reports:** Never publish private credentials, API keys, passwords, or personally identifiable information in public issues, forums, or discussions.

---

## 📝 How to Report a Vulnerability

If you discover a security vulnerability or potential weakness in Swan Drive, please submit a clear, actionable report directly to the maintainer via:

- **Direct Email:** Send your vulnerability details directly to **[rshanmugaraj11@gmail.com](mailto:rshanmugaraj11@gmail.com)** with the subject line `[Swan Drive Security Report] <Brief Title>`.
- **GitHub Private Vulnerability Reporting:** Alternatively, you can use the repository's **Private Vulnerability Reporting** feature under the Security tab (if enabled).

### What to Include in Your Report:
To help us understand and resolve the issue efficiently, please include:
1. **Description:** A concise explanation of the vulnerability or security concern.
2. **Affected Component:** The specific API endpoint, backend service, frontend component, or configuration file involved.
3. **Reproduction Steps:** Step-by-step instructions or a minimal, non-destructive proof-of-concept demonstrating how the issue can be reproduced.
4. **Impact Assessment:** An explanation of the potential risk (e.g., unauthorized data access, privilege escalation, information disclosure).
5. **Suggested Remediation:** Any recommended fixes, configuration updates, or security hardening steps (if known).
6. **Supporting Evidence:** Relevant screenshots, sanitized network logs, or code snippets, ensuring no sensitive credentials or personal data are exposed.

Thank you for helping keep Swan Drive and its community safe!
