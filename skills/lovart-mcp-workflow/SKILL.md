---
name: lovart-mcp-workflow
description: Use Codex with Lovart MCP to generate and iteratively improve Midjourney images, including reference images and sref, prompt and parameter planning, candidate evaluation, refinement, result delivery, and connection or generation troubleshooting.
---

# Midjourney 生图与优化工作流｜Codex + Lovart MCP

通过Lovart MCP调用Midjourney，完成参考图准备、画风探索、候选比较、按反馈优化及结果交付。适用于插画、角色、概念图和其他用户指定的图像任务；不预设某种画风、某个项目或固定生成数量。这里不是直连Midjourney官方API；精确编辑可在用户授权下使用适合的其他模型，并单独记录来源。

## 先明确本轮任务

- 读取当前项目已有的上下文、参考图与任务记录（如manifest）；确认项目ID、目标、选定模型、调用预算、待完成任务及用户已经作出的选择。
- 用户已经授权的步骤直接继续；不要把对话中断当作重新提交生成的授权。缺少预算等关键边界时先明确最小试验范围，不自行批量扩展。
- 用户要求等待、只总结经验或只编写提示词时，不触发生成。只有当前任务包含连接验证或实际生成时，才做必要的账户检查。
- 将连接可用、任务被接收、任务完成、画面达标与用户采用分别记录。只读接口成功不能证明某个模型生成故障已恢复。

## 正常生成与优化路径

实际执行时读取[Midjourney提交、回收与优化](references/midjourney.md)，只做与本轮目标相关的阶段：

1. **检查现有接入。** 优先发现原生Lovart工具；工具可用时用get_credits等只读接口确认本次账户访问。网页登录和computer-use不是默认生图入口。
2. **核对模型与参考。** 用list_models解析Midjourney路由，再describe_model读取参数；外层model是Lovart路由，params.model是Midjourney版本。图像／造型输入与sref画风输入分开记录，sref不保证身份锁定。
3. **准备并提交。** 复用用户指定project_id。外部／本地参考先经upload_asset获得Lovart URL，已托管的同一文件可复用。完整请求与调用序号先登记，返回后立即保存task_ids；失败也计入预算。
4. **回收全部结果。** pending按poll_after_seconds等待，需要内部查询时用get_task_status；终态不循环查询。生成回合按工具规则用一次get_task展示本轮全部任务ID，需要看图则include_images=true。
5. **检查与比较。** 实际查看候选，按用户目标检查构图、形体、细节、画风及使用尺寸。需要筛选时按工具规则使用rank_candidates_with_jev，描述实际画面；排名不能代替用户采用。保留全部候选。
6. **按反馈优化。** 整体方向偏差在授权预算内继续Midjourney探索；选定形体后的局部修改使用已授权编辑工具。每次写清改变项与保留特征，编辑后与源图对照。材质延展、抠图、放大和导出分别核验，不覆盖原件。

参数使用当前schema报告的名称；prompt、aspect_ratio等已有顶层字段不在params重复。历史成功参数只可作为例子，不是通用最佳值。未逐项回显的参数标为“已请求、未证实”。n和实际产物数分开统计，按账户concurrency_limit安排并发，不硬编码。

## 连接异常时按证据处理

| 观察结果 | 处理 |
| --- | --- |
| 原生只读接口成功 | 继续授权任务，不重复登录 |
| 工具目录没有Lovart | 核查现有配置和宿主加载状态，不先要求网页登录 |
| CLI显示Auth Unknown或OAuth标记 | 仅作状态线索；Unknown不等于未授权，OAuth标记不等于令牌有效 |
| 实际连接返回invalid_grant或刷新令牌被拒绝 | 使用MCP OAuth恢复，再验证只读接口 |
| 认证有效但工具未加载 | 检查宿主支持的状态／重载方式；必要时读取[高级宿主诊断](references/app-server.md) |
| 任务已提交后failed／API_CALL_ERROR | 记录为生成失败；该错误本身不是重登录依据 |

配置检查只输出服务名、启用状态、传输类型与URL；完整JSON在内部解析，不打印认证头、令牌或其他私有字段：

```powershell
codex mcp list
$lovartConfig = codex mcp get lovart --json | ConvertFrom-Json
[pscustomobject]@{
  name = $lovartConfig.name
  enabled = $lovartConfig.enabled
  transport = $lovartConfig.transport.type
  url = $lovartConfig.transport.url
}
```

只有配置确实缺失且配置工作已获授权时，才添加已知接入：

```powershell
codex mcp add lovart --url https://lgw.lovart.ai/v1/openapi/mcp/widgets --oauth-client-id codex-lovart
```

只有明确认证失败才恢复OAuth：

```powershell
codex mcp login lovart
```

复用实际生效配置，不默认logout、remove或重复add。OAuth成功后重新发现原生工具并只读验证，不能预先要求重启整个应用。浏览器仅用于必要的授权；网页登录与MCP授权分别处理，不复制cookies、令牌或含授权码的回调URL进记录。凭据由本机Codex管理，不随技能分发。

## 失败停止与交付

- 超时、响应丢失或本地记录保存失败时先核对原任务，状态未知不等于生成失败，不重复提交。若已取得任务ID，先保留原响应并修复记录，再查询该ID。completed、failed、moderated等终态只读存档；最后的get_task展示调用不作为循环轮询。
- API_CALL_ERROR缺少详情时保持原因未知。仍有授权预算且有具体依据，才做至多一次受控重试；同错重复后停止计费尝试。不根据通用错误武断归因于HD、iw或提示词长度。
- 用户拒绝整批时留档且不自动扩展；用户指定保留Midjourney等待时不切模型，也不安排后台重试。用户最新限制优先于历史泛泛授权。
- 保存完整请求、参考分工、模型路由、实际任务与产物、参数回显情况、用户采用状态和续作点。记录模型／工具来源，未实现的编辑、透明导出或界面替换不写成已完成。

需要理解这些判断的匿名案例时，读取[连接与生成故障经验](references/session-lessons.md)。技能可以减少重复诊断与误调用，不能保证外部服务始终可用或OAuth永不过期。

官方依据：[Codex MCP](https://learn.chatgpt.com/docs/extend/mcp)、[App Server](https://learn.chatgpt.com/docs/app-server)。执行时以当前宿主及Lovart工具schema为准。
