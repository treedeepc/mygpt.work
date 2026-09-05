---
title: "How mygpt.work Protects Sessions and Network Requests"
description: "A technical overview of browser-side session encryption, short-lived server storage, order access controls, API limits, and browser security headers at mygpt.work."
summary: "mygpt.work reads only sessionToken and account email from official session JSON, encrypts the input in the browser, stores only the normalized token in a non-persistent temporary vault, and separates order viewing from privileged actions."
publishedAt: "2026-09-05"
updatedAt: "2026-09-06"
canonical: "https://mygpt.work/privacy"
author: "mygpt.work Editorial Team"
category: "Security"
keywords:
  - "ChatGPT session security"
  - "GPT top-up security"
  - "browser encryption"
  - "mygpt.work privacy"
---

[简体中文](../zh-CN/session-and-network-security.md)

# How mygpt.work Protects Sessions and Network Requests

A ChatGPT login session is a sensitive credential. mygpt.work therefore treats it as short-lived authorization for one subscription order, rather than ordinary profile data. The design combines encrypted transport, browser-side encryption, one-time key intake, limited retention, controlled order access, and restricted browser execution.

This article describes the public security model. It does not document internal endpoints, service topology, data schemas, task orchestration, payment execution, deployment, or key infrastructure.

For a control-by-control disclosure covering the non-persistent ephemeral store, durable-data separation, and what first-party documentation cannot prove, see the [Session Security Model](../../SESSION-SECURITY-MODEL.md).

## Session intake: encryption begins in the browser

The order form reads `sessionToken` and `user.email` from the official ChatGPT session-endpoint JSON. It does not import a browser profile, cookie array, localStorage, sessionStorage, IndexedDB, service-worker data, or extension state. The email becomes order metadata; after decryption, only a normalized `sessionToken` enters the temporary vault.

The production site uses HTTPS. Before session content is submitted, the browser creates a fresh AES-256-GCM data key and encrypts the content locally through the Web Crypto API. GCM is an authenticated-encryption mode standardized in [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final). The browser then wraps that data key with a short-lived RSA-OAEP public key issued for the current visitor; RSAES-OAEP is specified in [RFC 8017](https://www.rfc-editor.org/info/rfc8017/).

```text
shortLivedKey = requestPublicEncryptionKey()
encryptedAuthorization = browserEncrypt(session, shortLivedKey)
submitOverHTTPS(order, encryptedAuthorization)
clearBrowserPlaintext()
```

The intake key is short lived and single use. An expired or previously consumed key is rejected.

## Server-side use: encrypted and time limited

After successful decryption, the server keeps only the normalized authorization in encrypted temporary storage. The temporary session ciphertext and sealed per-session data key expire after no more than 15 minutes, and the workflow can destroy them earlier when processing finishes. The longer-lived service master key is restricted runtime configuration outside that limit and is not stored in the order database, durable queue, or temporary Redis.

```text
authorization = acceptForCurrentOrder(encryptedAuthorization)

try:
    verifyAccountOwnershipAndEligibility(authorization, order)
    runInIsolatedOrderContext(authorization, order)
    verifySubscriptionResult(order)
finally:
    destroyTemporaryAuthorization()
```

Before processing, the service checks that the account identity matches the order and that the account is eligible for the selected subscription. Each execution launches a fresh browser process with a new temporary browser context, both closed after use. This is process-and-context isolation; the service does not claim a separate operating-system container for every order. A risk-control or human-verification challenge stops the automated path instead of being bypassed.

The durable order record keeps business states such as payment and fulfillment progress. It does not turn the short-lived session into a permanent order field. If authorization expires before processing can complete, the customer can provide fresh authorization without paying again.

## Order access is separated by capability

The original ordering browser receives a signed, HttpOnly, SameSite=Strict credential. These attributes follow the controls described in the [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html): HttpOnly prevents JavaScript from reading the cookie, while SameSite restricts cross-site delivery. A customer who changes browsers can request a six-digit code sent to the order email. The code expires after 10 minutes, attempts are limited, and a successful check grants 24-hour read-only access.

Read-only access can display progress but cannot start payment or submit replacement session authorization. An order number alone is not an access credential. The verification request also returns a neutral response before verification, reducing email-address probing.

## Network and browser safeguards

- HTTPS provides an authenticated, encrypted channel between the browser and mygpt.work. The security goals of modern TLS are defined in [RFC 8446](https://www.rfc-editor.org/info/rfc8446/).
- HTTP requests receive a permanent redirect to HTTPS, and the TLS edge negotiates HTTP/2 as specified by [RFC 9113](https://www.rfc-editor.org/rfc/rfc9113).
- A nonce-based Content Security Policy restricts executable scripts, blocks inline script attributes, rejects embedded objects, and prevents other sites from framing the pages. The nonce and `strict-dynamic` behavior follows [Content Security Policy Level 3](https://www.w3.org/TR/CSP3/).
- `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, a same-origin referrer policy, and a restrictive Permissions Policy reduce common browser attack paths. The obsolete `X-XSS-Protection` filter is set to `0`; executable content is governed by CSP instead.
- API payloads are schema validated and size limited before business logic runs.
- Sensitive endpoints have tighter rate limits, including public-key issue, administrator login, email-code requests, code verification, and coupon actions.
- Security events use fingerprints instead of placing raw sensitive values in public output.

## What mygpt.work does not request

mygpt.work does not ask for an Alipay or WeChat payment password, does not collect bank-card details, and does not place login sessions in email. Public documentation contains no customer emails, orders, coupon codes, one-time codes, sessions, administrator passwords, or production configuration.

No online service can promise zero risk. Customers should use only their own account, verify the domain before submitting authorization, keep coupon and order information private, and revoke authorization if anything looks unusual. See the current [privacy and session authorization policy](https://mygpt.work/privacy) for the authoritative boundary.

## Technical references

- [NIST SP 800-38D: Galois/Counter Mode (GCM)](https://csrc.nist.gov/pubs/sp/800/38/d/final)
- [RFC 8017: PKCS #1 v2.2 and RSAES-OAEP](https://www.rfc-editor.org/info/rfc8017/)
- [RFC 8446: TLS 1.3](https://www.rfc-editor.org/info/rfc8446/)
- [RFC 9113: HTTP/2](https://www.rfc-editor.org/rfc/rfc9113)
- [MDN: SubtleCrypto.encrypt()](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt)
- [W3C: Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)

These references explain the underlying standards and recommended controls. Their inclusion does not imply that NIST, IETF, W3C, MDN, or OWASP has audited or endorsed mygpt.work.
