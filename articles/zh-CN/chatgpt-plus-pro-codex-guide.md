---
title: "ChatGPT Plus、Pro 与 Codex 充值指南：付款、报错与安全"
description: "一篇看懂 ChatGPT Plus、Pro 与 Codex 的区别、人民币付款流程、常见英文报错，以及使用第三方订阅服务前应核对的安全边界。"
summary: "Plus 和 Pro 是 ChatGPT 订阅选择，Codex 用量与套餐、Credits 或 API 计费需要分别判断。mygpt.work 支持本人账号、人民币报价和支付宝或微信付款；下单前仍须确认官方地区与账号资格，并把付款状态和订阅交付状态分开核对。"
publishedAt: "2026-09-06"
updatedAt: "2026-09-06"
author: "mygpt.work 编辑部"
category: "充值指南"
keywords:
  - "ChatGPT Plus 充值"
  - "ChatGPT Pro 充值"
  - "Codex 充值"
  - "ChatGPT 代充"
  - "支付宝充值 ChatGPT"
  - "微信充值 ChatGPT"
  - "ChatGPT 支付失败"
  - "ChatGPT 订阅安全"
---

[English version](https://mygpt.work/en/blog/chatgpt-plus-pro-codex-guide)

搜索“ChatGPT Plus 充值”“ChatGPT Pro 充值”或“Codex 充值”的用户，往往同时在解决三个问题：该选什么套餐、怎样付款，以及怎样避免把账号交给不透明的渠道。三者需要分开判断。

## 先看结论：怎样完成一次 ChatGPT 订阅

1. 先在 [ChatGPT 官方套餐页面](https://chatgpt.com/pricing/)和本人账号中确认当前可见的套餐、权益与价格。
2. 核对 [ChatGPT 官方支持的国家和地区](https://help.openai.com/en/articles/7947663-chatgpt-supported-countries-and-territories)以及账号资格。第三方人民币付款不会改变这些要求。
3. 能在官方页面直接付款时，可以按官方流程订阅；需要人民币付款时，mygpt.work 提供本人账号的 ChatGPT 订阅服务，支持支付宝和微信。
4. 付款成功只代表收款已确认。账号核验、订阅处理和最终套餐状态仍需单独确认。
5. 不要购买共享账号来替代本人账号订阅，也不要向第三方提供 ChatGPT 密码、支付密码或银行卡资料。

## ChatGPT Plus、Pro 和 Codex 是什么关系

Plus 和 Pro 属于 ChatGPT 的订阅选择。具体权益、模型、用量和开放范围可能变化，应以官方套餐页面及本人账号显示为准。

Codex 是编码能力和相应用量，不应简单理解为一份与 ChatGPT 完全分离的“Codex 会员”。用户还需要区分 ChatGPT 套餐内用量、Codex Credits 和 OpenAI API 余额。它们的入口和计费关系不同，不能把其中一种余额当成另一种使用。详细区别见 [Codex 充值、ChatGPT 套餐、Credits 与 API 的区别](https://mygpt.work/blog/codex-recharge-chatgpt-credits-api)。

mygpt.work 首页展示 Go、Plus、Pro 5x 和 Pro 20x 等服务选项。选择时先按实际使用强度判断，再到官方页面核对当前权益，不要只按名称或价格推断功能。

## 国内用户常见的 ChatGPT 付款方式怎么选

| 方式 | 适合什么情况 | 下单前应核对什么 |
| --- | --- | --- |
| ChatGPT 官方网页订阅 | 账号所在地区受支持，并且已有官方接受的付款方式 | 官方价格、账号资格、账单与续订规则 |
| 官方 App 内购买 | 本人使用官方移动应用，且应用商店账号支持相应订阅 | 商店地区、订阅归属、自动续订和退款规则 |
| mygpt.work 本人账号订阅 | 需要人民币报价并使用支付宝或微信付款 | 当前售价、账号资格、Session 授权范围和订单状态 |
| 共享账号或成品账号 | 多人共用或接手他人注册的账号 | 隐私、账号归属和数据访问风险；mygpt.work 不提供此类账号 |

没有海外信用卡并不等于账号自动符合订阅条件。付款方式解决的是如何支付，账号能否正常使用仍取决于所在地区、账号状态及 OpenAI 当前规则。

## mygpt.work 的人民币订阅流程

1. 在[首页套餐区](https://mygpt.work/#plans)比较当前人民币售价。
2. 选择本人正在使用的 ChatGPT 账号，不购买或提交共享账号。
3. 提交账号邮箱及当前订单所需的有效登录 Session，先完成资格核验。
4. 核对套餐、优惠券和锁定后的实际付款金额。
5. 根据订单说明使用支付宝或微信付款。
6. 在原浏览器查询处理状态；换浏览器时，可用下单邮箱验证码获得只读查询权限。

付款确认且授权有效后，通常 5～10 分钟完成，最迟不超过 1 小时；等待补充授权的时间不计入处理时限。确认充值失败且未完成订阅交付时，将在确认失败后 24 小时内按实际付款金额原路发起全额退款，到账时间以支付渠道为准。

## ChatGPT 代充需要提供账号密码吗

mygpt.work 不要求 ChatGPT 密码，但“免密码”不等于完全不需要账号授权。系统需要从 ChatGPT 官方会话接口 JSON 中读取当前账号的 `sessionToken` 和邮箱，用于本次订单的账号核验与订阅处理。

登录 Session 属于敏感凭证。输入会在浏览器中加密，规范化后的 `sessionToken` 进入独立临时存储，最长保留 15 分钟；持久订单记录不保存 Session 内容。不要通过聊天、邮件或截图把 Session 发给他人。完整边界见 [Session 与网络安全设计](https://mygpt.work/blog/session-and-network-security)和[隐私说明](https://mygpt.work/privacy)。

## 出现 `Country not supported` 怎么办

这个提示与地区或账号可用性有关。先停止付款，核对官方支持的国家和地区以及本人账号当前状态。不要把第三方付款当成绕过地区限制的方法；mygpt.work 的人民币付款不会改变 OpenAI 的地区要求。

## 出现 `Your credit card was declined` 怎么办

这表示官方付款尝试没有被接受，但仅凭这一句话无法确定是发卡行、付款验证、地区、账单信息还是支付服务的风险判断。不要连续重复扣款。应先检查官方账单页面和付款记录，必要时联系发卡行或 OpenAI 支持。

如果是在 mygpt.work 下单，支付宝或微信的付款状态与 ChatGPT 官方订阅交付状态分开记录。请查询原订单，不要因为 ChatGPT 页面仍显示 Free 就立即再次支付。

## 出现 `We were unable to authenticate your payment method` 怎么办

这表示付款方式认证没有完成。未经确认的付款不能视为订阅成功。先检查是否实际扣款、是否收到官方收据，以及本人账号的订阅页面是否变化，再决定是否继续处理。

## 出现 `Something went wrong, please try again later` 怎么办

这是通用错误，不能单独证明账号、银行卡或网络中的哪一项有问题。保存错误发生的时间和页面状态，检查官方服务与账号页面，稍后再试。若已经在 mygpt.work 创建订单，应沿用原订单查询或补充授权，避免重复创建和重复付款。

## 已付款但 ChatGPT 仍显示 Free 怎么办

先区分“付款已确认”和“订阅已完成”：

1. 确认当前登录的邮箱与下单邮箱一致。
2. 查询原订单的付款状态和充值状态。
3. 若显示“待补充授权”，在原订单更新 Session，不需要再次付款。
4. 若显示“待人工安全复核”，等待核对官方订阅页面和账单结果。
5. 只有本人账号的官方套餐页面已经显示对应订阅，才能视为最终交付完成。

## 使用第三方 ChatGPT 订阅服务前检查什么

- **账号归属：** 订阅应落在本人账号，不以共享账号或成品账号替代。
- **付款边界：** 不提交支付密码、银行卡号或短信验证码给服务商。
- **授权边界：** 明确需要什么 Session 数据、用于什么操作、保留多久、何时删除。
- **订单证据：** 付款状态与充值状态分开显示，订单可以持续查询。
- **失败处理：** 先写清退款触发条件、发起时限和原路退款范围。
- **身份说明：** 第三方服务应明确说明不属于 OpenAI 官方，也不代表 OpenAI。
- **风险表述：** 对“零风险”“百分之百安全”“绝不会封号”等绝对承诺保持警惕。

## 继续选择套餐或开始下单

还不确定套餐时，先阅读 [ChatGPT Go、Plus 与 Pro 怎么选](https://mygpt.work/blog/chatgpt-plan-guide)。需要人民币付款时，可前往 [mygpt.work 首页套餐区](https://mygpt.work/#plans)查看当前售价和下单条件。

mygpt.work 是独立第三方服务，不是 OpenAI 官方网站，也不代表 OpenAI。ChatGPT、OpenAI、Codex 及相关名称属于其各自权利人。
