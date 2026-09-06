# GitHub苹果存活验证规则

这是一个面向 Codex 的本地 Skill：在推荐或评估 GitHub 项目、开源工具、Skill、插件与脚本前，分别核验维护状态、当前可用性与用户环境适配度。

## 文件

- `SKILL.md`：Skill 的入口与操作准则。
- `references/verification-guide.md`：详细检查清单、风险判断与输出模板。
- `agents/openai.yaml`：Codex 中的显示信息。

## 安装

将整个目录复制到 Codex 的 Skills 目录：`D:\codex\.codex\skills\github-project-liveness-validation`。随后重新打开或新建 Codex 对话，使其重新发现本地 Skill。

此 Skill 不会自动安装项目或执行外部账号操作；这类操作仍需用户明确授权。
