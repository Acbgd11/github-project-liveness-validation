# GitHub 项目存活与可用性检查指南

## 检查清单

### 1. 仓库与维护信号

- 仓库是否存在、公开可访问，且未被 `Archived`。
- README、仓库描述、置顶 Issue 和 Release 是否写明 Deprecated、迁移或已知失效。
- 默认分支最近 3–5 个提交的日期、作者和改动范围；区分核心代码修复与只改文档、锁文件、机器人提交或标签。
- 最近 Release 的日期与说明；没有 Release 不必然是负面信号，但有近期 Release 是正面证据。

### 2. 可用性信号

- 近期 Issue/Discussion 中是否有“失效、403、认证失败、报错、无法使用、封号、兼容性”反馈。
- 关键问题是否有维护者回应，是否被关联到修复提交、Release 或清晰的解决方案。
- README 是否仍给出可执行的安装和运行方式；外部 API、模型、网站和账户前提是否仍存在。
- 需要时查看近期 Pull Request 或提交记录，避免把关闭 Issue 误认为已修复。

### 3. 适配度

- 操作系统、CPU/GPU、运行时、依赖版本、网络、授权账户和许可条件是否符合用户环境。
- 维护状态良好也可能不适合：例如只支持 Linux、只支持已下线 API，或需要用户没有的硬件。
- 高变化类别（下载器、爬虫、网站/API 适配、模型接口、浏览器自动化、Harness）对新鲜证据要求更高。

## 风险判断

| 信号 | 建议判断 |
|---|---|
| Archived、作者声明弃用，或明确要求迁移 | 不建议使用旧项目 |
| 近期反馈证实关键功能失效，且无人处理 | 不建议或证据不足 |
| 高频变化项目已有数周无有效维护，同时出现未解决故障 | 谨慎推荐，优先找替代品 |
| 近期有效提交或 Release，维护者处理关键问题，前提与用户匹配 | 推荐 |
| 稳定项目长期未更新，但文档、依赖和近期用户反馈无异常 | 可谨慎推荐，并说明未做实测 |

时间是风险信号而不是通用裁决。不要用“超过六个月必死”或“两个星期内更新必可用”代替证据。

可用以下时间信号做快速初筛；它们不是自动判死规则，最终结论仍以仓库、Issue 和环境证据为准：

| 项目类型 | 时间信号 | 下一步 |
|---|---|---|
| 爬虫、下载器、签名、网站逆向、API 适配 | 默认分支约 1 个月无有效维护 | 提高风险；查近期失效反馈、维护者回应和依赖服务状态 |
| 同类高变化项目 | 默认分支约 3 个月无有效维护 | 必须找到近期成功使用或修复证据；若同时有未解决失效反馈，通常不建议推荐 |
| 稳定 CLI、数据处理库、规范类 | 约 6 个月无有效维护 | 查 Issue、依赖兼容性和用户环境；无异常时仍可谨慎推荐 |
| 任何类型 | Archived、作者明确弃用，或 README 自述关键功能失效 | 不建议使用旧项目；需要时寻找迁移目标 |

## 建议的证据来源

优先使用 GitHub 仓库主页、默认分支提交记录、Release、README、Issue/Discussion 和作者公告。可用 GitHub API、网页或官方文档获取这些数据。搜索结果摘要只能用于发现候选，不能作为最终证据。

## 可选的 GitHub API 取证方式

优先使用已可用的 GitHub 页面、API 或工具；以下 `curl` 仅是没有专用工具时的备用方式。先从仓库元数据取得实际的 `default_branch`，再代入后续请求；不要假设每个仓库都使用 `main`。

```bash
# 仓库元数据：看 default_branch、pushed_at、archived 与描述
curl -s "https://api.github.com/repos/{owner}/{repo}" \
  | grep -E '"(default_branch|pushed_at|created_at|archived|stargazers_count|open_issues_count|description)"'

# 最近 Issue（含已关闭）：看真实使用反馈；响应也可能包含 Pull Request，需区分 pull_request 字段
curl -s "https://api.github.com/repos/{owner}/{repo}/issues?state=all&per_page=20" \
  | grep -E '"(title|state|created_at)"'

# README：用实际默认分支替换 {default_branch}，找免责声明和失效自述
curl -s "https://api.github.com/repos/{owner}/{repo}/readme?ref={default_branch}"

# 默认分支最近提交：区分核心代码修复与文档/机器人提交
curl -s "https://api.github.com/repos/{owner}/{repo}/commits?sha={default_branch}&per_page=5" \
  | grep -E '"(date|message)"'
```

⚠️ **关键陷阱：`updated_at` ≠ `pushed_at`。** `updated_at` 会因 Star、Issue 等活动刷新；`pushed_at` 也可能只是其他分支、标签或非核心文件发生变化。判断维护状态应同时查看 `pushed_at`、默认分支最近提交的改动范围，以及 Release/Issue 证据。

若访问 GitHub 受限，只能使用当前环境已确认可用的网络配置；不要在通用 Skill 中硬编码某台机器的本地代理地址，也不要把加速镜像作为最终证据来源。

## 输出模板

```markdown
## 结论

推荐 / 谨慎推荐 / 不建议 / 证据不足：一句话说明最关键的原因。

| 验证项 | 证据 | 判断 |
|---|---|---|
| 仓库状态 | Archived/Deprecated、检查日期 | ... |
| 最近有效维护 | 默认分支提交与最近 Release | ... |
| 真实使用反馈 | 近期关键 Issue/Discussion 与处理状态 | ... |
| 环境适配 | 系统、依赖、账户或硬件前提 | ... |

### 注意事项与下一步

- 已验证的范围：...
- 未验证或仍有风险的范围：...
- 可选替代方案：...
```

不要把 Star 数当成可用性结论；若列出，只将其作为背景信息。
