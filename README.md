# Midjourney 生图与优化工作流｜Codex + Lovart MCP

将可复用的Codex技能保存在GitHub，便于安装到个人环境并持续维护。首个技能通过 **Lovart MCP** 调用Midjourney，覆盖从参考图、sref和提示词准备，到结果回收、候选比较、反馈优化与导出核验的流程。

这是工作流技能，不是Midjourney官方API客户端，也不提供或转移任何账户授权。

## 包含的能力

- **参考图与画风控制**：区分图像／造型输入和sref，使用当前模型schema构造请求，不将画风参考当作身份锁定。
- **生成管理**：复用项目、明确调用预算、登记任务ID、按账户并发限制提交，避免超时后重复生图。
- **结果与评估**：取回全部候选，实际看图后按用途比较，区分接口完成、助手推荐和用户采用。
- **迭代优化**：整体方向探索与局部精确修改分别处理，比较编辑前后差异，核验透明背景和实际尺寸。
- **连接与失败诊断**：区分工具未加载、OAuth失效、模型API错误与视觉不满意，减少无效重登录与重试。

适用于插画、角色、概念图及其他用户指定的图像工作。具体风格、图片数量、参数和是否使用其他编辑模型由当次需求决定。

## 安装

在Codex中输入：

```text
$skill-installer 请安装：
https://github.com/qiu-qiu-xia1207/codex-midjourney-workflow/tree/main/skills/lovart-mcp-workflow
```

使用Codex内置的skill-installer安装器，下一轮对话即可使用。若技能未出现在可用列表中，再检查安装目录和当前宿主的技能发现状态。不要把整个仓库复制到单个技能目录。

## 调用示例

只检查接入，不生图：

```text
$lovart-mcp-workflow
先验证 Lovart MCP 是否可用，不提交生成任务。
```

制作画风小样：

```text
$lovart-mcp-workflow
在我指定的 Lovart 项目中，用参考图作为 sref 生成两个画风小样。
最多两次 Midjourney 调用，失败也计入；保留全部结果，先比较后再做局部修改。
```

继续已有任务：

```text
$lovart-mcp-workflow
先读取当前项目记录，查询已有任务，不重复提交。
取回候选后按我的反馈整理下一轮优化方案。
```

## Lovart MCP 接入

安装技能与配置服务、账户授权是不同步骤。先复用已有Lovart配置；只有配置缺失时才添加：

```powershell
codex mcp add lovart --url https://lgw.lovart.ai/v1/openapi/mcp/widgets --oauth-client-id codex-lovart
```

首次需要授权或实际认证失败时：

```powershell
codex mcp login lovart
```

生成会使用账户积分。技能会遵守用户授权的调用预算，连接验证只使用必要的只读接口。GitHub不保存OAuth凭据；新设备首次使用仍需自己的授权。只读连接成功不代表Midjourney生成接口一定正常。

## 更新

仓库中的修改不会自动同步到已安装副本。需要更新时，在Codex中指定本仓库和技能目录，请求将更新安装到**已发现的现有技能目录**；先比较差异并保留本地定制，不直接覆盖。内置安装器遇到同名目录会停止，应先安装到临时目录检查，再更新原目录。

如需固定版本，可要求安装某个提交SHA，而不是始终跟随main。

## 目录与边界

技能位于[`skills/lovart-mcp-workflow`](skills/lovart-mcp-workflow/SKILL.md)，详细流程与匿名故障案例按需从references读取。

仓库不包含项目图片、项目ID、账户余额、原始接口快照、用户本机路径或认证材料；示例不是生成质量或服务可用性的保证。精确编辑使用其他工具时必须符合当轮授权，并记录实际来源。

依据：[官方技能说明](https://learn.chatgpt.com/docs/build-skills)、[Codex MCP](https://learn.chatgpt.com/docs/extend/mcp)。
