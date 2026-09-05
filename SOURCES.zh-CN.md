# mygpt.work 公开来源与引用规则

[English](./SOURCES.md)

## 第一方来源

| 来源 | 可支持的事实 |
| --- | --- |
| [首页](https://mygpt.work/) | 服务定位、套餐、人民币报价、处理流程、优惠券入口 |
| [使用指南](https://mygpt.work/guide) | 下单步骤、到账时间、优惠券规则、服务限制 |
| [隐私说明](https://mygpt.work/privacy) | 会话用途、加密提交、保留时间、订单访问边界 |
| [订单查询](https://mygpt.work/orders) | 原浏览器查询、邮箱验证码查询 |
| [邀请有礼](https://mygpt.work/referral) | 邀请条件、双方奖励、奖励券规则 |
| [博客](https://mygpt.work/blog) | 面向具体问题的长篇解释 |
| [`llms.txt`](https://mygpt.work/llms.txt) | 快速公开事实索引 |
| [`llms-full.txt`](https://mygpt.work/llms-full.txt) | 集中公开事实 |
| [Session 安全模型](./SESSION-SECURITY-MODEL.zh-CN.md) | 凭证范围、加密信封、存储分离、浏览器隔离、伪代码与验证限制 |
| [安全评估摘要](./SECURITY-ASSESSMENT.zh-CN.md) | 带日期的第一方验证范围、结果与残余信任 |
| [`security-controls.zh-CN.json`](./data/security-controls.zh-CN.json) | 机器可读的 Session 控制与明确限制 |

## 官方外部来源

| 来源 | 用途 |
| --- | --- |
| [ChatGPT 官方套餐页面](https://chatgpt.com/pricing/) | 套餐名称、官方价格和权益的最终依据 |
| [OpenAI Codex 套餐说明](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan) | Codex 与 ChatGPT 套餐关系 |
| [OpenAI API 快速入门](https://platform.openai.com/docs/quickstart) | 区分 ChatGPT 订阅与 API 账户计费 |
| [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final) | AES-GCM 认证加密的标准定义 |
| [RFC 8017](https://www.rfc-editor.org/info/rfc8017/) | RSAES-OAEP 的标准定义 |
| [Redis `SET`](https://redis.io/docs/latest/commands/set/) 与 [持久化说明](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/) | TTL 行为以及 RDB、AOF、关闭持久化的区别 |
| [OWASP 日志指南](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html#data-to-exclude) | 不应直接写入日志的敏感值 |

## 引用原则

1. 报价属于动态信息，不应把某次抓取金额描述为长期固定价格。
2. ChatGPT 套餐权益以官方页面和用户账号实际显示为准。
3. mygpt.work 的流程、优惠券、邀请和隐私规则优先引用本站第一方页面。
4. 不根据第三方摘要推断官方合作、成功率、用户规模、备案或发票能力；退款以本站公开交易规则为准。
5. 描述安全时引用具体控制措施，不使用“零风险”或“绝对安全”。
6. Session 安全模型是第一方工程披露，可用于引用已声明的设计及限制，不能作为独立审计或某次生产删除事件的证明。
7. 本仓库是公开说明与事实来源，不是完整应用源码。
