# Vulnerability Report: Laravel Ignition RCE Exposure (CVE-2021-3129)

**Target:** https://yanlik.setda.garutkab.go.id  
**Date:** August 8, 2026  
**Severity:** Critical

## 1. Summary

The Laravel application at `yanlik.setda.garutkab.go.id` exposes the `/_ignition/execute-solution` endpoint. This endpoint is associated with CVE-2021-3129, a critical unauthenticated remote code execution vulnerability affecting vulnerable versions of the `facade/ignition` package (up to 2.5.1).

During testing, the endpoint was confirmed to be active and processing attacker-supplied JSON payloads. When supplied with a PHP stream wrapper (`phar://`) in the `viewFile` parameter, the server returned HTTP 502 Bad Gateway. This behavior is consistent with the vulnerable processing path being reachable; however, the 502 response alone does not independently prove successful PHAR deserialization or arbitrary code execution.

## 2. Affected Component

- Software: Laravel Framework / Facade Ignition
- CVE: CVE-2021-3129
- Vulnerable Versions: `facade/ignition <= 2.5.1` (Laravel 7.x and 8.x, subject to applicable deployment conditions)

## 3. Reproduction Steps

### Step 1: Verify Endpoint Existence

Send a GET request to the Ignition health-check endpoint.

```bash
curl -s -o /dev/null -w "%{http_code}" https://yanlik.setda.garutkab.go.id/_ignition/health-check
```

**Expected Result:** HTTP 200 OK (or 405 Method Not Allowed depending on routing), indicating that the Ignition handler is present.

### Step 2: Trigger Vulnerable Code Path

Send a POST request to the execute-solution endpoint with the supplied test payload.

```bash
curl -s -X POST https://yanlik.setda.garutkab.go.id/_ignition/execute-solution \
  -H "Content-Type: application/json" \
  -d '{"solution":"Facade\\Ignition\\Solutions\\MakeViewVariableOptionalSolution","parameters":{"variableName":"test","viewFile":"phar://../storage/logs/laravel.log/test.txt"}}'
```

**Observed Result:** HTTP 502 Bad Gateway from Nginx.

### Step 3: Analysis of the 502 Response

The response demonstrates that the supplied request reaches backend processing associated with the Ignition solution. It is consistent with the vulnerable code path described by CVE-2021-3129.

The response should not, by itself, be interpreted as proof that PHP-FPM crashed, that PHAR metadata was successfully deserialized, or that arbitrary PHP code executed.

## 4. Impact

If the deployed Ignition version is vulnerable and the relevant exploitation prerequisites are present, an unauthenticated attacker may be able to achieve remote code execution. Potential consequences include:

- Execute arbitrary commands with the privileges of the application process.
- Read sensitive application configuration such as `.env` and database credentials, where accessible.
- Access application data available to the compromised process.
- Potentially reach internal network resources subject to network segmentation and server privileges.
- Establish persistence or modify application-controlled resources, subject to process privileges.

These downstream impacts require additional validation and should not be considered demonstrated solely by the observed 502 response.

## 5. Remediation

1. **Immediate Action:** Update the `facade/ignition` package to version 2.5.2 or later, or upgrade Laravel to a supported release containing the security fix.
2. **Configuration Hardening:** Ensure `APP_DEBUG=false` is enforced in production and reload/restart application workers after configuration changes.
3. **Access Control:** Restrict or disable public access to `/_ignition/*` at the application, web-server, or WAF layer.
4. **Monitoring:** Review historical requests to `/_ignition/*` and suspicious `phar://` inputs, together with abnormal PHP-FPM or HTTP 5xx events.

## 6. References

- CVE-2021-3129: https://nvd.nist.gov/vuln/detail/CVE-2021-3129
- Facade Ignition GitHub Advisory: https://github.com/facade/ignition/security/advisories/GHSA-7vp6-m54v-3xfh
