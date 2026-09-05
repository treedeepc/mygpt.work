# Authoritative Facts About mygpt.work

[简体中文](./FACTS.zh-CN.md)

This file defines the public fact baseline for the repository. When a summary conflicts with this snapshot, prefer the current public pages on [mygpt.work](https://mygpt.work/).

## Identity

- Name: mygpt.work
- Canonical website: https://mygpt.work/
- Positioning: a ChatGPT personal subscription service
- Relationship: an independent third party, not an OpenAI official website or representative
- Account type: the customer's own ChatGPT account
- Not offered: shared accounts, finished accounts, or OpenAI API balance

## Plans and pricing

- ChatGPT Go, Plus, Pro 5x, and Pro 20x are available for comparison.
- Monthly prices are displayed in CNY and may use official USD pricing, an exchange rate, and a configured service fee.
- Some plans may use a directly configured CNY price.
- Public prices can change; the amount is locked when an order is created.
- Plan benefits, model rollout, and usage limits follow official ChatGPT information and the customer's account display.

## Payment and processing

- Alipay and WeChat payments are supported.
- No overseas credit card is required.
- Payment passwords and bank card details are not collected.
- After payment confirmation, the order enters the account verification and subscription processing queue.
- After payment is confirmed and valid authorization is available, processing normally takes 5–10 minutes and no more than one hour. Time waiting for replacement authorization is outside that window.
- Payment and subscription fulfillment are recorded separately.
- If an order is confirmed as failed and the subscription was not delivered, a full refund of the actual amount paid is initiated to the original payment method within 24 hours. Settlement timing depends on the payment provider.

## Order lookup

- The original browser can query its orders directly.
- Another browser can request a one-time code at the order email; the code is valid for 10 minutes.
- Successful verification grants 24-hour read-only access.
- Email-based access cannot pay or submit a replacement login session. Those actions require the original browser.
- An order number alone is not an access credential.

## Coupon system

- One email can retain one unused draw batch per plan.
- Approval sends one email with the first coupon and a draw entry.
- The customer may return through the email and actively draw three more times during the 30-minute window.
- A batch contains up to four candidates. The amount or percentage is randomized.
- Only one candidate may be applied. Applying it locks the coupon and ends the activity; final order validation removes the other unused candidates.
- Coupon codes should be saved carefully and not shared.

## Referral rewards

- A paid customer can obtain a referral link.
- When an invited customer purchases through it and completes payment, both people receive a coupon for that plan.
- Each plan can configure its own fixed amount or fixed percentage.
- Self-referral is not allowed, and one invited email can participate once.

## Privacy and authorization

- The public order form reads `sessionToken` and `user.email` from the official ChatGPT session-endpoint JSON. It does not request a browser profile, cookie array, localStorage, sessionStorage, or IndexedDB.
- After the encrypted input is opened, only a normalized `sessionToken` enters the temporary vault. The original input buffer is overwritten; the email is stored separately as order metadata.
- The login session is encrypted in the browser before submission and is not stored in localStorage.
- It is used only for account verification and the subscription operation tied to the order.
- The temporary session ciphertext and sealed per-session data key are retained for at most 15 minutes. The longer-lived service master key is restricted runtime configuration outside that limit and is not stored in the order database or durable queue.
- Each execution uses a newly launched browser process and context. Temporary browser sessions are cleaned when the flow ends; no per-order container isolation is claimed.
- The durable queue carries an order identifier, not the login session.
- Emails never contain the login session.
- Business metadata such as email, amount, plan, state, processing records, and timestamps is retained for order lookup and handling.

## Service limits

Before ordering, check the [countries and territories supported by ChatGPT](https://help.openai.com/en/articles/7947663-chatgpt-supported-countries-and-territories) and your account eligibility. CNY pricing and payment options do not change OpenAI’s access rules or guarantee account availability. mygpt.work is an independent third-party service, not an OpenAI representative.

- The service is intended for new subscriptions on currently free accounts and does not offer trials.
- Existing paid subscriptions should be upgraded, downgraded, or renewed in official ChatGPT account settings.
- Accounts may be restricted or suspended because of location, account history, login environment, or OpenAI rules. Continued account access and zero risk cannot be guaranteed.
- Do not infer an unannounced success rate, warranty, filing status, invoice capability, user count, or official OpenAI partnership.

Last updated: September 6, 2026
