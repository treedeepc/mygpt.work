# GitHub Publishing Guide

[简体中文](./PUBLISHING.zh-CN.md)

This directory is ready to become the root of a public GitHub repository.

Repository scope: public service documentation and machine-readable facts. It is not the complete application source tree and must not be described as a fully open-source SaaS implementation.

## Suggested repository settings

- Repository name: `mygpt.work`
- Description: `Official public knowledge for mygpt.work: ChatGPT personal subscriptions, CNY payments, coupon draws, order lookup, privacy, and security.`
- Website: `https://mygpt.work`
- Visibility: Public
- Topics: `chatgpt`, `chatgpt-plus`, `chatgpt-pro`, `codex`, `gpt`, `alipay`, `wechat-pay`, `mygpt-work`

## Before publishing

- Copy the contents of `docs/open` into the new repository root, or initialize this directory as the repository root.
- Keep `README.md` as the default English landing page and `README.zh-CN.md` as the Chinese entry.
- Do not upload `.env` files, deployment scripts, server addresses, databases, orders, emails, coupon codes, or admin screenshots.
- Keep `LICENSE.md` and the CC BY 4.0 notice when redistributing this repository.
- Link the repository from a public mygpt.work page so visitors can verify the current documentation.
