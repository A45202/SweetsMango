🛡️ Government Security Report Tracker

«A public research log documenting security findings identified on Indonesian government websites and reported through responsible disclosure.»

""Responsible Disclosure" (https://img.shields.io/badge/Disclosure-Responsible-blue)" (#-disclosure-principles)
""Scope" (https://img.shields.io/badge/Scope-Indonesian%20Government-green)" (#-scope)
""Reports" (https://img.shields.io/badge/Reports-5-orange)" (#-overview)

---

📊 Overview

Metric| Count
Total Reports| 5
Fixed| 2
Unresolved| 2
Under Verification| 1

Severity Range: Low → Critical

Last Updated: August 2026

---

📋 Reported Findings

🟢 01 — Korlantas Polri – SIM Website

- URL: https://digitalkorlantas.polri.go.id/sim/
- Vulnerability: Broken Link Hijacking (BLH)
- CWE: CWE-610
- Severity: "Low"
- Status: "Fixed"
- Notes: Reported via Gov-CSIRT (BSSN). The issue was subsequently resolved.

---

🟢 02 — Diskominfo Salatiga

- URL: https://diskominfo.salatiga.go.id/
- Vulnerability: Broken Link Hijacking (BLH) – Twitter/X
- CWE: CWE-610
- Severity: "Low"
- Status: "Fixed"
- Notes: Reported directly to the government administrator. The issue was resolved following disclosure.

---

🟠 03 — Diskominfo Kampar & Pemkab Kampar

- URL: https://diskominfosandi.kamparkab.go.id/
- Vulnerability: Broken Link Hijacking (BLH) – Instagram
- CWE: CWE-610
- Severity: "Low"
- Status: "Unresolved"
- Notes: Reported through multiple official channels. No confirmation or response has been received yet.

---

🟠 04 — Diskominfo Lampung Selatan

- URL: https://diskominfo.lampungselatankab.go.id/
- Vulnerability: Broken Link Hijacking (BLH) – Instagram
- CWE: CWE-610
- Severity: "Low"
- Status: "Unresolved"
- Notes: Reported through multiple official channels. No confirmation or response has been received yet.

---

🔴 05 — Pemerintah Kabupaten Garut – Yanlik

- URL: https://yanlik.setda.garutkab.go.id/
- Vulnerability: Laravel Ignition RCE Exposure
- CVE: CVE-2021-3129
- Endpoint: "/_ignition/execute-solution"
- Severity: "Critical"
- Status: "Under Verification"

Notes:

The exposed Laravel Ignition endpoint was observed processing attacker-controlled JSON containing a "phar://" URI. The request resulted in an HTTP 502 Bad Gateway response.

This behavior is consistent with the vulnerable CVE-2021-3129 processing path being reachable.

Arbitrary PHP code execution was not independently verified. Confirmation requires verification of the installed Ignition/Laravel/PHP versions and relevant application configuration.

---

📈 Severity Overview

Critical  █  1
High      █  0
Medium    █  0
Low       ████  4

«Severity is assigned based on the available evidence at the time of reporting and may change following vendor verification.»

---

🔐 Disclosure Principles

All findings documented in this repository are reported under responsible disclosure principles.

Testing is intentionally limited to:

- Non-destructive validation
- Minimal proof-of-concept requests
- No unauthorized persistence
- No data modification
- No credential harvesting
- No denial-of-service testing
- No post-exploitation activity

Sensitive exploitation details are not published when doing so could unnecessarily increase risk to affected systems.

---

🎯 Scope

This tracker focuses on security findings affecting publicly accessible Indonesian government websites and services.

The purpose of this repository is to document responsible security research and remediation status, not to facilitate exploitation.

---

📝 Status Definitions

Status| Meaning
🟢 Fixed| The reported issue has been resolved or mitigated.
🟠 Unresolved| The issue has been reported but remains unresolved or unanswered.
🔴 Under Verification| The finding requires additional confirmation from the affected organization or further technical validation.

---

⚠️ Disclaimer

This repository is a research and disclosure tracker.

All testing is intended to be conducted against publicly accessible systems within a responsible-disclosure context. No attempt is made to gain unauthorized access, obtain sensitive information, disrupt services, or maintain persistence.

Organizations mentioned in this repository are encouraged to verify reported findings independently and apply appropriate remediation.

---

📚 References

- "CVE-2021-3129 — NVD" (https://nvd.nist.gov/vuln/detail/CVE-2021-3129)
- "Facade Ignition Security Advisory" (https://github.com/facade/ignition/security/advisories/GHSA-7vp6-m54v-3xfh)
- "CWE-610 — Externally Controlled Reference to a Resource in Another Sphere" (https://cwe.mitre.org/data/definitions/610.html)
