# Codex 任务划分规范

## 文档目的

规定一个标准需求在 Codex 中如何拆分任务、读取仓库、修改仓库、产出交付物、使用子代理、使用 Worktree 和交接下一阶段。

## 总体原则

- 一个需求统一使用同一需求编号：`REQ-XXX`。
- 需求分析、设计、开发、测试、发布应拆成独立 Codex 任务，避免上下文污染和越权修改。
- 未确认 PRD 和技术设计前，Codex 禁止修改业务代码。
- SDK 任务按需创建；仅当 OpenAPI、协议、模型、签名/加密、配置或示例变化时创建。

## 标准任务

| 任务 | 可读取仓库 | 可修改仓库 | 必读需求文档 | 必交付物 | 允许改业务代码 | 子代理建议 | Worktree 建议 | 下一步交接 |
|---|---|---|---|---|---|---|---|---|
| `REQ-XXX｜01 需求分析与确认` | `acquiring-delivery`；业务相关代码只读按需 | 仅 `acquiring-delivery` | `original-requirement.md`、`open-questions.md` | PRD、业务规则、验收标准、状态更新 | 否 | 可用子代理只读扫描 | 不需要 | 交接给技术设计任务 |
| `REQ-XXX｜02 系统影响与技术设计` | 全部仓库只读 | 仅 `acquiring-delivery` | PRD、业务规则、验收标准、平台基线、质量门禁 | 影响分析、技术设计、接口/数据库/权限/配置设计、时序图 | 否 | 建议按后端/前端/SDK 只读子代理扫描 | 不需要 | 交接给开发任务 |
| `REQ-XXX｜03 后端开发` | `acquiring-delivery`、`acquiring-orchestration`，前端/SDK 只读按需 | `acquiring-orchestration`、`acquiring-delivery` | 已确认 PRD、技术设计、接口契约、数据库/权限/配置设计、实施计划 | 后端代码、测试、变更清单、后端交接 | 是，仅批准范围 | 可用子代理做代码审查或测试分析 | 建议独立 Worktree | 交接给前端、SDK 或测试 |
| `REQ-XXX｜04 前端开发` | `acquiring-delivery`、`acquiring-frontend`，后端只读 | `acquiring-frontend`、`acquiring-delivery` | PRD、技术设计、接口契约、权限设计、后端交接 | 前端代码、typecheck/build 结果、页面验证、变更清单 | 是，仅批准范围 | 可用子代理做页面 QA | 建议独立 Worktree | 交接给测试 |
| `REQ-XXX｜05 SDK开发（按需）` | `acquiring-delivery`、`acquiring-openapi-java-sdk`、OpenAPI 后端只读 | `acquiring-openapi-java-sdk`、`acquiring-delivery` | OpenAPI 契约、技术设计、SDK 任务、兼容性要求 | SDK 代码、测试、P3C/PMD、打包记录、文档更新 | 是，仅 SDK 范围 | 可用子代理做兼容性检查 | 建议独立 Worktree | 交接给测试和发布 |
| `REQ-XXX｜06 测试与缺陷修复` | 全部相关仓库 | 缺陷确认后仅修改对应代码仓库和 `acquiring-delivery` | 测试计划、测试用例、变更清单、交接记录 | 测试结果、缺陷清单、回归结果 | 修复缺陷时允许，需确认范围 | 建议使用子代理复核失败原因 | 缺陷修复建议独立 Worktree | 交接给代码审查与发布 |
| `REQ-XXX｜07 代码审查与发布` | 全部相关仓库只读 | 仅 `acquiring-delivery`，除非确认修复审查问题 | 测试结果、回归结果、变更清单、发布资料 | 代码审查、部署计划、配置清单、发布清单、回滚方案、发布说明 | 默认否 | 可用子代理做审查 | 不需要；修复问题时按需 | 关闭需求 |

## 交接格式

每个任务完成后必须更新：

- `STATUS.md`
- 当前阶段文档
- `change-list.md` 或对应测试/发布记录
- 未确认内容同步到 `open-questions.md`

## 完成标准

- 每个任务边界清楚，读写仓库最小化。
- 并行任务不修改同一公共 DTO、枚举、数据库实体或根配置。
- 下一任务具备明确输入，不依赖聊天记忆。
