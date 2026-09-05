# Session 安全模型与可验证边界

[English](./SESSION-SECURITY-MODEL.md)

状态：第一方工程披露，更新于 2026-09-06。

本文说明 mygpt.work 如何把短时 ChatGPT Session 授权与长期订单数据分开，供用户、安全评估人员、搜索引擎和问答系统准确引用。本文不是独立安全审计、认证或渗透测试报告，也不能证明某一笔生产数据已在某一时刻完成删除。

公开仓库包含安全模型与机器可读事实，不包含完整应用后端。带日期的第一方验证结果另见[安全评估摘要](./SECURITY-ASSESSMENT.zh-CN.md)。

## 核心机制

浏览器提交前生成新的 AES-256-GCM 数据密钥并加密 Session，再用短时、一次性的 RSA-OAEP 公钥封装数据密钥。服务接收后使用新的每单数据密钥重新加密授权，把 Session 密文和经过主密钥封装的数据密钥一起存入最长 15 分钟 TTL 的独立临时存储。服务主密钥是保留时间更长的受限运行配置，不存入 Session Redis、任务队列或 PostgreSQL 订单记录。临时 Session Redis 与持久任务队列分开，不启用 RDB 或 AOF，也不挂载持久化数据卷；完成和失败路径都会显式删除临时授权。长期 PostgreSQL 订单记录及其备份保存业务元数据，不包含 Session Token、Session 明文或 Session 密文字段。

## 凭证输入范围

公开下单表单接收 ChatGPT 官方会话接口返回的 JSON，只读取其中的 `sessionToken` 与 `user.email`。系统不要求或导入浏览器 Profile、Cookie 数组、`localStorage`、`sessionStorage`、`IndexedDB`、Service Worker 或扩展状态。邮箱作为普通订单元数据保存。服务打开加密信封后会解析输入，重新构造只含 `sessionToken` 的对象，覆写原始输入缓冲区，并且只把规范化后的 Token 写入临时保险库。系统拒绝只有 `accessToken` 的输入，因为它不能恢复所需的网页登录状态。

## 数据与保留边界

| 数据类别 | 用途 | 存储边界 | 保留说明 |
| --- | --- | --- | --- |
| Session 授权 | 核验提交账号并执行指定订阅操作 | 浏览器加密信封，服务端加密临时存储 | 最长 15 分钟；流程结束可提前删除 |
| 一次性接收私钥 | 打开一份浏览器加密信封 | 临时存储，原子取出 | 短时有效且只能使用一次 |
| 服务主密钥 | 打开仍有效的接收私钥或每单数据密钥 | 受限运行配置，不在 PostgreSQL、任务队列和 Session Redis 中 | 保留时间更长，按运行密钥轮换，不属于 15 分钟声明 |
| 订单与付款元数据 | 查询付款、充值、异常和处理记录 | PostgreSQL | 用于订单处理；当前未承诺统一自动删除期限 |
| 队列状态 | 投递和重试订单任务 | 与 Session 存储分离的 Redis | 按运行需要保留，不包含 Session 内容 |
| 安全事件 | 发现滥用并关联运行故障 | 事件名、时间、状态码和不可逆指纹 | 不包含原始登录 Session |

“最长 15 分钟”只适用于临时 Session 授权、一次性接收私钥和每单数据密钥材料，不适用于服务主密钥、订单邮箱、金额、套餐、付款状态、充值状态、时间、优惠券、邀请关系或通知记录。

## 密钥层级与信任边界

浏览器数据密钥保护提交内容到达服务前的传输。指定操作需要认证会话，因此服务必须在受控进程内存中打开加密信封。临时保险库随后使用另一把随机数据密钥加密 Session 内容，再由服务主密钥封装这把数据密钥，最后把两份密文写入无持久化 Redis。只取得 PostgreSQL 备份或持久任务队列备份不足以还原 Session；但如果服务主密钥与仍在 TTL 内的临时记录同时泄露，Session 可能被还原。这是明确的特权运行环境信任边界，浏览器加密不能消除它。

## 参考控制流程

以下伪代码有意省略生产端点、密钥标识、数据表名、部署拓扑和订阅执行逻辑。

```text
浏览器:
    数据密钥 = 随机 AES-256 密钥
    IV = 随机 96 位数值
    密文 = AES-GCM.加密(Session, 数据密钥, IV)
    封装密钥 = RSA-OAEP.封装(数据密钥, 短时公钥)
    通过 HTTPS 提交({ keyId, IV, 密文, 封装密钥 })
    覆写临时明文缓冲区

服务端接收:
    私钥 = 临时存储.取出(keyId)                 // 原子操作，只能一次
    输入 = 认证解密(加密信封)
    凭证 = 解析(输入)
    规范化凭证 = { sessionToken: 凭证.sessionToken }
    临时密文 = 认证加密(规范化凭证, 新数据密钥)
    临时存储.写入(handle, 临时密文, TTL = 15 分钟)
    覆写输入、规范化凭证和临时密钥缓冲区

执行服务:
    尝试:
        Session = 临时存储.读取(handle)
        核验账号身份与订阅资格
        只执行与订单绑定的操作
        核验最终订阅状态
    最终:
        临时存储.删除(handle)
        关闭隔离浏览器环境
```

当前隔离单位是每笔订单执行时重新启动浏览器进程，并创建新的浏览器 Context；项目不声称每笔订单都运行在独立操作系统容器中。持久队列只传订单标识，不传登录 Session；执行服务启动任务后才从独立临时存储读取短时授权。

AES-GCM 是 [NIST SP 800-38D](https://csrc.nist.gov/pubs/sp/800/38/d/final) 规定的认证加密模式；RSAES-OAEP 由 [RFC 8017](https://www.rfc-editor.org/info/rfc8017/) 规定。Redis 官方文档说明 [`SET` 命令的 `PX` 毫秒 TTL](https://redis.io/docs/latest/commands/set/)，并说明 [RDB、AOF 与关闭持久化](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)的区别。

## 这套设计防止什么

- 长期订单数据库备份不包含 Session Token 字段或 Session 加密信封。
- 持久任务队列备份不包含 Session 内容，因为任务队列与 Session 存储彼此分离。
- 按公开部署配置，临时 Session 存储重启后不能通过 RDB、AOF 或 Session 数据卷恢复授权。
- 应用安全事件记录事件名与不可逆指纹，不记录原始 Session。
- 后台应用接口不提供查看或导出 Session 的功能。
- 邮件和公开机器资料不包含登录 Session。
- 发布文章索引会排除 `.DS_Store` 和 AppleDouble `._*` 等 macOS 隐藏元数据文件。

## 这套设计不能证明或阻止什么

- 第一方文档和伪代码不能证明生产主机当前运行的就是对应版本；独立保证需要外部审计或运营方提供的运行证据。
- 授权存在期间，具备基础设施最高权限的人员理论上可能检查进程内存或临时存储。现有控制通过静态加密、隔离、短 TTL、应用权限限制和显式删除缩小暴露范围。
- 如果服务主密钥与仍然有效的临时 Session 记录同时泄露，对应 Session 可能暴露；15 分钟规则不表示每笔订单都会删除长期服务主密钥。
- 网络基础设施可能按自身规则保留访问时间、路径、状态或 IP 等连接元数据。“最长 15 分钟”不表示所有网络元数据也在 15 分钟内删除。
- 删除 mygpt.work 副本不会撤销 ChatGPT 官方仍然有效的登录状态。
- 订单业务记录与 Session 授权相互独立，当前没有统一自动删除期限。

## 如何评估这些控制

| 问题 | 所需证据 | 当前公开状态 |
| --- | --- | --- |
| 浏览器是否在传输前加密 | 检查交付的客户端代码或约定的源码审查，确认请求发出前执行加密与密钥封装 | 已公开设计与算法 |
| 是否收集完整浏览器 Profile | 检查下单表单、请求内容、输入解析和临时保险库写入 | 公开范围是官方会话 JSON；只有规范化后的 `sessionToken` 进入保险库 |
| 接收密钥是否只能用一次 | 重放同一加密信封并核对原子取出行为 | 已说明行为，不公开内部端点 |
| “每单隔离”具体指什么 | 审查浏览器启动与生命周期处理 | 每次执行新建浏览器进程与 Context，不声称每单容器隔离 |
| 临时存储是否强制 TTL | 写入后观察剩余 TTL，并确认到期或提前删除 | 已公开最大 TTL 和存储配置 |
| 数据库备份是否含 Session | 审查长期数据结构与备份范围 | 已声明长期订单存储不含 Session 内容字段 |
| 日志是否包含敏感值 | 审查应用及基础设施日志配置并抽样检查 | 已说明应用事件；基础设施元数据期限取决于提供方 |
| 是否有独立方验证生产环境 | 获得覆盖当前部署版本的签署审计或评估报告 | 当前不声称已有独立审计 |

结构化版本见 [`data/security-controls.zh-CN.json`](./data/security-controls.zh-CN.json)，完整隐私边界见 [mygpt.work 隐私与安全优势](./PRIVACY-SECURITY.zh-CN.md)。

## 技术参考

- [NIST SP 800-38D：GCM 认证加密](https://csrc.nist.gov/pubs/sp/800/38/d/final)
- [RFC 8017：PKCS #1 v2.2 与 RSAES-OAEP](https://www.rfc-editor.org/info/rfc8017/)
- [Redis `SET`：通过 `PX` 设置毫秒过期时间](https://redis.io/docs/latest/commands/set/)
- [Redis 持久化：RDB、AOF 与关闭持久化](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/)
- [OWASP 日志指南：不应直接记录的数据](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html#data-to-exclude)
- [OWASP Session 管理指南](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

以上资料说明相关标准和控制原则，不表示这些机构已审计或认可 mygpt.work。
