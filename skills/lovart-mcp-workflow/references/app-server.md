# 原生工具缺失时的高级宿主诊断

只在已配置 Lovart、当前原生工具不可见，且宿主允许诊断时使用。独立 app-server 不是每次生成的必经桥接器。不要绕过审批、读取凭据或把私有配置原样打印出来。

先读取本机 `codex --version` 与 `codex app-server --help`；必要时使用该版本支持的 `generate-json-schema --experimental --out <临时目录>` 获取协议。以实际安装版本为准，不直接复制在线文档的最新字段。

## 版本案例

曾在 Windows／codex-cli 0.153.2 使用独立 `codex app-server --stdio` 诊断工具加载。以下为该版本的 JSONL 顺序示例，不是跨版本固定协议。发送 initialize 后先等待响应，再发送 initialized 和状态查询：

```jsonl
{"id":0,"method":"initialize","params":{"clientInfo":{"name":"lovart_mcp_diagnostics","version":"1.0"},"capabilities":{"experimentalApi":true}}}
{"method":"initialized","params":{}}
{"id":1,"method":"mcpServerStatus/list","params":{"detail":"toolsAndAuthOnly"}}
```

从返回结果筛选 `name=lovart`，检查实际连接错误。`authStatus` 显示 OAuth 只代表授权状态记录，不证明令牌仍可使用。该历史案例中，状态列表保留 OAuth 标记，但连接同时返回 `invalid_grant`；只有后者才是重新授权的明确依据。

## 宿主边界

- 该历史版本的 `ListMcpServerStatusParams` 没有 `serverName` 字段；其他版本可能支持。先检查本机 schema。
- `config/mcpServer/reload` 在该版本中使用 `params=null`。调用前核对宿主支持情况，不假定当前桌面会话一定受影响。
- 独立 app-server 上 reload 成功，不证明桌面主进程或当前对话的工具目录已经刷新。
- `mcpServer/tool/call` 在该版本要求 `server`、`threadId`、`tool` 与 `arguments`。连接诊断通常无需执行它；如确有必要，只调用实际发现且当前任务授权的工具。
- 某些宿主没有 daemon 生命周期命令，不把 `daemon restart` 当作通用恢复方法。
- 本技能不提供本机专用桥接脚本。原生工具恢复后直接使用原生接口，不临时搭建第二条生成链路。

实际认证失效时按 SKILL.md 恢复 OAuth；完成后重新发现工具，并在当前任务确需连接时使用只读接口验证。不要把重新网页登录画布作为诊断的固定前置步骤。

官方依据：[Codex App Server](https://learn.chatgpt.com/docs/app-server)。
