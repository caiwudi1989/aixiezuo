# 文件锁协议（GitHub 无服务器模式）

## 锁命名
对业务仓库 `owner/repo` 的路径 `src/app.js`，锁文件为 `coordination/locks/owner/repo/src/app.js.lock.json`。路径大小写、斜杠与文件名须完全一致。禁止 `..` 路径。对目录级锁、文件重命名及跨文件事务必须另行约定。

## 抢锁
1. 先读取目标锁文件；存在则不可抢，即使 expires_at 已过期也不能直接视为释放。
2. 使用 GitHub Contents API 创建该文件（仅允许文件不存在），内容例如：
```json
{"owner":"GPT-A","github_user":"account-a","task_id":"task-001","issued_at":"2026-11-13T00:00:00Z","expires_at":"2026-11-13T00:30:00Z","nonce":"unique-random-token"}
```
3. 创建失败（409/422 等）立即放弃；成功后重新读取文件确认 nonce、owner 与 SHA 一致。
4. 修改文件期间定期使用最新 blob SHA 条件更新 expires_at；失败即停止修改。

## 释放/过期
只有持有者或受信任的维护者可删除锁，删除前再次检查 owner/nonce 和最新 SHA。锁过期并不等于自动删除：需由可信维护者或定时 GitHub Actions 清理，并确认原持有者已停止。清理可能与旧持有者并发，故**过期锁不能单独保证安全**。

## 安全边界
GitHub Contents API 的文件 SHA 检查能避免多数覆盖竞争，但它不是可验证的全局 fencing 服务。无法凭这个协议保证旧持有者不会继续提交。对高可靠性项目，必须在业务仓库启用受保护分支、必需检查、PR 审核和受信任的锁验证服务或工作流。不要把协作 JSON 当成 GitHub 原生分支保护。
