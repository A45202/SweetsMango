# Security Report Status

A tracker for vulnerability research and responsible disclosure reports.

## 2026

| Finding | Category | Severity | Status |
|---|---|---|---|
| Laravel Ignition — CVE-2021-3129 | Potential RCE | Critical / Pending Validation | Reported |
| OAuth2 Dynamic Client Registration / Device Flow | OAuth2 / OIDC | Critical / Pending Validation | Reported |
| Unauthenticated DNS Zone Transfer (AXFR) | DNS Security | High / Pending Validation | Reported |
| Broken Link Hijacking | Web Security | Pending Validation | Reported |

## Status Legend

| Status | Meaning |
|---|---|
| Research | Under investigation |
| Reported | Report submitted to the relevant organization |
| Acknowledged | Organization has acknowledged the report |
| Pending Validation | Awaiting technical validation |
| Fixed | Remediation has been implemented |
| Closed | Report has been closed |

## Notes

- Severity may change after technical validation by the affected organization.
- "Potential RCE" does not indicate confirmed code execution unless independently validated.
- Public reports are sanitized to remove credentials, tokens, private information, and sensitive infrastructure details.
- No active credentials or authentication material should be stored in this repository.

## Reports

- [Laravel Ignition — CVE-2021-3129](./reports/2026/laravel-ignition-cve-2021-3129/report.md)
- [OAuth2 Dynamic Client Registration / Device Flow](./reports/2026/purwakarta-oauth-dcr/report.md)
- [Unauthenticated DNS Zone Transfer](./reports/2026/dns-zone-transfer/report.md)
- [Broken Link Hijacking](./reports/2026/broken-link-hijacking/report.md)
