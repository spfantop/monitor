# monitor

文档：[monitor-document.pages.dev](https://monitor-document.pages.dev)，安装、配置、反向代理与主题开发都在这里。

主题站：[monitor-themes.pages.dev](https://monitor-themes.pages.dev)，在线预览各个公开页主题，复制地址即可在面板安装。

## 特性

- 实时监控：秒级实时数据展示
- 轻量高效：Rust 语言构建，低资源占用，极简高效
- 自托管：完全掌控数据隐私，部署简单
- 通知：节点掉线、流量、到期与登录，推送到 Telegram 或自定义 Webhook

## 组成

| 仓库 | 说明 |
|---|---|
| [monitor](https://github.com/spfantop/monitor) | hub：后台、API、公开页宿主 |
| [agent](https://github.com/spfantop/agent) | Linux agent |
| [monitor-theme-default](https://github.com/spfantop/monitor-theme-default) | 内置默认主题 |
| [themes](https://github.com/monitor-probe/themes) | 主题站：收录第三方主题，提供在线预览 |

```
agent (Linux)  ──WebSocket / JSON-RPC 2.0──▶  hub (axum + SQLite)  ──▶  后台 + 状态页
```

## 只读 API Token

管理员可在后台「安全」中创建只读 API Token，并将它限制到全部或指定服务器。完整 Token 只在创建时显示一次；请求必须使用请求头传递：

```http
Authorization: Bearer <api-token>
```

接口：

```text
GET /api/v1/servers
GET /api/v1/servers/:id
GET /api/v1/servers/:id/metrics
```

Token 只允许读取服务器数据，不能代替后台登录，也不能读取节点 Token、管理员信息或后台配置。不要把 Token 放在 URL 参数中。
