# 2026-10-08 上游同步

## 范围

- Hub：合并 monitor-probe/monitor 的 29 个提交，截止 `a7554388b05d369936cec6d88549a4caee4c405a`（1.4.0）。
- 默认主题：合并 monitor-probe/monitor-theme-default 的 11 个提交，截止 `2719bc51bef64f56ed78c5101e17447a529f64e4`（1.4.0），配合新的历史接口和 gzip WebSocket。
- Agent 本次未修改。
- 保留 spfantop 仓库地址、首页隐藏登录入口、后台通用 API Token、既有安装脚本兼容改动。主题目录仍链接上游的公共主题站。

## 兼容处理

上游与旧改版都占用了数据库版本 11：上游用于小时历史，改版用于 API Token。当前迁移版本提升为 13，对两个分支的新增表和字段执行幂等补齐。旧改版数据库及备份升级有回归测试，已有 Token 和节点范围保持不变。

通用 API 的历史查询跟随新保留天数和分层采样，增加 `step`（秒）；节点详情继续按 Token 范围返回，保留 hostname，不返回 IP、Agent Token 或私有备注。

## 发布顺序

1. 先提交、发布改版默认主题 1.4.0。
2. 将工作区 `artifacts/theme-v1.4.0/theme.tar.gz` 和 `theme.tar.gz.sha256` 上传到 spfantop/monitor-theme-default 的 `v1.4.0` Release。当前 Hub 的 `web-theme.pin` 对应的 SHA-256 为：

   `e70f7b10ac455db234ea774d3a8b511f9fbbc269f9ab55183276b0d6ec116ad9`

   如由主题 CI 重新打包，压缩包字节和校验值可能不同，必须先把 `web-theme.pin` 改为实际发布产物的校验值，再构建 Hub；不得禁用校验。
3. 再提交、构建及发布 Hub。主题尚未发布时，全新环境构建无法下载该固定版本；本地验证使用同一压缩包内容和 pin 填充 `target/theme`。
4. 部署前备份 Hub 数据库与配置，再升级 Hub。升级后核对节点上报、历史图表和 API Token 的节点权限。

本次仅作本地提交，不推送或发布 Release。

## 验证与限制

- 管理后台：npm lint、test、build 通过。
- 默认主题：npm lint、build 通过；测试在 TZ=UTC 下通过。Windows 默认时区下原有时间格式测试存在差异。
- Rust：146 项测试通过，包含旧改版数据库/备份升级和 API 数据范围回归测试。
- Rust 测试在 Windows 上临时将 Unix 关机信号监听替换为 Ctrl+C，测试后恢复源码；不是 Linux 发布二进制的运行验证。两个上游测试补充了 SQLite 连接释放，确保 Windows 可以清理文件。
- Linux CI、真实服务升级以及桌面/移动端浏览器视觉检查尚未验证。
- 默认主题 npm audit 报告既有 source-map-js 1.2.1 漏洞 GHSA-68fv-2mgg-jv7q；上游主题此次未修复，未额外升级依赖。
