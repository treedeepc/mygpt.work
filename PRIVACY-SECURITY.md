# Privacy and Security at mygpt.work

[简体中文](./PRIVACY-SECURITY.zh-CN.md)

mygpt.work explains concrete data boundaries instead of relying on an “absolutely safe” slogan.

## Login sessions

- A login session is a sensitive credential and is used only for account verification and the subscription operation tied to a specific order.
- The order form reads `sessionToken` and `user.email` from the official ChatGPT session-endpoint JSON. It does not request a browser profile, cookie array, or browser storage. Only the normalized `sessionToken` enters the temporary vault after decryption.
- It is encrypted in the browser before submission and is not written to localStorage.
- The temporary session ciphertext and sealed per-session data key are retained for at most 15 minutes. The longer-lived service master key is restricted runtime configuration outside that limit and is not stored in the order database or durable queue.
- Each execution starts a fresh browser process and context. Temporary browser sessions are cleaned when the flow ends; no per-order container isolation is claimed.
- Emails never contain login sessions.

## Payment information

- Alipay and WeChat are supported without asking the customer to submit a payment password.
- Bank card information is not collected.
- Payment and fulfillment states are recorded separately, so “paid” is not presented as “subscription complete.”

## Order access

- An HttpOnly credential identifies the original ordering browser.
- A different browser must verify the order email with a code valid for 10 minutes.
- Verification grants 24-hour read-only access and cannot initiate payment or replacement authorization.
- An order number alone cannot authorize access.

## Coupon protection

- A coupon binds to the actual order email when it is first applied.
- One draw batch can apply only one coupon. Application locks the selected candidate, and order submission validates the rule again.
- Unused candidates are removed when the activity ends.
- Customers should store coupon codes privately and avoid sharing them.

## Data retention and external services

The 15-minute limit applies to temporary session credentials. Order email, amount, plan, state, processing records, coupon applications, referral relationships, and delivery records are retained separately for order lookup and handling. The application currently has no uniform automatic deletion period for these business records; clearing browser cookies does not delete server-side records.

Subscription operations send the required account authorization to ChatGPT. Notification delivery requires the mail service provider to process the recipient address and message; messages do not contain login sessions. The website uses Google Analytics and GoatCounter for visit analytics. Browsers connect to those services and submit the visited path, referrer, and technical information about the browser, device, and language. Each provider handles that information under its published terms: [Google Privacy Policy](https://policies.google.com/privacy) and [GoatCounter privacy policy](https://www.goatcounter.com/help/privacy). External providers may process information outside the visitor’s country.

Deleting a local session copy does not revoke other ChatGPT logins. If you suspect exposure, review active sessions in the official ChatGPT account security settings.

## Public-data boundary

Public pages, Markdown files, and this repository contain no customer emails, order records, coupon codes, one-time codes, login sessions, admin credentials, or production configuration.

Current data-handling notice: [mygpt.work privacy and session authorization](https://mygpt.work/privacy)

Technical controls, reference pseudocode, storage separation, and the limits of first-party verification are documented in the [Session Security Model](./SESSION-SECURITY-MODEL.md).
