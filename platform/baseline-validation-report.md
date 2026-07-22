# Acquiring Platform 基线验证报告

> 验证时间：2026-07-22  
> 验证范围：`acquiring-delivery`、`acquiring-orchestration`、`acquiring-frontend`、`acquiring-openapi-java-sdk`  
> 本次约束：不修改业务代码，不修改数据库，不升级依赖，不提交或推送 Git；构建失败只记录原因。

## 0. 人工确认事项

| 时间 | 确认项 | 结论 |
|---|---|---|
| 2026-07-22 | `acquiring-delivery` 远程仓库 | 使用 `https://github.com/wikerx/acquiring-delivery.git` |
| 2026-07-22 | SDK Java 基线 | `acquiring-openapi-java-sdk` 保持 JDK 1.8 / Java 8 编译目标，因为版本过高可能导致较多商户无法使用 |
| 2026-07-22 | 后端 Java 基线 | 除 SDK 外，其他后端服务统一使用 JDK 17 |

## 1. 本次修改范围

| 仓库 | 文件 | 操作 | 说明 |
|---|---|---|---|
| `acquiring-delivery` | `AGENTS.md` | 新增 | 建立交付资料仓库级协作规则 |
| `acquiring-orchestration` | `AGENTS.md` | 完善 | 追加 JDK 17、分层、MapStruct、金额、幂等、状态机、敏感日志、验证真实性等仓库级门禁 |
| `acquiring-frontend` | `AGENTS.md` | 完善 | 追加三套前端目录、构建基线、后端契约、敏感数据、变更控制规则 |
| `acquiring-openapi-java-sdk` | `AGENTS.md` | 新增 | 建立 SDK 兼容性、安全、配置、构建、发布规则 |
| `acquiring-delivery` | `standards/development-gates.md` | 新增 | 建立开发阶段门禁 |
| `acquiring-delivery` | `standards/testing-gates.md` | 新增 | 建立测试阶段门禁 |
| `acquiring-delivery` | `standards/security-gates.md` | 新增 | 建立安全阶段门禁 |
| `acquiring-delivery` | `standards/documentation-gates.md` | 新增 | 建立文档阶段门禁 |

未修改后端、前端、SDK 业务代码；未修改数据库；未升级依赖；未提交或推送 Git。

## 2. 扫描文档校验结论

### 2.1 已确认

| 项目 | 结论 | 依据 |
|---|---|---|
| 仓库结构 | 顶层包含 `acquiring-orchestration`、`acquiring-frontend`、`acquiring-openapi-java-sdk`、`acquiring-delivery` | `pwd`、`rg --files`、`find acquiring-delivery -maxdepth 3 -type f` |
| 后端 Maven 一级模块 | 根 `pom.xml` 声明 `component-library`、`channel-library`、9 个 `service-*` 模块 | `acquiring-orchestration/pom.xml` 第 14-25 行 |
| 后端公共组件模块 | `component-library` 声明 9 个子模块：`component-core`、`component-excel`、`component-web`、`component-security`、`component-http`、`component-db`、`component-redis`、`component-mq`、`component-job` | `acquiring-orchestration/component-library/pom.xml` 第 16-25 行 |
| 后端渠道库模块 | `channel-library` 声明 `payment-channel-library`、`payout-channel-library` | `acquiring-orchestration/channel-library/pom.xml` 第 16-18 行 |
| 后端 Java 基线 | 项目配置 Java 17，编译插件使用 `release` | `acquiring-orchestration/pom.xml` 第 29 行、第 45 行；构建输出显示 `javac [debug release 17]` |
| 后端启动类 | 9 个服务启动类存在 | `find acquiring-orchestration -path '*/src/main/java/*Application.java'` 输出 9 个 `*Application.java` |
| 后端环境文件 | 9 个服务均存在 `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find acquiring-orchestration -path '*/src/main/resources/application*.yml'` |
| 前端三个应用目录 | `apps/admin-system`、`apps/merchant-portal`、`apps/hosted-checkout` 存在 | `acquiring-frontend/apps/*/package.json`、`vite.config.ts`、`src/main.ts` |
| 前端 workspace | 根 `package.json` 使用 npm workspaces：`apps/*`、`packages/*` | `acquiring-frontend/package.json` 第 6-9 行 |
| 前端 shared 包 | 当前唯一带 `package.json` 的共享包是 `packages/shared`，导出为 `@acquiring/shared` | `find acquiring-frontend/packages -mindepth 2 -maxdepth 2 -name package.json` |
| 前端构建命令 | 根 scripts 提供 `build`、`build:admin`、`build:merchant`、`build:checkout`、`typecheck`、`format`，未提供 `lint` / `test` | `acquiring-frontend/package.json` 第 16-23 行、`npm run`、`npm run lint`、`npm run test` |
| SDK Maven 构建方式 | SDK 为 Maven 单模块，版本 `0.9.0`，配置 surefire、source、javadoc、P3C/PMD profile | `acquiring-openapi-java-sdk/pom.xml` 第 9 行、第 135-198 行 |
| SDK 配置方式 | SDK 使用 `merchant-config.properties` / `merchant-config.example.properties`，配置项包括 `merchant.id`、`merchant.jwt.secret`、密钥文件、公钥/私钥文本、`merchant.openapi.base-url` | `acquiring-openapi-java-sdk/README.md`、`src/main/resources/merchant-config.example.properties` |
| 已有 AGENTS | 后端和前端原先已有 `AGENTS.md`，delivery 和 SDK 原先未发现 | `rg --files -g 'AGENTS.md'` 初始输出 |

### 2.2 需要修正

| 文档位置 | 原结论 | 修正结论 | 依据 |
|---|---|---|---|
| `environment-matrix.md` 第 4 节 | `docs/deployment/nacos` 缺少 `service-checkout-dev.yaml`、`service-payout-dev.yaml` | 本次复核确认每个服务本地 `src/main/resources/application-dev.yml` 都存在；但 Nacos 文档目录中服务专属 DataId 是否缺失仍需按 `docs/deployment/nacos` 再确认 | `find acquiring-orchestration -path '*/src/main/resources/application*.yml'` 输出包含 checkout/payout 的 dev 文件 |
| `build-test-matrix.md` / SDK 规则 | SDK 当前 Java 版本为 Java 8 / `1.8` | 已人工确认 SDK 保持 JDK 1.8 / Java 8 编译目标，以兼容更多商户服务端运行环境；后端服务统一使用 JDK 17 | 用户 2026-07-22 确认；`acquiring-openapi-java-sdk/pom.xml` 第 35-37 行；`acquiring-orchestration/pom.xml` 第 29 行 |
| `repository-map.md` 前端共享包描述 | 前端存在 `packages/components`、`api`、`hooks`、`utils`、`constants`、`types`、`layouts` 等目录 | 这些目录存在，但当前只有 `packages/shared/package.json` 是可确认 workspace 包；不能把其他目录当作已确认构建包 | `find acquiring-frontend/packages -mindepth 2 -maxdepth 2 -name package.json` 只输出 `packages/shared/package.json` |

### 2.3 待人工确认

| 问题 | 原因 | 依据 |
|---|---|---|
| 后端 Maven 运行时是否必须切到 JDK 17 | 已确认其他后端服务统一使用 JDK 17；本机 `mvn -v` 仍显示 Maven runtime Java 26.0.1 | 用户 2026-07-22 确认；`java -version`、`mvn -v` |
| SDK 当前 dirty worktree 是否属于目标基线 | SDK 已存在多处业务代码修改、删除和未跟踪文件；本次只新增 `AGENTS.md` | `git -C acquiring-openapi-java-sdk status --short --branch` |
| 前端主包管理器应固定 npm 还是 pnpm | README/scripts 使用 npm workspaces，但仓库同时存在 `pnpm-workspace.yaml`、`pnpm-lock.yaml` 和 `package-lock.json` | 文件扫描、`acquiring-frontend/package.json` |
| 前端 lint/test 是否要建设 | 当前根 scripts 无 `lint` / `test`，执行对应命令失败 | `npm run lint`、`npm run test` |
| `service-payout` 测试基线 | 后端 test 阶段显示 `service-payout` 无测试可运行 | `mvn -Pdev test` 输出 `No tests to run` |
| 后端 OpenAPI 密钥生成测试日志是否应脱敏或降级 | `mvn -Pdev test` 中 `MerchantOpenApiKeyGenerationPureJavaTests` 输出 PEM 公钥和密钥指纹；未输出私钥正文，但安全日志口径需确认 | `mvn -Pdev test` 输出 |

## 3. AGENTS 和门禁一致性检查

| 检查项 | 结果 | 说明 |
|---|---|---|
| delivery 规则与目录冲突 | 通过 | 规则允许按需创建 `standards/`；本次新增文件均在 `AGENTS.md` 定义目录内 |
| 后端规则与真实项目冲突 | 部分待确认 | JDK 17 与后端 `pom.xml` 一致；但本机 Maven runtime 为 Java 26，已记录为待确认 |
| 前端规则与真实项目冲突 | 通过，存在待确认项 | 三个 app、`packages/shared`、typecheck/build 与真实 scripts 一致；lint/test 缺失已写为记录缺口，不声称可执行 |
| SDK 规则与真实项目冲突 | 通过 | 已人工确认 SDK 保持 JDK 1.8 / Java 8 编译目标；当前 `pom.xml` 与该口径一致 |
| 规则重复或互相矛盾 | 未发现硬冲突 | 后端原有金额、幂等、状态机、安全规则与新增补充方向一致；前端新增规则补足目录/构建/契约，不覆盖原有表格和弹窗规则 |

## 4. 实际执行命令

| 仓库 | 命令 | 结果 | 关键输出 / 失败原因 |
|---|---|---|---|
| 根目录 | `rg --files -g 'AGENTS.md' -g 'pom.xml' -g 'package.json' -g 'vite.config.*' -g 'vue.config.*' -g 'src/main/java/**Application.java' -g 'merchant-config.properties'` | 成功 | 确认现有 `AGENTS.md`、Maven POM、前端 package、Vite 配置、SDK 测试配置 |
| 后端 | `mvn -v` | 成功 | Maven 3.9.16；Maven runtime Java 26.0.1 |
| 根目录 | `java -version` | 成功 | OpenJDK 17.0.19 |
| 后端 | `mvn -Pdev -DskipTests compile` | 成功 | 23 个 Maven reactor 模块全部 `SUCCESS` |
| 后端 | `mvn -Pdev test` | 成功 | 23 个 Maven reactor 模块全部 `SUCCESS`；`service-payout` 无测试可运行；部分 Redis 集成测试 skipped |
| 后端 | `mvn -Pdev -DskipTests package` | 成功 | 23 个 Maven reactor 模块全部 `SUCCESS`；生成各模块 jar / Spring Boot repackage |
| 前端 | `node --version` | 成功 | Node.js v26.0.0 |
| 前端 | `npm --version` | 成功 | npm 11.12.1 |
| 前端 | `pnpm --version` | 成功 | pnpm 11.5.2 |
| 前端 | `npm run` | 成功 | 列出 dev/build/typecheck/format；无 lint/test |
| 前端 | `npm run typecheck` | 成功 | Admin、Hosted Checkout、Merchant、Shared 均完成类型检查 |
| 前端 | `npm run build` | 成功 | Admin、Hosted Checkout、Merchant、Shared 均完成构建；存在 chunk size、Tailwind content、动态导入警告 |
| 前端 | `npm run lint` | 失败 | 退出码 1；`Missing script: "lint"` |
| 前端 | `npm run test` | 失败 | 退出码 1；`Missing script: "test"` |
| SDK | `mvn -v` | 成功 | Maven 3.9.16；Maven runtime Java 26.0.1 |
| SDK | `mvn test` | 成功 | 45 个测试通过，0 failures，0 errors，0 skipped；Java 8 target 在 JDK 26 下有过时和 bootclasspath 警告 |
| SDK | `mvn package` | 成功 | 45 个测试通过；生成 `acquiring-openapi-java-sdk-0.9.0.jar`、sources jar、javadoc jar |
| SDK | `mvn -Pp3c pmd:check` | 成功 | P3C/PMD 通过；有 `ConditionalExpression/@Ternary` deprecated attribute 警告 |

## 5. 成功项

- 后端 `compile`、`test`、`package` 基线命令执行成功。
- 前端 `typecheck`、`build` 基线命令执行成功。
- SDK `test`、`package`、`p3c pmd:check` 基线命令执行成功。
- 4 个仓库级 `AGENTS.md` 已建立或完善。
- 4 个 delivery 统一质量门禁文档已创建。
- 未修改业务代码、数据库或依赖版本。

## 6. 失败项和原因

| 失败项 | 原因 | 建议 |
|---|---|---|
| 前端 `npm run lint` | 根 `package.json` 未定义 `lint` script | 由前端负责人确认是否引入 ESLint 或其他 lint 基线 |
| 前端 `npm run test` | 根 `package.json` 未定义 `test` script | 由前端负责人确认是否引入 Vitest/Jest/Cypress/Playwright 等测试基线 |

## 7. 风险和后续待办

| 风险 | 影响 | 建议 |
|---|---|---|
| Maven runtime Java 26 与后端 JDK 17 基线不一致 | 本地构建虽成功，但运行时与门禁不一致，可能掩盖 JDK 17 环境问题 | 固化 Maven 使用 JDK 17，例如 shell 环境、toolchains 或 CI 镜像 |
| SDK Java 8 target | 已人工确认 SDK 维持 Java 8 编译目标以兼容更多商户服务端 | 后续不要在未确认情况下升级 SDK Java target |
| SDK dirty worktree | 当前测试基于已有未提交业务变更，不能代表远端 master 干净基线 | 确认 SDK 未提交变更归属，再固化版本基线 |
| 后端 `service-payout` 无测试 | 代付核心服务测试基线不足 | 建立最小单元测试和状态/幂等测试基线 |
| 前端缺少 lint/test | 前端质量门禁只能执行 typecheck/build | 确认并建设 lint/test scripts |
| 后端测试日志出现 OpenAPI 密钥生成信息 | 当前未输出私钥正文，但测试日志包含 PEM 公钥和密钥指纹，安全日志策略需明确 | 评审测试日志是否需要默认降噪或脱敏 |
