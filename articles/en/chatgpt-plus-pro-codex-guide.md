---
title: "ChatGPT Plus, Pro, and Codex: Payments, Errors, and Safety"
description: "Compare ChatGPT Plus, Pro, and Codex payment paths, understand common billing errors, and check account and session safety before ordering."
summary: "Plus and Pro are ChatGPT subscription choices, while Codex usage, Credits, and API billing must be distinguished. mygpt.work supports personal-account subscriptions with CNY pricing and Alipay or WeChat payment, subject to official regional and account eligibility."
publishedAt: "2026-09-06"
updatedAt: "2026-09-06"
author: "mygpt.work Editorial Team"
category: "Subscription Guide"
keywords:
  - "ChatGPT Plus payment"
  - "ChatGPT Pro payment"
  - "Codex top up"
  - "ChatGPT subscription service"
  - "ChatGPT payment declined"
  - "ChatGPT payment error"
  - "Alipay ChatGPT"
  - "WeChat ChatGPT"
---

[简体中文版](https://mygpt.work/blog/chatgpt-plus-pro-codex-guide)

People searching for a ChatGPT Plus payment, a ChatGPT Pro upgrade, or a Codex top-up are usually solving three separate problems: which product they need, how to pay, and how to protect the account while a subscription is processed.

## Quick answer: how to complete a ChatGPT subscription

1. Check the plans, benefits, and prices currently visible on the [official ChatGPT pricing page](https://chatgpt.com/pricing/) and in the customer's own account.
2. Confirm the [countries and territories supported by ChatGPT](https://help.openai.com/en/articles/7947663-chatgpt-supported-countries-and-territories) and the account's eligibility. A third-party CNY payment does not change those requirements.
3. Use the official checkout when an accepted payment method is available. When CNY payment is needed, mygpt.work supports personal-account subscriptions paid with Alipay or WeChat.
4. Treat payment confirmation and subscription delivery as separate states. A successful payment does not by itself prove that the plan is active.
5. Do not substitute a shared or finished account for a subscription on the customer's own account, and do not disclose ChatGPT passwords, payment passwords, or bank-card data to a third party.

## How are ChatGPT Plus, Pro, and Codex related?

Plus and Pro are ChatGPT subscription choices. Benefits, models, usage limits, and availability can change, so the official pricing page and the customer's account are the authority.

Codex is a coding capability with associated usage. It should not automatically be treated as a completely separate “Codex membership.” ChatGPT plan usage, Codex Credits, and OpenAI API billing have different product and billing paths and are not interchangeable. Read [Codex top-up, ChatGPT subscriptions, Credits, and API billing explained](https://mygpt.work/en/blog/codex-recharge-chatgpt-credits-api) for the detailed distinction.

The mygpt.work storefront currently presents Go, Plus, Pro 5x, and Pro 20x service options. Choose by the actual workload, then verify current official benefits instead of inferring features from the label or price alone.

## Which ChatGPT payment path fits the situation?

| Path | When it fits | What to verify first |
| --- | --- | --- |
| Official ChatGPT web checkout | The account is in a supported region and an accepted payment method is available | Official price, account eligibility, billing, and renewal terms |
| Official in-app purchase | The customer uses the official mobile app and the app-store account supports the subscription | Store region, subscription ownership, renewal, and refund terms |
| mygpt.work personal-account subscription | The customer needs CNY pricing and Alipay or WeChat payment | Current price, account eligibility, session scope, and order states |
| Shared or finished account | Multiple people use one account, or ownership starts with another party | Privacy, ownership, and data-access risks; mygpt.work does not sell these accounts |

Not having an overseas credit card does not make an account eligible automatically. A payment method solves how to pay; account availability still depends on region, account state, and current OpenAI rules.

## How does the mygpt.work CNY subscription flow work?

1. Compare the current CNY prices in the [homepage plan section](https://mygpt.work/en/#plans).
2. Use the customer's existing personal ChatGPT account rather than a shared account.
3. Submit the account email and the valid login session required for this order so eligibility can be checked.
4. Confirm the plan, coupon, and locked order amount.
5. Pay with Alipay or WeChat according to the order instructions.
6. Track processing in the original browser. Another browser can obtain read-only access through an OTP sent to the order email.

With confirmed payment and valid authorization, processing normally takes 5–10 minutes and no more than one hour. Time waiting for replacement authorization is outside that window. If an order is confirmed as failed and the subscription was not delivered, a full refund of the actual amount paid is initiated to the original payment method within 24 hours. Settlement timing depends on the payment provider.

## Does a ChatGPT subscription service need the account password?

mygpt.work does not request the ChatGPT password, but password-free processing still requires limited account authorization. The system reads `sessionToken` and the account email from the official ChatGPT session-endpoint JSON for eligibility checks and subscription processing on the current order.

A login session is sensitive. The input is encrypted in the browser, and only the normalized `sessionToken` enters dedicated temporary storage for at most 15 minutes. Durable order records do not contain the session payload. Never send a login session through chat, email, or screenshots. Read [How mygpt.work protects sessions and network requests](https://mygpt.work/en/blog/session-and-network-security) and the [privacy notice](https://mygpt.work/en/privacy) for the full boundary.

## What should I do after `Country not supported`?

This message concerns regional or account availability. Stop before paying and check the official list of supported countries and territories together with the current account state. A third-party payment service should not be treated as a way to bypass regional requirements; mygpt.work CNY payment does not change OpenAI's access rules.

## What should I do after `Your credit card was declined`?

The official payment attempt was not accepted, but that message alone does not identify whether the issuer, payment verification, region, billing information, or a payment-service risk decision caused it. Do not submit repeated charges. Check the official billing page and payment record first, then contact the issuer or OpenAI support when needed.

For a mygpt.work order, the Alipay or WeChat payment state is recorded separately from ChatGPT subscription delivery. Query the original order instead of paying again merely because ChatGPT still displays Free.

## What does `We were unable to authenticate your payment method` mean?

Payment-method authentication was not completed. An unconfirmed payment must not be treated as a successful subscription. Check whether money was actually charged, whether an official receipt exists, and whether the subscription page in the correct account changed before proceeding.

## What should I do after `Something went wrong, please try again later`?

This is a generic error and does not by itself prove whether the account, card, or network caused the failure. Record when and where the error occurred, check the official service and account pages, and retry later. If a mygpt.work order already exists, continue through that order's status or replacement-authorization flow to avoid duplicate orders and payments.

## Why does ChatGPT still show Free after payment?

Separate “payment confirmed” from “subscription completed”:

1. Confirm that the currently signed-in email matches the order email.
2. Query both the payment state and subscription state of the original order.
3. If replacement authorization is requested, update the session on the original order without paying again.
4. If the order is under a manual security review, wait for the official subscription page and billing result to be checked.
5. Treat delivery as complete only when the corresponding subscription is visible on the official plan page of the customer's own account.

## What should I check before using a third-party ChatGPT subscription service?

- **Account ownership:** The subscription should be applied to the customer's own account, rather than replacing it with a shared or finished account.
- **Payment boundary:** Do not send payment passwords, bank-card numbers, or SMS verification codes to the service provider.
- **Authorization boundary:** Ask what session data is needed, what operation it enables, how long it is retained, and when it is deleted.
- **Order evidence:** Payment and fulfillment states should be shown separately, and the order should remain queryable.
- **Failure policy:** Refund triggers, initiation timing, and the original-payment-method scope should be stated before payment.
- **Identity notice:** A third-party service should clearly say that it is not OpenAI and does not represent OpenAI.
- **Risk language:** Be cautious with absolute promises such as “zero risk,” “100% safe,” or “never suspended.”

## Choose a plan or start an order

If the plan is still unclear, read [How to choose ChatGPT Go, Plus, or Pro](https://mygpt.work/en/blog/chatgpt-plan-guide). For CNY payment, visit the [mygpt.work plan section](https://mygpt.work/en/#plans) to review current prices and ordering conditions.

mygpt.work is an independent third-party service. It is not an OpenAI official website and does not represent OpenAI. ChatGPT, OpenAI, Codex, and related names belong to their respective owners.
