# Unauthenticated OAuth2 Dynamic Client Registration / Device Authorization

**Target:** `https://accounts.purwakartakab.go.id`  
**Category:** OAuth2 / OpenID Connect  
**Severity:** Critical / Pending Validation  
**Status:** Reported / Pending Validation  

## 1. Summary

The OpenID Connect identity provider at `accounts.purwakartakab.go.id` exposes a Dynamic Client Registration (DCR) endpoint that can be accessed without prior authentication.

During testing, an unauthenticated request was accepted by the registration endpoint and resulted in the creation of a new OAuth2/OIDC client.

The newly registered client was also able to initiate the OAuth2 Device Authorization flow and receive a device authorization response containing a verification URI hosted on the legitimate government identity-provider domain.

This creates a security risk because an unauthorized party can create an OAuth client within the organization's identity infrastructure.

The downstream impact depends on the scopes, privileges, token issuance behavior, and applications that accept tokens issued through this flow.

---

## 2. Affected Components

### Dynamic Client Registration

```text
https://accounts.purwakartakab.go.id/identity/register
```

### Device Authorization

```text
https://accounts.purwakartakab.go.id/identity/device_authorization
```

### Device Verification

```text
https://accounts.purwakartakab.go.id/identity/device
```

### Token Endpoint

```text
https://accounts.purwakartakab.go.id/identity/token
```

---

## 3. Technical Details

### Step 1 — OIDC Discovery

The publicly accessible OIDC discovery document exposes the Dynamic Client Registration endpoint.

```text
https://accounts.purwakartakab.go.id/.well-known/openid-configuration
```

The discovery document identifies:

```text
registration_endpoint:
https://accounts.purwakartakab.go.id/identity/register
```

---

### Step 2 — Unauthenticated Dynamic Client Registration

The Dynamic Client Registration endpoint accepted a registration request without prior authentication.

A sanitized representation of the request is:

```http
POST /identity/register
Host: accounts.purwakartakab.go.id
Content-Type: application/json
```

Example sanitized request body:

```json
{
  "client_name": "Security Audit App",
  "redirect_uris": [
    "http://127.0.0.1/callback"
  ],
  "grant_types": [
    "authorization_code",
    "refresh_token",
    "urn:ietf:params:oauth:grant-type:device_code"
  ],
  "response_types": [
    "code"
  ],
  "scope": "openid profile email [REDACTED]"
}
```

The server responded by creating an OAuth client and returning client information.

Sensitive response values have been removed:

```json
{
  "client_id": "[REDACTED]",
  "client_secret": "[REDACTED]",
  "grant_types": [
    "authorization_code",
    "refresh_token",
    "urn:ietf:params:oauth:grant-type:device_code"
  ],
  "scope": "openid profile email [REDACTED]"
}
```

No active client credentials are included in this public report.

---

## 4. Device Authorization Flow

The newly registered client was subsequently used to access the Device Authorization endpoint.

Sanitized request:

```http
POST /identity/device_authorization
Host: accounts.purwakartakab.go.id
Content-Type: application/x-www-form-urlencoded
```

The resulting response contained device authorization information.

Sensitive values are intentionally redacted:

```json
{
  "device_code": "[REDACTED]",
  "user_code": "[REDACTED]",
  "verification_uri": "https://accounts.purwakartakab.go.id/identity/device",
  "verification_uri_complete": "https://accounts.purwakartakab.go.id/identity/device?user_code=[REDACTED]",
  "expires_in": 600,
  "interval": 5
}
```

The important observation is that the verification URI is hosted on the legitimate government identity-provider domain.

---

## 5. Security Analysis

The observed behavior creates a trust-boundary issue in the identity provider.

An unauthenticated party can create an OAuth client without an established trust relationship with the identity provider.

That client can then participate in the Device Authorization flow.

The resulting verification URL is hosted by the legitimate identity provider rather than by an attacker-controlled domain.

This can make an attacker-controlled OAuth client appear to be a legitimate application participating in the organization's authentication infrastructure.

---

## 6. Confirmed Behavior

The following behavior was demonstrated during testing:

- The OIDC discovery document exposes the registration endpoint.
- The Dynamic Client Registration endpoint accepts unauthenticated registration.
- A new OAuth2/OIDC client can be created.
- The server generates client credentials for the newly created client.
- The newly created client can initiate Device Authorization.
- The server returns a device authorization response.
- The verification URI is hosted on the legitimate government identity-provider domain.

---

## 7. Potential Impact

Depending on token scopes, privileges, consent requirements, and downstream application configuration, an attacker-controlled OAuth client could potentially obtain unauthorized access to protected resources.

Potential impacts may include:

- Unauthorized access to identity information
- Unauthorized access to protected APIs
- Unauthorized access to integrated applications
- Compromise of an account if a victim authorizes the attacker-controlled client and sufficient privileges are granted
- Potential access to downstream applications accepting the issued tokens

### Account Takeover

Account Takeover is considered a **potential impact**, not a confirmed impact in this public report.

The public PoC does not include:

- A real victim account
- A real user's credentials
- A valid access token
- A valid refresh token
- A valid session
- Private user data

Further validation should be performed by the system owner using an authorized test account.

---

## 8. Proof of Concept

The original testing demonstrated the following chain:

```text
Unauthenticated Request
        │
        ▼
Dynamic Client Registration
        │
        ▼
OAuth Client Created
        │
        ▼
Device Authorization
        │
        ▼
Device Verification URI
        │
        ▼
Official Identity Provider Domain
```

Sensitive PoC values have been redacted:

```text
client_id:
[REDACTED]

client_secret:
[REDACTED]

device_code:
[REDACTED]

user_code:
[REDACTED]

access_token:
[REDACTED]

refresh_token:
[REDACTED]
```

No active authentication material is included in this public report.

---

## 9. Impact Validation

The following items should be validated by the affected organization:

1. Whether dynamically registered clients can obtain access tokens after user authorization.
2. Which scopes can be requested by dynamically registered clients.
3. Whether sensitive scopes can be granted.
4. Whether refresh tokens can be issued.
5. Which downstream applications accept tokens issued to dynamically registered clients.
6. Whether the resulting tokens provide access to user or administrative resources.
7. Whether Device Authorization should be available to dynamically registered clients.

---

## 10. Remediation

### 1. Restrict Dynamic Client Registration

Require authentication or an Initial Access Token before allowing a new OAuth client to be registered.

### 2. Restrict Device Authorization

Limit the Device Authorization Grant to trusted and pre-approved clients.

### 3. Implement Client Approval

Require administrative approval or allowlisting for newly registered OAuth clients.

### 4. Restrict OAuth Scopes

Prevent untrusted or dynamically registered clients from requesting sensitive scopes.

### 5. Review Token Privileges

Review the privileges and downstream resources available to tokens issued through dynamically registered clients.

### 6. Revoke Test Credentials

Revoke any OAuth client credentials generated during authorized security testing.

---

## 11. Recommended Vendor Validation

The affected organization should verify the following in a controlled environment:

```text
1. Dynamic Client Registration authentication requirements
2. Allowed OAuth grant types
3. Allowed OAuth scopes
4. Device Authorization eligibility
5. User consent requirements
6. Access-token privileges
7. Refresh-token behavior
8. Downstream application token acceptance
```

Testing should use dedicated test accounts and should not involve real users or unauthorized data.

---

## 12. Public Disclosure Policy

This document is a sanitized public version of the security report.

The following information has intentionally been redacted:

- OAuth client ID
- OAuth client secret
- Registration access token
- Device code
- User code
- Access tokens
- Refresh tokens
- Session information
- Personal information
- Other active authentication material

The target domain and endpoint names remain visible so the vulnerability can be understood and independently reviewed by the system owner.

---

## 13. Disclosure

The finding was reported to the relevant organization for technical validation and remediation.

The final severity and confirmed impact should be determined by the affected organization after reviewing the identity provider's configuration, token privileges, OAuth client trust model, and downstream application integrations.

---

## 14. References

- OAuth 2.0 Dynamic Client Registration
- OAuth 2.0 Device Authorization Grant
- OpenID Connect Discovery
- OpenID Connect Core

---

## 15. Disclaimer

This report documents security research performed for responsible disclosure purposes.

The finding and potential impacts described above are based on the evidence available during testing.

Potential impact must not be interpreted as confirmed exploitation unless independently validated by the affected organization.

No real user account, private user data, active access token, or active refresh token is intentionally disclosed in this public report.
