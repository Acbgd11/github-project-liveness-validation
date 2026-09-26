# 输出格式 v1.2

普通单项目采用短报告；批量比较用同一组维度；仅在需要跨智能体交接或机器处理时补 JSON。不要为每个简单初筛输出冗长表格。

## 单项目短报告

```markdown
结论：推荐 / 谨慎推荐 / 不建议 / 证据不足（置信度：高 / 中 / 低）。一句话说明决定性原因。

- 项目：[owner/repo](官方仓库链接)
- 用途、目标版本/安装渠道、用户环境：...
- 检查时间：YYYY-MM-DD（时区）；深度：初筛 / 标准 / 深入
- 证据范围：分支/版本、检索时间窗及提交和反馈抽样范围；未覆盖范围：...

| 维度 | 判断与置信度 | 可复核证据（事件日期、版本、直接链接） | 限制或反证 |
|---|---|---|---|
| 维护 | 活跃 / 稳定低频 / 停止 / 未知；... | ... | ... |
| 目标功能 | 已本机实测 / 外部证据支持 / 已证实失效 / 未知；... | ... | ... |
| 环境适配 | 已确认 / 条件性适配 / 不适配 / 未知；... | ... | ... |

- 红旗与影响：已确认 / 可疑待核实；没有发现也不等于保证无风险。
- 实测范围：版本、环境、输入与观察结果；未执行时写“未执行”。
- 证据缺口：哪些事实未知，会怎样影响结论。
- 下一步：最小补证、试用条件或已同等验证的替代方案。
- 复核触发：正式采用前、上游 API/认证变化、关键新故障或版本/环境改变。
```

## 批量比较

| 项目/目标版本 | 维护 | 目标功能 | 环境适配 | 红旗/缺口 | 推荐等级 | 置信度 | 关键证据与日期 |
|---|---|---|---|---|---|---|---|

同类候选使用相同的检查深度和目标环境。不同类型不以原始提交数、Issue 数或 Star 排名。未完成标准验证的条目标注“初筛候选”。

## 可选的机器交接记录

使用稳定字段，事实缺失为 `null` 或 `unknown`，不存在或不适用为 `not_applicable`，不要混为 0。以下为空记录示例，输出时替换为实际值；没有事实时保留空数组及原因。

```json
{
  "schema_version": "1.2",
  "repository": null,
  "checked_at": null,
  "scope": {
    "purpose": null,
    "project_type": null,
    "target_version": null,
    "install_source": null,
    "environment": null,
    "depth": "standard",
    "evidence_window": null,
    "sample_scope": null
  },
  "verdict": "insufficient_evidence",
  "confidence": "low",
  "rationale": "尚未取证",
  "dimensions": {
    "maintenance": {"status": "unknown", "confidence": "low", "evidence_ids": []},
    "functionality": {"status": "unknown", "confidence": "low", "evidence_ids": []},
    "environment_fit": {"status": "unknown", "confidence": "low", "evidence_ids": []}
  },
  "evidence": [],
  "red_flags": [],
  "tests": [],
  "unknowns": ["尚未取证"],
  "next_steps": [],
  "recheck_triggers": []
}
```

- `verdict`：`recommend / cautious / avoid / insufficient_evidence`；`confidence`：`high / medium / low`。
- maintenance.status：`active / stable_low_activity / discontinued / unknown`。
- functionality.status：`tested_here / externally_supported / broken / unknown`。
- environment_fit.status：`confirmed / conditional / incompatible / unknown`；文档类不需运行环境时可 `not_applicable` 并解释。
- 每条 evidence 至少含 `id, source_url, retrieved_at, event_at, version_or_commit, kind, observation, supports, limitations`。`kind` 用 `observed / reported / inferred`；推断须指明依据，日期未知保留 `null`。
- 每条 red_flags 含 `flag, certainty, evidence_ids, impact`；`certainty` 为 `confirmed / suspected`。
- 每条 tests 含 `status, version, environment, action, result, limitations`；`status` 为 `passed / failed / not_run`。`passed` 只适用于记录的测试动作。
- 如使用聚合服务或评分，再记录工具版本、数据快照、覆盖率及取数错误；没有取到数据与测得为零必须分开。

## 判断示例（虚构情境，非真实仓库评估）

- **稳定离线工具两年无提交**：目标运行时的最小功能测试通过，支持条件符合，无已知关键阻断。可推荐，维护标“稳定低频”；不能仅凭两年未更新否定。
- **热门下载器昨日更新徽章**：目标站点认证上周失效，维护者确认尚未修复。该用途不建议；Star 和更新时间不能抵消。
- **Issue 被机器人关闭**：没有修复链接或发布记录。功能仍未知；不能写“已修复”。
- **API 403、Issues 禁用**：查已授权访问和官方外部跟踪器；仍无数据则证据不足，不能记“0 个问题，健康”。
- **修复在 main、安装包未发布**：稳定版不建议或证据不足，取决于故障是否已证实；源码试用需另行验证与说明条件。
- **规则/文档仓库没有 Release**：检查内容质量、有效修订、引用与行为适配，不按可执行程序的发版频率判定。
