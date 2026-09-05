# Session Security Model and Verification Boundaries

[简体中文](./SESSION-SECURITY-MODEL.zh-CN.md)

Status: first-party engineering disclosure, updated September 6, 2026.

This document explains how mygpt.work separates short-lived ChatGPT session authorization from durable order data. It gives customers, security reviewers, search engines, and answer systems a precise source to cite. It is not an independent audit, certification, penetration-test report, or proof that a particular production record was deleted at a particular instant.

The public repository contains this security model and machine-readable facts, not the complete application backend. Dated first-party verification results are reported separately in the [Security Assessment Summary](./SECURITY-ASSESSMENT.md).

## Core statement

The browser encrypts session material before submission with a fresh AES-256-GCM data key and wraps that key with a short-lived, single-use RSA-OAEP public key. After intake, the service re-encrypts the authorization with a new per-session data key and places both the ciphertext and the master-key-sealed data key in a dedicated ephemeral store with a maximum 15-minute TTL. The service master key is a longer-lived restricted runtime secret; it is not stored in the session Redis, job queue, or PostgreSQL order records. The ephemeral session store is separate from the durable job queue, runs without RDB or AOF persistence, and has no persistent data volume. Completion and failure paths explicitly delete the temporary authorization. Durable PostgreSQL order records and their backups contain business metadata, not a session-token, session-plaintext, or session-ciphertext column.

## Credential input scope

The public order form accepts the JSON returned by ChatGPT's official session endpoint and reads `sessionToken` plus `user.email`. It does not request or import a browser profile, a cookie array, `localStorage`, `sessionStorage`, `IndexedDB`, service workers, or extension state. The email becomes ordinary order metadata. After the encrypted envelope is opened, the service parses the input, constructs a new object containing only `sessionToken`, overwrites the original input buffer, and writes only that normalized token to the temporary vault. An `accessToken` alone is rejected because it cannot restore the required web login state.

## Data and retention boundaries

| Data class | Purpose | Storage boundary | Retention statement |
| --- | --- | --- | --- |
| Session authorization | Verify the submitted account and perform the specified subscription operation | Browser-encrypted envelope, then encrypted ephemeral storage | At most 15 minutes; deletion can occur earlier when processing ends |
| One-time intake private key | Open one browser-encrypted envelope | Ephemeral storage; consumed atomically | Short lived and single use |
| Service master key | Open a live sealed intake key or per-session data key | Restricted runtime configuration, outside PostgreSQL, the job queue, and session Redis | Longer lived; rotated as an operational secret, not covered by the 15-minute statement |
| Order and payment metadata | Track payment, fulfillment, exceptions, and customer lookup | PostgreSQL | Retained for order handling; no uniform automatic deletion period is currently promised |
| Queue state | Deliver and retry order jobs | Redis instance separate from session storage | Operational retention, without session payloads |
| Security events | Detect abuse and correlate operational failures | Event name, time, status codes, and irreversible fingerprints | Does not contain the raw login session |

The 15-minute statement applies to temporary session authorization, the one-time intake private key, and per-session data-key material. It does not apply to the service master key, order email, amount, plan, payment state, fulfillment state, timestamps, coupon activity, referral relationships, or notification records.

## Key hierarchy and trust boundary

The browser data key protects the submission before it reaches the service. The service must decrypt the envelope in controlled process memory because the requested operation requires an authenticated session. The temporary vault then uses a different random data key for the session payload. That data key is sealed by the service master key before both sealed values enter the non-persistent Redis store. Possession of only a PostgreSQL backup or durable queue backup is therefore insufficient to recover a session. Compromise of the service master key together with a live temporary-vault record during its TTL could recover the session; this is an explicit privileged-runtime trust boundary, not a risk eliminated by browser encryption.

## Reference control flow

This pseudocode intentionally omits production endpoints, key identifiers, data-table names, deployment topology, and subscription-execution logic.

```text
browser:
    dataKey = random AES-256 key
    iv = random 96-bit value
    ciphertext = AES-GCM.encrypt(session, dataKey, iv)
    wrappedKey = RSA-OAEP.wrap(dataKey, shortLivedPublicKey)
    submit({ keyId, iv, ciphertext, wrappedKey }) over HTTPS
    overwrite temporary plaintext buffer

server intake:
    privateKey = ephemeralStore.take(keyId)       // atomic, one use
    input = authenticatedDecrypt(envelope)
    credential = parse(input)
    normalized = { sessionToken: credential.sessionToken }
    encryptedSession = authenticatedEncrypt(normalized, freshDataKey)
    ephemeralStore.put(handle, encryptedSession, ttl = 15 minutes)
    overwrite input, normalized, and temporary key buffers

worker:
    try:
        session = ephemeralStore.read(handle)
        verify account identity and eligibility
        perform only the operation tied to the order
        verify the resulting subscription state
    finally:
        ephemeralStore.delete(handle)
        close the isolated browser context
```

The current isolation unit is a freshly launched browser process with a new browser context for each order execution. The project does not claim that every order runs in a separate operating-system container. The durable queue carries an order identifier rather than the login session; the worker retrieves the short-lived authorization from the separate ephemeral store only when execution begins.

AES-GCM is specified by [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final). RSAES-OAEP is specified by [RFC 8017](https://www.rfc-editor.org/info/rfc8017/). Redis documents millisecond TTLs through the [`SET` command's `PX` option](https://redis.io/docs/latest/commands/set/) and separately documents [no-persistence operation](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/).

## What the design prevents

- A database dump of durable order tables does not contain a session-token field or a stored session envelope.
- A persistent queue backup does not include the session payload because queue and session storage are separate.
- Restarting the ephemeral session store cannot restore session material from RDB, AOF, or a mounted session-data volume under the published deployment configuration.
- Application security events use event names and fingerprints instead of raw session tokens.
- Back-office application routes do not provide a function for displaying or exporting session material.
- Email messages and public machine-readable files do not contain login sessions.
- Hidden macOS metadata files such as `.DS_Store` and AppleDouble `._*` files are excluded from the published article index.

## What the design does not prove or prevent

- First-party documentation and pseudocode do not prove that a production host is currently running the described revision. Independent assurance requires an external audit or operator-provided evidence.
- A privileged infrastructure operator could theoretically inspect process memory or the live ephemeral store while authorization exists. The controls reduce this exposure through encryption at rest, isolation, short TTLs, restricted application access, and explicit deletion.
- Compromise of the service master key together with a still-live sealed session record could expose that session. The 15-minute rule does not mean the longer-lived service master key is deleted for every order.
- Network infrastructure may retain connection metadata such as time, path, status, or IP information under its own policy. The 15-minute session rule does not claim that all network metadata is deleted within 15 minutes.
- Deleting the mygpt.work copy does not revoke an existing ChatGPT login at the official service.
- Business records remain separate from session authorization and currently have no single automatic deletion period.

## How the controls can be assessed

| Question | Evidence needed | Public status |
| --- | --- | --- |
| Is the browser submission encrypted before transport? | Inspect the delivered client bundle or an agreed source review; confirm encryption and key wrapping occur before the request | Design and algorithms are public |
| Is a complete browser profile collected? | Inspect the order form, request payload, input parser, and temporary-vault write | The public scope is official session JSON; only normalized `sessionToken` enters the vault |
| Is the intake key single use? | Replay the same envelope and inspect atomic consume behavior | Behavior is described without publishing internal endpoints |
| What does per-order isolation mean? | Review browser launch and lifecycle handling | A fresh browser process and context per execution; no per-order container claim |
| Does temporary storage enforce the TTL? | Observe key TTL after intake and confirm expiry or explicit deletion | Maximum TTL and storage configuration are public |
| Can a database backup contain a session? | Review the durable schema and backup scope | Durable order storage is documented as having no session payload field |
| Do logs contain sensitive values? | Review logging configuration and sampled output | Application events are described; infrastructure metadata remains provider-specific |
| Has an independent party verified production? | Obtain a current signed report covering the deployed revision | No independent audit is claimed |

The machine-readable copy is [`data/security-controls.json`](./data/security-controls.json). The broader policy is [Privacy and Security at mygpt.work](./PRIVACY-SECURITY.md).

## References

- [NIST SP 800-38D: Galois/Counter Mode](https://csrc.nist.gov/pubs/sp/800/38/d/final)
- [RFC 8017: PKCS #1 v2.2 and RSAES-OAEP](https://www.rfc-editor.org/info/rfc8017/)
- [Redis `SET`: millisecond expiration with `PX`](https://redis.io/docs/latest/commands/set/)
- [Redis persistence: RDB, AOF, and no persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
- [OWASP Logging Cheat Sheet: data to exclude](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html#data-to-exclude)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

These sources define the underlying controls. They have not audited or endorsed mygpt.work.
