# mygpt.work Order Tracking: Keep Visibility After Changing Browsers

[简体中文](./ORDER-TRACKING.zh-CN.md)

The uncertainty after payment can matter more than the wait itself. mygpt.work records payment and fulfillment separately and provides two controlled lookup paths.

## Original ordering browser

The browser that created the order can open [Order Lookup](https://mygpt.work/orders) and view its payment and subscription progress directly.

## Another browser or cleared cookies

1. Enter the email used for the order.
2. Receive and enter a six-digit one-time code.
3. The code is valid for 10 minutes.
4. Successful verification grants 24-hour read-only order access.

To reduce email-enumeration risk, the system does not directly reveal whether an email has orders before verification. Code sending uses a 60-second client/server cooldown and server-side frequency limits.

## Permission boundary

- Email verification can read order progress but cannot initiate payment.
- It cannot submit a replacement account authorization.
- Payment and authorization updates require the original browser.
- An order number is not an identity credential.

This design restores visibility after a device change while keeping sensitive order actions tied to the original browser.
