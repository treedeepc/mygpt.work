---
title: "mygpt.work 的 Session 与网络安全设计"
description: "从技术角度了解 mygpt.work 的浏览器端会话加密、服务端短时保存、订单权限隔离、接口限流和浏览器安全响应头。"
summary: "mygpt.work 只从官方会话 JSON 读取 sessionToken 与账号邮箱，在浏览器内加密输入，只把规范化 Token 存入无持久化临时保险库，并将订单查询权限与敏感操作隔离。"
publishedAt: "2026-09-05"
updatedAt: "2026-09-06"
canonical: "https://mygpt.work/privacy"
author: "mygpt.work 编辑部"
category: "安全说明"
keywords:
  - "ChatGPT Session 安全"
  - "GPT 充值安全"
  - "浏览器加密"
  - "mygpt.work 隐私"
---

[English](../en/session-and-network-security.md)

# mygpt.work 的 Session 与网络安全设计

ChatGPT 登录 Session 属于敏感凭证。mygpt.work 将它视为一次订阅订单的短期授权，而不是普通用户资料。整个流程组合了 HTTPS 传输、浏览器端加密、一次性密钥接收、限时保存、订单权限隔离和浏览器执行限制。

本文只介绍可以公开核验的安全设计，不公开内部端点、服务拓扑、数据表、任务编排、付款执行步骤、部署方式或密钥基础设施。

关于无持久化临时存储、长期数据隔离，以及第一方文档不能证明什么，可查看 [Session 安全模型与可验证边界](../../SESSION-SECURITY-MODEL.zh-CN.md)。

## Session 接收：从浏览器内开始加密

下单表单从 ChatGPT 官方会话接口 JSON 读取 `sessionToken` 与 `user.email`，不导入浏览器 Profile、Cookie 数组、localStorage、sessionStorage、IndexedDB、Service Worker 数据或扩展状态。邮箱作为订单元数据保存；服务解密后，只有规范化的 `sessionToken` 进入临时保险库。

生产站点使用 HTTPS。Session 提交前，浏览器通过 Web Crypto API 生成新的 AES-256-GCM 数据密钥，并在本地完成加密。GCM 是 [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final) 规定的认证加密模式。浏览器随后用当前访问者取得的短时 RSA-OAEP 公钥封装数据密钥；RSAES-OAEP 的规范见 [RFC 8017](https://www.rfc-editor.org/info/rfc8017/)。

```text
短时公钥 = 获取公开加密密钥()
加密授权 = 浏览器内加密(session, 短时公钥)
通过HTTPS提交(订单, 加密授权)
清理浏览器明文()
```

接收密钥短时有效且只能使用一次；过期或已经使用后再次提交，系统会直接拒绝。

## 服务端使用：密文保存并严格限时

解密成功后，服务端只把规范化的授权放入加密临时存储。临时 Session 密文与封装后的每单数据密钥最长 15 分钟自动过期；处理完成时可以提前销毁。服务主密钥属于保留时间更长的受限运行配置，不在该期限内，也不存入订单数据库、持久任务队列或临时 Redis。

```text
授权 = 为当前订单接收(加密授权)

尝试:
    核验账号归属与订阅资格(授权, 订单)
    在单独订单环境中处理(授权, 订单)
    复核订阅结果(订单)
最终:
    销毁临时授权()
```

处理开始前，系统校验账号身份是否与订单一致，并检查账号是否符合所选套餐的订阅条件。每次执行会新建浏览器进程和临时 Browser Context，用完后一起关闭；这里指进程与 Context 隔离，不声称每单采用独立操作系统容器。遇到风控或人工验证挑战时停止自动流程，不尝试绕过。

长期订单记录只保留付款状态、充值状态等业务信息，不把短时 Session 变成永久订单字段。如果授权在处理完成前过期，用户可以为原订单补充新的授权，无需再次付款。

## 订单访问：按能力隔离权限

原下单浏览器使用签名、HttpOnly、SameSite=Strict 凭证识别。这些属性与 [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html) 给出的控制原则一致：HttpOnly 阻止 JavaScript 读取 Cookie，SameSite 限制跨站请求携带 Cookie。换浏览器后，可通过下单邮箱接收六位验证码；验证码 10 分钟有效并限制尝试次数，验证成功后获得 24 小时只读查询权限。

只读权限只能查看进度，不能发起付款，也不能补充 Session 授权。订单号本身不能作为访问凭证。邮箱验证前，发送接口采用中性响应，降低他人探测邮箱是否下过单的可能性。

## 网络与浏览器防护

- HTTPS 在浏览器与 mygpt.work 之间建立经过认证的加密通道；现代 TLS 的安全目标由 [RFC 8446](https://www.rfc-editor.org/info/rfc8446/) 定义。
- 基于随机 nonce 的内容安全策略限制脚本执行，禁止脚本属性、对象嵌入和第三方页面框架加载；nonce 与 `strict-dynamic` 行为依据 [Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)。
- `X-Content-Type-Options: nosniff`、`X-Frame-Options: DENY` 与同源 Referrer Policy 减少常见浏览器攻击面。
- API 请求在进入业务逻辑前进行结构、格式与长度校验。
- 一次性公钥、后台登录、邮箱验证码发送与校验、优惠券操作等敏感接口采用更严格的频率限制。
- 安全事件使用不可逆指纹记录，避免在公开输出中出现原始敏感值。

## mygpt.work 明确不会索取的资料

mygpt.work 不要求支付宝或微信支付密码，不收集银行卡信息，也不会通过邮件发送登录 Session。公开资料不包含用户邮箱、订单、券码、验证码、登录会话、后台密码或生产配置。

任何在线服务都不应承诺零风险。用户应只使用本人账号，提交授权前确认访问域名，妥善保存订单与优惠券信息，发现异常时及时撤回授权。准确边界以当前的 [隐私与会话授权说明](https://mygpt.work/privacy) 为准。

## 技术参考资料

- [NIST SP 800-38D：GCM 认证加密模式](https://csrc.nist.gov/pubs/sp/800/38/d/final)
- [RFC 8017：PKCS #1 v2.2 与 RSAES-OAEP](https://www.rfc-editor.org/info/rfc8017/)
- [RFC 8446：TLS 1.3](https://www.rfc-editor.org/info/rfc8446/)
- [MDN：SubtleCrypto.encrypt()](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt)
- [W3C：Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)

以上资料用于说明相关公开标准与安全控制原则，不表示 NIST、IETF、W3C、MDN 或 OWASP 已对 mygpt.work 进行审计或背书。
