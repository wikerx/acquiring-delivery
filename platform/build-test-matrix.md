# Acquiring Platform 构建与测试矩阵

> 本文记录各仓库当前可确认的构建、测试、类型检查、规约检查与发布命令。按本阶段约束，本次未运行项目构建、测试、lint 或 dev server。

## 1. 后端 `acquiring-orchestration`

| 项目 | 结论 | 命令 / 配置 | 依据 |
|---|---|---|---|
| 构建工具 | Maven，多模块工程 | 根 `pom.xml` packaging 为 `pom`，声明 11 个一级模块 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/pom.xml` |
| 项目 Java 版本 | Java 17 | `<java.version>17</java.version>`，compiler plugin `<release>${java.version}</release>` | 根 `pom.xml` |
| Spring Boot | 3.5.14 | `<spring-boot.version>3.5.14</spring-boot.version>` | 根 `pom.xml` |
| Maven profile | `dev`、`test`、`uat`、`prod`；默认 `dev` | `<profiles>` 配置 | 根 `pom.xml` |
| 打包命令 | `mvn -Pdev clean package`、`mvn -Ptest clean package`、`mvn -Puat clean package`、`mvn -Pprod clean package` | README 明确列出 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/README.md` |
| 测试命令 | `mvn -Pdev test` | README 明确列出 | README |
| 单元/集成测试现状 | 已发现多个模块存在 `src/test` 测试文件，覆盖 component、channel、admin、checkout、job、merchant、openapi、payment、risk；未发现 `service-payout/src/test` 文件 | `find ... -path '*/src/test/*' -type f` | 本次只读扫描 |
| OpenAPI 测试 | 存在 `OpenApiSecurityFlowTests`、`MerchantOpenApiEndToEndTests`、`MerchantClientRequestWithoutDatabaseTests` 等 | `service-openapi/src/test/java` | 本次只读扫描 |
| 支付核心测试 | 存在状态机、MQ 消费、渠道调用、回调、通知、记录服务测试 | `service-payment/src/test/java` | 本次只读扫描 |
| 调度测试 | 存在汇率解析、通知重试、节点服务测试 | `service-job/src/test/java` | 本次只读扫描 |
| 本次是否运行测试 | 未运行 | 不适用 | 用户约束只读扫描，不启动外部服务 |

## 2. 后端模块测试线索

| 模块 | 已发现测试线索 | 依据 |
|---|---|---|
| `component-core` | 密码/token、卡号生成、全局 ID、ISO 标准、脱敏工具等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/component-core/src/test/java` |
| `component-db` | 权限服务、分表算法、分表范围、分片键解析等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/component-db/src/test/java` |
| `component-redis` | Redis ID 自动配置、ID 生成、集成测试、Redis 时间提供者测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/component-redis/src/test/java` |
| `component-security` | OpenAPI 商户密钥材料服务测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/component-security/src/test/java` |
| `component-web` | 全局异常、统一错误、内部服务鉴权拦截测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/component-library/component-web/src/test/java` |
| `channel-library/payment-channel-library` | MPGS、WorldPay、渠道注册、回调处理测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/channel-library/payment-channel-library/src/test/java` |
| `service-admin` | 认证、基础字典、任务调度、交易查询、渠道、汇率、商户、MFA 等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-admin/src/test/java` |
| `service-checkout` | Country Config 应用服务测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-checkout/src/test/java` |
| `service-job` | 汇率抓取、通知重试、执行节点服务等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-job/src/test/java` |
| `service-merchant` | 商户认证应用服务测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-merchant/src/test/java` |
| `service-openapi` | OpenAPI 安全、ISO、商户入网、E2E、数据库 flow、回调安全支持等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-openapi/src/test/java` |
| `service-payment` | 交易状态机、通知消费、渠道调用、回调、outbox relay、通知、交易记录、风控调用等测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payment/src/test/java` |
| `service-risk` | 风控评估应用服务和默认风控服务测试 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-risk/src/test/java` |
| `service-payout` | 待确认，扫描未发现 `src/test` 文件 | `find ... -path '*/src/test/*'` 输出未包含 `service-payout` |

## 3. 前端 `acquiring-frontend`

| 项目 | 命令 / 结论 | 依据 |
|---|---|---|
| 包管理结构 | npm workspaces，`workspaces: ["apps/*", "packages/*"]` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/package.json` |
| pnpm 线索 | 存在 `pnpm-workspace.yaml`、`pnpm-lock.yaml` | 文件扫描 |
| 主包管理器 | README 使用 npm；pnpm 是否作为主流程待确认 | 子代理扫描 |
| Admin dev | `npm run dev:admin` -> `npm --workspace @acquiring/admin-system run dev` -> `vite --host 0.0.0.0 --port 5173` | 根和 Admin `package.json` |
| Merchant dev | `npm run dev:merchant` -> `npm --workspace @acquiring/merchant-portal run dev` -> `vite --host 0.0.0.0 --port 5174` | 根和 Merchant `package.json` |
| Checkout dev | `npm run dev:checkout` / `npm run dev:hosted-checkout` / `npm run dev:cashier` -> hosted checkout dev，端口 5175 | 根和 Checkout `package.json` |
| 全量 build | `npm run build` -> `npm run build --workspaces --if-present` | 根 `package.json` |
| Admin build | `npm run build:admin` -> `vue-tsc --noEmit && vite build` | 根和 Admin `package.json` |
| Merchant build | `npm run build:merchant` -> `vue-tsc --noEmit && vite build` | 根和 Merchant `package.json` |
| Checkout build | `npm run build:checkout` -> `vue-tsc --noEmit && vite build` | 根和 Checkout `package.json` |
| typecheck | `npm run typecheck` -> 各 workspace `typecheck` | 根和各 workspace `package.json` |
| format | `prettier --write "{apps,packages}/**/*.{ts,vue,css,md,json}"` | 根 `package.json` |
| lint | 待确认，未在 `package.json` scripts 中发现 `lint`，未发现 ESLint 配置 | 子代理扫描 |
| test | 待确认，未在 `package.json` scripts 中发现 `test`，未发现 Vitest/Jest/Cypress 配置 | 子代理扫描 |
| 本次是否运行前端命令 | 未运行 | 用户约束不启动服务、不安装依赖；本次只读读取配置 |

## 4. SDK `acquiring-openapi-java-sdk`

| 项目 | 命令 / 结论 | 依据 |
|---|---|---|
| 构建工具 | Maven 单模块 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/pom.xml` |
| Java 版本 | Java 8 / `1.8` | `pom.xml` 的 `java.version=1.8`、README Requirements |
| Maven 要求 | Maven 3.6+ | SDK README |
| 编译插件 | `maven-compiler-plugin 3.13.0`，source/target `1.8` | SDK `pom.xml` |
| 测试插件 | `maven-surefire-plugin 3.3.1` | SDK `pom.xml` |
| 源码包 | `maven-source-plugin 3.3.1` 在 package 阶段执行 `jar-no-fork` | SDK `pom.xml` |
| Javadoc 包 | `maven-javadoc-plugin 3.6.3` 在 package 阶段执行 `jar` | SDK `pom.xml` |
| 规约扫描 | `mvn -Pp3c pmd:check` | SDK README、`config/p3c/p3c-ruleset.xml`、`pom.xml` profile |
| 常规测试命令 | 待确认；可按 Maven 约定使用 `mvn test`，但 README 未明确列出 | SDK `pom.xml` 已配置 surefire |
| 打包命令 | 待确认；可按 Maven 约定使用 `mvn package`，但 README 未明确列出 | SDK `pom.xml` 已配置 source/javadoc attach |
| 发布命令 | 待确认 | 未发现 `distributionManagement`、release/deploy 脚本或发布文档 |
| CI 配置 | 待确认 | 子代理未发现 `.github`/YAML 输出 |
| 测试文件数量 | 已跟踪测试 Java 文件 22 个 | 子代理 `git ls-files` 统计 |
| 既有测试报告 | `target/surefire-reports` 下存在历史测试报告文件 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/target/surefire-reports` |
| 本次是否运行测试 | 未运行 | 用户约束只读扫描 |

## 5. 构建环境待确认项

| 待确认项 | 原因 | 依据 |
|---|---|---|
| 后端 Maven 实际运行 JDK 应固定为 17 还是允许 26 | `mvn -v` 显示 Maven runtime Java 26.0.1，而 `java -version` 显示 17.0.19；项目配置 Java 17 | 本机命令输出、后端 `pom.xml` |
| 前端主包管理器 | README 使用 npm，但仓库存在 pnpm workspace/lock | 根 `package.json`、`pnpm-workspace.yaml`、`pnpm-lock.yaml` |
| 前端 lint/test 基线 | 当前未发现 `lint`/`test` scripts | 根和 workspace `package.json` |
| SDK 发布流程 | 未发现 release/deploy 配置 | SDK `pom.xml`、文档扫描 |
| SDK 当前 dirty worktree 是否属于目标基线 | 未提交修改影响接口和版本判断 | SDK `git status --short --branch` |
