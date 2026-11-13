# AI 双账号协作中心 (aixiezuo)

此仓库用于协调两个不同 ChatGPT/GitHub 账号开发多个业务仓库。**本仓库公开；禁止存放令牌、密钥、私人代码、客户资料或敏感任务详情。**

## 工作流程
1. 两个账号分别使用独立 GitHub 身份，且必须都能访问本仓库及目标业务仓库。
2. 每次开始前读取 `AGENTS.md`、`coordination/tasks.json`、`coordination/messages.json` 和 `coordination/locks/`。
3. 在业务仓库使用独立分支，先取得文件锁再编辑；每次提交/PR 前重新确认锁归属。
4. 完成后更新任务和交接信息并释放锁。锁冲突时停止修改，不得覆盖他人工作。
5. **此仓库仅为协作约定，不会自动使两个 ChatGPT 会话互相唤醒或实时通知。**

## 锁协议
`coordination/locks/<repo>/<path>.lock.json` 对应业务仓库中的一个文件；使用 GitHub Contents API 的“仅创建不存在文件”操作竞争同一锁路径。一个成功，其他创建应失败。创建后必须重新读取确认。锁包含 owner、task、issued_at、expires_at、nonce；续期/删除必须提供最新 blob SHA，防止普通覆盖。过期锁需要人工或可信协调程序清理；**不能仅凭时间到期就认为自己获得了锁**。对同一业务文件的锁路径必须完全一致。

GitHub Contents API 不是完整的分布式租约/事务系统；旧持有者仍可能继续修改。真正强制防护需要目标仓库启用分支规则、必需 CI 检查以及受信任的 PR 验证。仅凭本仓库文件无法强制阻止直接 push。

参见 `AGENTS.md` 和 `docs/SETUP.md`。