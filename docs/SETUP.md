# 接入说明

1. 将本仓库改为 Private（推荐），邀请第二个 GitHub 账号作为 Collaborator，并确保双方 GitHub 连接器有读写权限。
2. 约定账号 A/B 的 GitHub 用户名，写入 `coordination/participants.json`。
3. 在每个业务仓库的 Settings → Rules → Rulesets（或 Branch protection rules）保护 main：禁止强制推送与删除、要求 Pull Request、要求必要检查通过。规则能否强制执行取决于仓库权限和 GitHub 方案。
4. 两个 GPT 的每次新会话都先读取此仓库 `AGENTS.md`，并在操作前检查锁。
5. 本仓库不会自动向另一个 ChatGPT 账号发送推送通知，也没有后台常驻进程；心跳需要主动调用更新。自动清理与可信 PR 检查尚未部署。

## 待配置
- 第二个 GitHub 用户名
- 需要保护的业务仓库清单
- 仓库私有化
- 目标业务仓库 Ruleset / PR 必需检查
- 自动锁过期清理与受信任的 PR 锁检查

**请勿将真实访问令牌或凭据写入任何仓库文件。**
