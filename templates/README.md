# V2 需求模板索引

每个需求位于私有台账的 `requirements/REQ-XXX/`。从对应源文件生成并填写适用内容，移除待填占位，回链需求编号、候选版本和证据。共用的风险、变更与证据在对应阶段的文档记录一次，通过链接引用。旧的 [V1 完整样板](../requirements/_TEMPLATE/README.md)保持可读。

| 阶段 | 文件 | 来源或填写要点 |
|---|---|---|
| 初始化 | `STATUS.md`、`DECISIONS.md` | [状态](status-template.md)、[决定](decisions-template.md) |
| 01 需求分析 | `01-requirement/original-requirement.md`、`prd.md`、`business-rules.md`、`acceptance-criteria.md`、`open-questions.md` | [需求输入](requirement-intake-template.md)、[PRD](prd-template.md)、[业务规则](business-rules-template.md)、[验收标准](acceptance-criteria-template.md)、[问题](open-questions-template.md) |
| 02 技术设计 | `02-design/impact-analysis.md`、`technical-design.md`、`implementation-plan.md` | [影响分析](impact-analysis-template.md)、[技术设计](technical-design-template.md)、[实施计划](implementation-plan-template.md)。按需补 [接口](api-contract-template.yaml)、[数据库](database-design-template.md)、权限、配置和时序 |
| 03 开发 | `03-development/change-list.md` 和适用任务文件 | 记录变更路径、审批范围、候选提交、自测。按需用 [后端](backend-task-template.md)、[前端](frontend-task-template.md)、[SDK](sdk-task-template.md) |
| 04 独立测试 | `04-testing/test-plan.md`、`test-cases.md`、`test-result.md`，按需 `defect-list.md`、`regression-result.md` | [计划](test-plan-template.md)、[用例](test-case-template.md)、[缺陷](defect-template.md)。证据记录环境、实际操作、版本、结果和退出码 |
| 05 Code Review | `05-review/code-review.md` | [Code Review](code-review-template.md)。记录候选提交、发现及复审证据 |
| 06 最终验收 | `06-acceptance/acceptance-result.md` | 一条验收标准一行，关联版本、证据路径和结论。记录剩余风险与验收建议 |
| 07 发布准备 | `07-release/release-checklist.md`、`release-notes.md`，按需部署、配置和回滚文件 | [发布清单](release-checklist-template.md)、[说明](release-notes-template.md)、[部署](deployment-plan-template.md)、[回滚](rollback-plan-template.md) |

某项检查不适用时，写明理由。无需为无影响的仓库、接口或配置批量复制占位文档。七个阶段均须提交明确的结论供审批。
