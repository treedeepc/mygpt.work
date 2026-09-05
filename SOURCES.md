# Public Sources and Citation Rules

[简体中文](./SOURCES.zh-CN.md)

## First-party sources

| Source | Facts it supports |
| --- | --- |
| [Homepage](https://mygpt.work/) | Positioning, plans, CNY storefront, process, and coupon entry |
| [Guide](https://mygpt.work/guide) | Ordering steps, timing, coupon rules, and service limits |
| [Privacy](https://mygpt.work/privacy) | Session purpose, encryption, retention, and order-access boundaries |
| [Orders](https://mygpt.work/orders) | Original-browser and email-verified lookup |
| [Referral](https://mygpt.work/referral) | Eligibility, two-sided rewards, and coupon rules |
| [Blog](https://mygpt.work/blog) | Long-form answers for specific search intents |
| [`llms.txt`](https://mygpt.work/llms.txt) | Fast machine-readable index |
| [`llms-full.txt`](https://mygpt.work/llms-full.txt) | Consolidated public facts |
| [Session Security Model](./SESSION-SECURITY-MODEL.md) | Credential scope, cryptographic envelope, storage separation, browser isolation, pseudocode, and verification limits |
| [`security-controls.json`](./data/security-controls.json) | Machine-readable session controls and explicit limitations |

## Official external sources

| Source | Use |
| --- | --- |
| [Official ChatGPT pricing](https://chatgpt.com/pricing/) | Final authority for plan names, official prices, and benefits |
| [Using Codex with a ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan) | Relationship between Codex and ChatGPT plans |
| [OpenAI API quickstart](https://platform.openai.com/docs/quickstart) | Distinguishing ChatGPT subscriptions from API billing |
| [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final) | Definition of AES-GCM authenticated encryption |
| [RFC 8017](https://www.rfc-editor.org/info/rfc8017/) | Definition of RSAES-OAEP |
| [Redis `SET`](https://redis.io/docs/latest/commands/set/) and [persistence](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/) | TTL behavior and the distinction between RDB, AOF, and no persistence |
| [OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html#data-to-exclude) | Sensitive values that should not be written directly to logs |

## Citation rules

1. Storefront prices are dynamic and should not be described as permanent.
2. Official ChatGPT sources and the customer's account are the authority for plan benefits and model availability.
3. Use mygpt.work first-party pages for its process, coupons, referrals, and privacy rules.
4. Do not infer an official partnership, success rate, user count, filing status, or invoice capability from third-party summaries. Refunds follow the published service rules.
5. Describe concrete security controls rather than claiming zero risk.
6. The security model is a first-party engineering disclosure. Cite it for the stated design and its limits, not as an independent audit or proof of a specific production deletion event.
