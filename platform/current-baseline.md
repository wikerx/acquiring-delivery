# 现行基线与业务规则入口

2026-11-20 只读核实。以下提交用于 V2 迁移核对。每个需求从目标分支重新检查实际源码、构建配置、契约和测试。以仓库相对路径定位，保留历史 [仓库地图](repository-map.md)、[服务边界](service-boundaries.md)、[环境矩阵](environment-matrix.md)、[构建矩阵](build-test-matrix.md)、[开放问题](baseline-open-questions.md)及[验证报告](baseline-validation-report.md)用于追溯。

| 仓库与核对版本 | 当前基线 | 每次任务的规则与验证入口 |
|---|---|---|
| `acquiring-orchestration` · `feat_scott_channel` · `29a760f9` | Java 17、Maven。现有 CI 执行 Java 注释、结构、日志检查与 Maven 测试。本轮在目标分支未发现 SDK 使用的 P3C profile。代付模块已有测试源码 | 根及最近模块 `AGENTS.md`、`.agents/skills/acquiring-backend-development/SKILL.md`、`docs/standards/coding-standard.md`、`docs/standards/development-governance.md`、模块 POM、`.github/workflows/quality.yml` |
| `acquiring-frontend` · `feat_scott_channel` · `a54dc11` | Vue 3、TypeScript、Vite、npm workspaces。当前已有 lint、typecheck、应用 build、专项检查和 E2E 入口 | 根及应用 `AGENTS.md`、`.agents/skills/acquiring-frontend-development/SKILL.md`、`docs/standards/frontend-development-standard.md`、根和应用 `package.json`、目标组件、路由权限、现有交互与测试 |
| `acquiring-openapi-java-sdk` · `feature_scott_payment` · `786efaa` | Java 17、Maven 单模块。通过现有 P3C profile 执行 PMD。规则集明确排除 `ClassMustHaveAuthorRule` | 根 `AGENTS.md`、`.agents/skills/acquiring-sdk-development/SKILL.md`、`docs/standards/sdk-development-standard.md`、`pom.xml`、`config/p3c/p3c-ruleset.xml`、`.github/workflows/quality.yml` |

后端和前端当前分支提交与所查远端一致。SDK 基线取本地提交，远端当前提交待核实。三份工作区均有既有改动。未运行构建、测试、环境联调或发布。历史基线中的 SDK Java 8、前端没有 lint、代付没有测试是历史观察。

## 使用规则

- 用户此前按 Word 整理并落地的规范，由各仓库现行文件和检查器执行。交付仓库只定义交付流程和跨仓门禁。Java 遵循目标仓库已有阿里巴巴规范和项目例外。前端沿用已有组件、界面风格、权限、列表操作和业务交互。
- 核对已批准需求、现行契约、根及最近一级规则、实际代码和验证入口。发现不一致时记录双方来源与影响，遵循目标仓库冲突处理规则。涉及 P0、协议、金额、权限、持久化、发布的未确认冲突进入私有台账。日志、证据和公开交付材料执行脱敏要求。
- 记录实际使用的仓库、分支、提交、工作区状态、规则路径和验证环境。私有 SDK 当前实现、内部地址和业务测试输出按私有交付记录处理。
