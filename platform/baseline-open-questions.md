# Acquiring Platform 基线开放问题

> 本文沉淀本轮只读扫描发现的待确认事项、项目结构问题和建议新增的 AGENTS.md。未确认内容不作为事实结论。

## 1. 已发现的项目结构问题

| 编号 | 问题 | 影响 | 依据 | 建议 |
|---|---|---|---|---|
| Q-001 | `acquiring-delivery` 远程仓库已确认，本地待初始化 Git | 架构、规范、需求、测试和发布资料已有明确版本控制边界 | 用户 2026-07-22 确认远程地址为 `https://github.com/wikerx/acquiring-delivery.git`；初始化前 `git -C acquiring-delivery rev-parse --show-toplevel` 返回 `fatal: not a git repository` | 初始化本地 Git，设置远程并推送交付资料 |
| Q-002 | 后端 README 文档导航存在旧路径或缺失路径 | 新人入口和项目基线引用可能失效 | README 中 `docs/git-branching.md`、`docs/coding-standard.md`、`docs/code-review.md`、`docs/payment-engineering.md` 等路径检查为 missing；当前实际文档多在 `docs/standards`、`docs/architecture`、`docs/api/openapi` | 更新 README 文档导航或建立跳转索引 |
| Q-003 | 后端存在未跟踪 `outputs/` | 容易与本次文档初始化或后续扫描产物混淆 | `git -C /Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration status --short --branch` 显示 `?? outputs/` | 确认是否应加入 `.gitignore`、归档、删除或纳入版本管理 |
| Q-004 | SDK 仓库存在未提交修改、删除和未跟踪文件 | SDK 接口、版本、测试结论可能处于中间态 | SDK `git status --short --branch` 显示多处 `M`、`D`、`??` | 先确认这些变更是否属于当前目标版本 |
| Q-005 | SDK 版本信息不一致 | 商户接入、User-Agent、文档版本可能混乱 | `pom.xml`、README、CHANGELOG 为 `0.9.0`；`OpenApiConstants.SDK_VERSION` 为 `1.0.0`，且该文件有未提交修改 | 确认 SDK 当前版本基线应为 `0.9.0` 还是 `1.0.0` |
| Q-006 | 后端 Nacos dev 目录缺少 `service-checkout-dev.yaml`、`service-payout-dev.yaml` | 这两个服务的服务专属配置来源不清晰 | `docs/deployment/nacos` 下检查显示 missing | 确认是否需要新增 DataId 或明确使用公共配置 |
| Q-007 | 前端同时存在 npm workspace 与 pnpm workspace/lock | 新人安装依赖和 CI 构建命令可能分歧 | 根 `package.json` 使用 npm workspaces；同时存在 `pnpm-workspace.yaml`、`pnpm-lock.yaml` | 明确主包管理器，并在 README/AGENTS 中固化 |
| Q-008 | 前端未发现 lint/test scripts | 质量门禁不完整 | 根和各 workspace `package.json` 未发现 `lint`、`test`；未发现 Vitest/Jest/Cypress 配置 | 确认是否暂不建设，或新增质量基线任务 |
| Q-009 | Merchant/Checkout README 未发现 | 三套前端文档不均衡 | 子代理未发现 `apps/merchant-portal/README*`、`apps/hosted-checkout/README*` | 为商户系统和收银台补齐 README |
| Q-010 | `service-payout` 未发现测试目录 | 代付核心服务测试基线不足或尚未初始化 | `find ... -path '*/src/test/*'` 输出未包含 `service-payout` | 确认代付模块当前阶段和最小测试基线 |
| Q-011 | Maven 运行时 Java 与项目 Java 基线不一致 | 本地构建可能使用 Java 26 编译/运行 Maven，项目目标是 Java 17 | `mvn -v` 显示 Maven runtime Java 26.0.1；`java -version` 显示 OpenJDK 17.0.19；后端 `pom.xml` 配置 Java 17 | 固化 Maven 使用 JDK 17 的方式，例如 toolchains 或 shell 环境说明 |
| Q-012 | SDK 未发现独立示例工程和发布脚本 | 商户接入演示和发布流程不够明确 | 未发现 `examples/`、`sample/`；未发现 `distributionManagement` 或 release/deploy 脚本 | 确认是否需要 SDK sample project 和 release checklist |
| Q-013 | SDK Java 基线已确认保持 JDK 1.8 / Java 8 编译目标 | 版本过高可能导致较多商户无法使用 SDK | 用户 2026-07-22 明确确认；SDK `pom.xml` 当前为 `<java.version>1.8</java.version>` | 后续 SDK 不得未经确认升级 Java target；后端服务仍统一 JDK 17 |

## 2. 后续建议建立的 AGENTS.md

| 建议位置 | 目的 | 依据 |
|---|---|---|
| `/Users/scott/Documents/code/ideaCodex/acquiring/AGENTS.md` | 统一多仓库协作边界：哪些目录可写、哪些仓库只读、delivery 文档位置、跨仓扫描输出格式 | 当前顶层包含 4 个项目目录，但顶层不是 Git 仓库 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-delivery/AGENTS.md` | 固化交付资料目录规范：architecture、requirements、test、release、platform 等分类，避免临时 Markdown 散落 | delivery 当前无 README/AGENTS |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/AGENTS.md` | 固化 SDK 安全边界：不得泄露密钥、不得破坏签名/JWT/加密兼容、版本发布规则、Java 8 兼容要求 | SDK 当前无 AGENTS，且存在安全协议和版本问题 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-openapi/AGENTS.md` | 约束商户 OpenAPI：`POST`、`/api/rest/{domain}/{version}`、`@VerificationAndProcessing`、请求/响应加密、防重放、敏感日志 | 后端根 AGENTS 已定义 OpenAPI 高风险规则，建议下沉到服务目录 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payment/AGENTS.md` | 约束支付核心：状态机、幂等、金额精度、MQ 事件、渠道映射、商户通知 | 后端根 AGENTS 指出 payment 仍有骨架/模拟实现风险 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payout/AGENTS.md` | 约束代付核心：审核、渠道路由、状态机、回调、退汇、流水与账务状态 | 后端根 AGENTS 指出 payout 仍有模拟实现风险 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/AGENTS.md` | 防止 component-library 变成业务垃圾桶 | 后端根 AGENTS 明确 component-library 边界 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/channel-library/AGENTS.md` | 防止渠道库承载平台状态机或商户业务规则 | 后端根 AGENTS 明确 channel-library 边界 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/admin-system/AGENTS.md` | 固化后台管理端页面、权限、表格、导出、路由和 Element Plus 使用规则 | 前端根 AGENTS 只到总体技术栈 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/merchant-portal/AGENTS.md` | 固化商户系统权限、菜单、接口、品牌和商户侧安全交互规则 | 商户系统无 README |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/hosted-checkout/AGENTS.md` | 固化收银台支付体验、i18n、Tailwind、敏感卡信息展示/保存禁忌 | Checkout 无 README，且属于高敏前端 |
| `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/packages/shared/AGENTS.md` | 约束共享包：只放跨 app 基础能力，避免塞入单一业务系统私有逻辑 | 三套前端依赖 `@acquiring/shared` |

## 3. 需要项目负责人确认的信息

| 类型 | 需要确认 | 当前依据 |
|---|---|---|
| 仓库治理 | `acquiring-delivery` 已确认使用独立远程仓库 `https://github.com/wikerx/acquiring-delivery.git`；本地需初始化并推送 | 用户 2026-07-22 确认 |
| 后端文档 | 后端 README 文档导航是否应迁移到 `docs/standards`、`docs/architecture`、`docs/api/openapi` 新路径 | README 链接检查 missing |
| 后端配置 | `service-checkout`、`service-payout` 是否需要服务专属 Nacos DataId | Nacos dev 目录缺少对应文件 |
| 后端运行 | 后端服务已确认统一使用 JDK 17；仍需固化本地 Maven runtime 使用 JDK 17 的方式 | 用户 2026-07-22 确认后端服务统一 JDK 17；本机 Maven runtime Java 26 |
| 后端模块状态 | `service-payment`、`service-payout` 当前是否仍按“骨架/模拟实现”管理 | 后端 `AGENTS.md` 明确提醒 |
| 前端工程 | npm 与 pnpm 哪个是主包管理器 | README 使用 npm；存在 pnpm workspace/lock |
| 前端质量 | 是否要建立 lint/test 基线 | 当前 scripts 未发现 |
| 前端文档 | Merchant/Checkout 是否需要独立 README | 当前未发现 |
| SDK 版本 | `0.9.0` 与 `OpenApiConstants.SDK_VERSION=1.0.0` 哪个是当前基线 | SDK `pom.xml`/README/CHANGELOG 与常量不一致 |
| SDK 发布 | SDK 是否需要 `distributionManagement`、release profile、示例工程、发布 checklist | 当前未发现 |
| SDK worktree | 当前未提交修改是否属于本轮前已有业务变更，是否可作为基线 | SDK `git status` 显示多处修改和未跟踪文件 |

## 4. 本阶段未处理事项

| 未处理事项 | 原因 |
|---|---|
| 不修改后端、前端、SDK 业务代码 | 用户明确约束 |
| 不重构模块或移动文件 | 用户明确约束 |
| 不安装、升级依赖 | 用户明确约束 |
| 不启动 Spring Boot、前端 dev server、Redis、RocketMQ、数据库、Nacos、XXL-JOB | 用户明确约束 |
| 不运行构建、测试、lint | 本阶段为只读扫描，且部分命令可能触发外部依赖或使用当前 dirty worktree |
| 不提交、不推送 | 用户明确约束 |
