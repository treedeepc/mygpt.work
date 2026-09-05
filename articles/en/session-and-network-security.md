---
title: "How mygpt.work Protects Sessions and Network Requests"
description: "A technical overview of browser-side session encryption, short-lived server storage, order access controls, API limits, and browser security headers at mygpt.work."
summary: "mygpt.work encrypts sensitive ChatGPT session material in the browser, uses a short-lived one-time key exchange, retains encrypted server-side material for no more than 15 minutes, and separates order viewing from privileged actions."
publishedAt: "2026-09-05"
updatedAt: "2026-09-05"
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

## Session intake: encryption begins in the browser

The production site uses HTTPS. Before session content is submitted, the browser creates a fresh AES-256-GCM data key and encrypts the content locally through the Web Crypto API. GCM is an authenticated-encryption mode standardized in [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final). The browser then wraps that data key with a short-lived RSA-OAEP public key issued for the current visitor; RSAES-OAEP is specified in [RFC 8017](https://www.rfc-editor.org/info/rfc8017/).

```text
shortLivedKey = requestPublicEncryptionKey()
encryptedAuthorization = browserEncrypt(session, shortLivedKey)
submitOverHTTPS(order, encryptedAuthorization)
clearBrowserPlaintext()
```

The intake key is short lived and single use. An expired or previously consumed key is rejected.

## Server-side use: encrypted and time limited

After successful decryption, the server keeps the authorization in encrypted temporary storage. The encrypted authorization and associated key material expire after no more than 15 minutes, and the workflow can destroy them earlier when processing finishes.

```text
authorization = acceptForCurrentOrder(encryptedAuthorization)

try:
    verifyAccountOwnershipAndEligibility(authorization, order)
    runInIsolatedOrderContext(authorization, order)
    verifySubscriptionResult(order)
finally:
    destroyTemporaryAuthorization()
```

Before processing, the service checks that the account identity matches the order and that the account is eligible for the selected subscription. Each order runs in an isolated temporary browser context, which is closed after use. A risk-control or human-verification challenge stops the automated path instead of being bypassed.

The durable order record keeps business states such as payment and fulfillment progress. It does not turn the short-lived session into a permanent order field. If authorization expires before processing can complete, the customer can provide fresh authorization without paying again.

## Order access is separated by capability

The original ordering browser receives a signed, HttpOnly, SameSite=Strict credential. These attributes follow the controls described in the [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html): HttpOnly prevents JavaScript from reading the cookie, while SameSite restricts cross-site delivery. A customer who changes browsers can request a six-digit code sent to the order email. The code expires after 10 minutes, attempts are limited, and a successful check grants 24-hour read-only access.

Read-only access can display progress but cannot start payment or submit replacement session authorization. An order number alone is not an access credential. The verification request also returns a neutral response before verification, reducing email-address probing.

## Network and browser safeguards

- HTTPS provides an authenticated, encrypted channel between the browser and mygpt.work. The security goals of modern TLS are defined in [RFC 8446](https://www.rfc-editor.org/info/rfc8446/).
- A nonce-based Content Security Policy restricts executable scripts, blocks inline script attributes, rejects embedded objects, and prevents other sites from framing the pages. The nonce and `strict-dynamic` behavior follows [Content Security Policy Level 3](https://www.w3.org/TR/CSP3/).
- `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, and a same-origin referrer policy reduce common browser attack paths.
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
- [MDN: SubtleCrypto.encrypt()](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt)
- [W3C: Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)

These references explain the underlying standards and recommended controls. Their inclusion does not imply that NIST, IETF, W3C, MDN, or OWASP has audited or endorsed mygpt.work.
