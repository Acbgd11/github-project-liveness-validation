# GitHub苹果存活验证规则 v1.2

面向其他智能体复用的 GitHub 项目验证 Skill：分开判断维护状态、目标功能可用性和用户环境适配，以可复核证据给出推荐等级与置信度。

版本：**1.2**；本次整理日期：**2026-09-26**。保留机器标识 `github-project-liveness-validation`，显示名称加入 v1.2，以便延续已有调用和目录引用。

## 使用

让智能体读取 `SKILL.md`，提供用途、候选仓库以及已知的环境/版本约束。支持 Agent Skills 的宿主可将整个文件夹放进其技能目录；具体路径遵循该宿主的说明。不支持自动发现时，也可以直接提供入口和相对引用文件。

公开仓库：`Acbgd11/github-project-liveness-validation`。可把 [仓库地址](https://github.com/Acbgd11/github-project-liveness-validation) 发给其他智能体，让它直接下载整个仓库并读取 `SKILL.md`。有 Git 的环境可运行：

```bash
git clone https://github.com/Acbgd11/github-project-liveness-validation.git
```

核心文件是普通 Markdown，无必需脚本、Token、付费服务或第三方工具依赖。`agents/openai.yaml` 仅提供 Codex 显示信息，其他宿主可忽略。网页/API 不可用时应说明证据不足。

Codex 调用示例：

```text
使用 $github-project-liveness-validation 检查这些 GitHub 项目，比较它们对我的用途是否仍可用，并给出证据、置信度和验证边界。
```

## 文件

- [SKILL.md](SKILL.md)：触发范围、执行流程与核心约束。
- [检查指南](references/verification-guide.md)：动态时间窗口、检查清单、红旗、取证与置信度规则。
- [输出格式](references/report-format.md)：短报告、比较表和可选 JSON 交接格式。
- [参考方法](references/method-sources.md)：借鉴来源、当次状态快照及不能直接照搬的部分。
- [agents/openai.yaml](agents/openai.yaml)：Codex 显示名称与默认调用提示。
- [LICENSE](LICENSE)：MIT 许可，允许保留版权及许可声明后使用、修改与再分发。

## 从旧版保留与升级

保留：三维判断、官方证据、默认分支有效改动、近期真实反馈、动态时效、最小验证、四档推荐，以及不硬编码代理、不把存活等同于安全的边界。

新增/改进：

1. 初筛、标准、深入三级检查；覆盖不足时有明确停止和交付方式。
2. 按项目类型与上游事件调整证据窗口，纳入文档/Skill/新项目。
3. 从故障追踪到修复的具体发布版本，区分稳定版、main 与 nightly。
4. 每个维度的置信度、红旗和缺失证据；缺失不记零，阻断不能被平均分掩盖。
5. 识别机器人活动、自动关闭、过期 CI 徽章、API/权限错误和评分数据停更。
6. 可复核报告与跨智能体交接字段；不捆绑专用工具或本机路径。

这是一套判断流程，不是已安装的仓库扫描服务，也不会自动执行第三方项目或公开报告。安装、账户变更、自动化部署等操作只在当前任务授权范围内执行。
