# Unauthenticated OAuth2 Dynamic Client Registration

**Target:** `accounts.purwakartakab.go.id`

**Category:** OAuth2 / OpenID Connect

**Status:** Reported

**Severity:** Pending Validation

## Summary

The OIDC identity provider exposes a Dynamic Client Registration endpoint that can be accessed without prior authentication.

An unauthenticated party can register an OAuth2/OIDC client and receive newly generated client credentials.

The newly registered client can also initiate the Device Authorization flow and receive a verification URI hosted on the legitimate identity-provider domain.

## Affected Endpoints

```text
/identity/register
/identity/device_authorization
/identity/device
/identity/token
