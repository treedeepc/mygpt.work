# Security Assessment Summary

[简体中文](./SECURITY-ASSESSMENT.zh-CN.md)

Status: first-party assessment of the September 6, 2026 service revision. This is not an independent certification or penetration-test report.

## Repository and assessment scope

This repository publishes service documentation, machine-readable facts, and a reviewable security model. It is not the complete application source tree. The assessment summarized here covered the delivered browser client, API authorization boundaries, temporary credential vault, durable data schema, email order lookup, back-office sessions, container configuration, reverse proxy, and local synthetic security regressions. It did not disclose production secrets, customer records, payment-channel credentials, or subscription-execution internals.

## Verified controls

| Control | Verification performed | Result |
| --- | --- | --- |
| Browser encryption | Reviewed the delivered client flow before the order request | A fresh AES-256-GCM key encrypts the input; RSA-OAEP-SHA-256 wraps that key with a short-lived public key |
| Credential minimization | Reviewed parsing and temporary-vault input | Only normalized `sessionToken` enters the temporary vault; account email remains separate order metadata |
| One-use intake | Replayed the same encrypted envelope in a synthetic regression | The first consume succeeds; replay is rejected because the intake key is atomically consumed |
| Temporary retention | Exercised expiry and reviewed the session-store write path | Session ciphertext and its sealed data key are written with TTL; expired material cannot be read |
| Order isolation | Used one synthetic browser owner credential against another owner’s order | Order, receipt, and revocation requests were rejected with no cross-owner disclosure |
| Receipt content | Generated a terminal-order receipt while the synthetic secret remained in an isolated vault | The receipt contained order and destruction metadata, not the session token, vault handle, cookie, or browser state |
| Revocation | Revoked a synthetic pre-payment order and immediately attempted to read its vault record | The vault ciphertext and sealed key were deleted before the successful API response completed |
| Request idempotency | Repeated the same payment request identifier, then changed its plan | An identical retry returned the same order; changed input with the same identifier was rejected |
| Email lookup | Reviewed storage and exercised invalid-code attempts | Codes are stored as keyed digests, expire after 10 minutes, are removed after success, and lock after five failed attempts; sending also has server-side cooldown and hourly limits |
| Durable schema | Reviewed PostgreSQL schema and aggregate-only production checks | Durable order tables contain business metadata and no session-token, cookie, ciphertext, wrapped-key, or envelope payload column |
| Logging | Reviewed application error handling, security events, and aggregate-only log checks | Application events use names and irreversible fingerprints; arbitrary server exceptions are not serialized to clients |

## Deployment controls checked

- The session Redis is separate from the persistent job queue, has RDB and AOF disabled, and has no persistent `/data` volume.
- Website, API, and worker containers use read-only root filesystems, restricted writable temporary filesystems, dropped capabilities, and resource limits.
- Public traffic terminates at Nginx; application listeners and data services bind to loopback addresses.
- Back-office access uses revocable server-side sessions. Logout and password rotation invalidate existing application cookies.
- PostgreSQL backups are access-restricted and restored into a disposable database during recovery checks.

## Limits and residual trust

- A privileged infrastructure operator can theoretically inspect live process memory or temporary storage while an authorization is active.
- Possession of both the service master key and a still-live temporary record could recover that session.
- First-party tests and configuration checks do not prove the absence of every defect or replace an independent review of the current production revision.
- The assessment did not test a live payment provider’s webhook signatures, settlement, disputes, or refund execution.
- Deleting the mygpt.work copy does not revoke the corresponding login session at the official service.
- Business records remain separate from temporary session authorization and do not share its 15-minute retention rule.

## Source standards

The control design follows [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final) for GCM, [RFC 8017](https://www.rfc-editor.org/info/rfc8017/) for RSAES-OAEP, Redis documentation for [`SET` expiry](https://redis.io/docs/latest/commands/set/) and [persistence modes](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/), and the [OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html#data-to-exclude) for excluding sensitive values from logs. These organizations have not audited or endorsed mygpt.work.

For the architecture and trust boundaries, see the [Session Security Model](./SESSION-SECURITY-MODEL.md). For customer-facing data handling, see [Privacy and Security](./PRIVACY-SECURITY.md).
