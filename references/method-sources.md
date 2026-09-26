# v1.2 参考方法与核验记录

核验日期：**2026-09-26（Asia/Shanghai）**。下述是当次快照，不是永久可用性承诺。已读取官方资料、GitHub 元数据、默认分支提交、发布记录及近期问题/PR；没有安装运行这些工具。链接或工具变化时应重新核验，普通项目验证不必每次加载本文件。

## 借鉴到 Skill 的方法

| 来源 | 采用的方法 | 不直接照搬的部分 |
|---|---|---|
| [CHAOSS Starter Project Health](https://www.chaoss.community/starter-project-health-metrics-model/) 与 [Viability Starter](https://www.chaoss.community/kb/metrics-model-project-viability-starter/) | 真人响应、变更处理、发布节奏、维护者集中度及依赖新旧程度；将风险放在使用场景中解释 | 不要求每次计算完整指标，也不对单维护者或低频稳定项目统一扣分；社区健康不能证明具体功能可用 |
| [OpenSSF Scorecard 检查文档](https://github.com/ossf/scorecard/blob/main/docs/checks.md) | 分项证据、测试与依赖更新信号；显式注明自动检测的覆盖局限 | 不套用 Maintained 的固定提交频率作为死亡阈值；安全实践分数不等于功能成功率；配置更新机器人不等于更新已合并 |
| [scorecard-action](https://github.com/ossf/scorecard-action) | 保留 JSON/SARIF 及运行/提交信息，便于复核与比较变化 | 不默认部署 Actions、开放写权限或开启 publish_results；采用前核对私有库功能条件与数据公开范围 |
| [criticality_score](https://github.com/ossf/criticality_score) | 原始信号与评分分离、按用途调整权重、记录数据来源与采集异常 | Criticality 衡量影响/重要性，不是存活或功能健康；不强制依赖其旧公共数据或当前 CLI 分数 |
| [awesome-lint](https://github.com/sindresorhus/awesome-lint) | 把清单条目的格式、描述一致性与筛选流程分开检查，允许持续检查 | 格式检查通过不证明入选项目可用、值得推荐或仍被维护 |
| [lychee](https://github.com/lycheeverse/lychee) / [lychee-action](https://github.com/lycheeverse/lychee-action) | 将链接可达性做成可重复检查，记录错误类型、缓存时效与忽略项 | HTTP 200 可能是登录页/挑战页；403/429/超时不等于死链接；链接正常不等于软件功能正常 |
| [deps.dev 官方 API](https://docs.deps.dev/api/) / [google/deps.dev](https://github.com/google/deps.dev) | 从具体包版本关联源码仓库、依赖图与已知公告，区分仓库 main 和真实分发版本 | 该仓库提供 API 定义/示例，并非托管服务全部源码；聚合数据有覆盖和时效限制，不能推断无未知漏洞或本机必定兼容 |

本 Skill 的推荐等级、置信度、检查深度和 JSON 字段是本次综合设计，不是上述组织发布的统一认证标准。参考内容均为方法提炼，未复制其实现代码。

## 当次状态证据

8 个仓库元数据均为 `archived=false`。以下按各自默认分支取证，日期采用来源事件日期；PR 状态只表示读到的状态，不等于完成复现。

| 项目 | 默认分支及维护/发布证据 | 反馈与适用限制 |
|---|---|---|
| chaoss/wg-metrics-models | `main`；[2025-02-13 提交](https://github.com/chaoss/wg-metrics-models/commit/4365fe61cf9654ef7b3911805425f4e485918672) 合并图片修复；无 GitHub Release | [PR #124](https://github.com/chaoss/wg-metrics-models/pull/124) 与 [PR #114](https://github.com/chaoss/wg-metrics-models/pull/114) 展示文档维护/待处理情况；这是规则文档，低频更新不足以证明方法失效；与官方方法页面结合使用 |
| ossf/scorecard | `main`；[2026-08-15 代码修复](https://github.com/ossf/scorecard/commit/d1fab88f54636ff366076edfc5c239f97b3c8e66) 涉及解压代码及测试；9 月还有依赖更新；[v5.5.0](https://github.com/ossf/scorecard/releases/tag/v5.5.0)，2026-04-23 | [PR #5191](https://github.com/ossf/scorecard/pull/5191) 等近期改进表明特定 GitHub Action 引用的自动识别仍需完善；有持续维护证据，未运行扫描 |
| ossf/scorecard-action | `main`；[2026-08-08 依赖更新](https://github.com/ossf/scorecard-action/commit/e8e61e86ea0a152be84d03729c02a7300065c17d)；[v2.4.4](https://github.com/ossf/scorecard-action/releases/tag/v2.4.4)，2026-07-23 | [PR #1708](https://github.com/ossf/scorecard-action/pull/1708) 为近期依赖更新；样本以自动更新为主，不能据此保证每项检测正确；未部署到用户仓库 |
| ossf/criticality_score | `main`；[2026-08-27 更新 README/数据](https://github.com/ossf/criticality_score/commit/0e76c6a99d865dddcbd89dff4117f0a54b1abfb8)；最新 Release [v2.0.4](https://github.com/ossf/criticality_score/releases/tag/v2.0.4) 为 2024-04-30 | README 明示公共基础设施/云数据停止提供；[PR #831](https://github.com/ossf/criticality_score/pull/831) 与 [PR #832](https://github.com/ossf/criticality_score/pull/832) 报告采集错误，当次仍 open。只借鉴方法，不把 CLI 当前可靠性视为已证实 |
| sindresorhus/awesome-lint | `main`；[2026-07-05 增加 pre-commit 配置](https://github.com/sindresorhus/awesome-lint/commit/4f870abda373cfa18c67822c39ee29c451ae8a70)；[v2.3.0](https://github.com/sindresorhus/awesome-lint/releases/tag/v2.3.0)，2026-04-17 | [PR #229](https://github.com/sindresorhus/awesome-lint/pull/229) 关于标点误报当次仍 open；说明 lint 结果也需解释。运行需要 Node.js/Git，本次未执行 |
| lycheeverse/lychee | `master`；[2026-09-20 功能提交](https://github.com/lycheeverse/lychee/commit/694caf580f2bd7c4497565ca8f26f4dcd8d5da6d) 支持按主机配置接受状态码；稳定 CLI [lychee-v0.24.2](https://github.com/lycheeverse/lychee/releases/tag/lychee-v0.24.2)，2026-05-01；9 月 nightly 单列 | [Issue #2305](https://github.com/lycheeverse/lychee/issues/2305) 中维护者回应并非项目终止，应区分坏链接与自动访问被拒；[Issue #2309](https://github.com/lycheeverse/lychee/issues/2309) 有连接重置报告和维护者回应，不能把工具网络错误直接算死链接 |
| lycheeverse/lychee-action | `master`；[2026-07-09 增加 CI 测试](https://github.com/lycheeverse/lychee-action/commit/649b0e4890508ea3e11ea6b3ee35ce899a25afd5)；[v2.9.0](https://github.com/lycheeverse/lychee-action/releases/tag/v2.9.0)，2026-07-09 | [Issue #342](https://github.com/lycheeverse/lychee-action/issues/342) 报告 GLIBC 兼容问题，已关闭但本次未追查修复版本；采用时仍须核对 runner/二进制版本；未执行 Action |
| google/deps.dev | `main`；[2026-08-27 解析工具代码修复](https://github.com/google/deps.dev/commit/dc936a45c6574bb6e6bd5433de8c74e4cdff1276) 涉及 API client 与测试，9 月有依赖更新；无 GitHub Release | [PR #399](https://github.com/google/deps.dev/pull/399) 是 Maven 插值改进，仍 open；对 API 定义/示例仓库不能用无 Release 判死。官方提供稳定 v3 与实验 v3alpha，本次未作服务端到端验证 |

## 两个直接改进规则的反例

1. **最近更新不保证采集正常。** criticality_score 的 README 更新说明服务/数据停止情况；开放 PR 报告 API 分页和机器人识别造成统计错误。因此新增“缺失/采集失败不能记零”“数据快照必须有日期”“未合并修复不能当作已修复”。PR 中的性能与复现数字属于提交者报告，本次没有独立复现实验。
2. **标题与关闭状态不足以裁决。** lychee 的“End of lychee?” 标题容易被误读。读完维护者评论可知它讨论的是网站挑战页和访问限制，关闭原因是并非具体技术问题。因此要求读取正文、评论、关闭原因与版本，不能把情绪化标题或自动关闭当结论。

## 自动化只作为可选实现

可把初筛元数据、清单格式、链接检查及部分依赖信息交给工具，将目标功能和证据冲突留给人工/智能体复核。输出保留工具版本、所查 commit、运行日期、缓存时效、失败和跳过项目；复查关注有意义的变化。

只有任务明确包含自动化部署时才新增工作流或通知。私有仓库不因为示例写了 publish_results 或创建 Issue 就自动公开扫描结果或发消息；采用 Action 时核验实际权限和固定版本/完整提交 SHA。此 Skill 更新没有新增 Actions 或改变仓库可见性。

## Skill 包装依据

入口采用 `SKILL.md` 的 name/description 与相对引用结构，按需加载支持文件，参考 [OpenAI 官方 Build skills](https://developers.openai.com/plugins/build/skills)。主体流程为宿主无关的 Markdown；没有要求其他智能体拥有 Codex 专用工具。
