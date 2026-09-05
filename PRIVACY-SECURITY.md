# Privacy and Security at mygpt.work

[简体中文](./PRIVACY-SECURITY.zh-CN.md)

mygpt.work explains concrete data boundaries instead of relying on an “absolutely safe” slogan.

## Login sessions

- A login session is a sensitive credential and is used only for account verification and the subscription operation tied to a specific order.
- It is encrypted in the browser before submission and is not written to localStorage.
- Server-side session ciphertext and keys are retained for at most 15 minutes.
- Temporary browser sessions are cleaned when the flow ends.
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

Subscription operations send the required account authorization to ChatGPT. Notification delivery requires the mail service provider to process the recipient address and message; messages do not contain login sessions. The website also uses GoatCounter analytics. Browsers connect to that service, which processes visit and device information according to its [privacy policy](https://www.goatcounter.com/help/privacy). External providers may process information outside the visitor’s country.

Deleting a local session copy does not revoke other ChatGPT logins. If you suspect exposure, review active sessions in the official ChatGPT account security settings.

## Public-data boundary

Public pages, Markdown files, and this repository contain no customer emails, order records, coupon codes, one-time codes, login sessions, admin credentials, or production configuration.

Current data-handling notice: [mygpt.work privacy and session authorization](https://mygpt.work/privacy)
