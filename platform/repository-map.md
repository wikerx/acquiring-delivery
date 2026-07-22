# Acquiring Platform 仓库地图

> 本文记录 Acquiring Platform 全球收单平台的仓库基线、技术栈、已有协作文档和目录边界。所有结论均来自本地真实目录、Git 状态、README 或构建配置；无法确认处标记为“待确认”。

## 1. 扫描范围

| 仓库 | 本地目录 | 扫描方式 | 备注 |
|---|---|---|---|
| 后端微服务 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration` | 子代理只读扫描 + 主线程补充读取 `pom.xml`、`README.md`、`AGENTS.md`、`application*.yml`、测试目录 | 未运行构建、测试或服务 |
| 前端 monorepo | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend` | 子代理只读扫描 `package.json`、`AGENTS.md`、README、源码结构、`.env.example` | 未启动 dev server，未安装依赖 |
| OpenAPI Java SDK | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk` | 子代理只读扫描 `pom.xml`、README、docs、源码结构、测试目录 | 未运行 Maven 构建或测试 |
| 交付资料 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-delivery` | 主线程只读检查目录和 Git 状态 | 仅允许写入 `platform/` 文档 |

## 2. Git 基线

| 仓库 | Git 根目录 | 当前分支 | 未提交状态 | 远程仓库 | 依据 |
|---|---|---|---|---|---|
| `acquiring-orchestration` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration` | `feature_scott_payment` | 存在未跟踪目录 `outputs/` | `https://github.com/wikerx/acquiring-orchestration.git` | `git rev-parse --show-toplevel`、`git status --short --branch`、`git remote -v` |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend` | `feature_scott_payment` | 干净 | `https://github.com/wikerx/acquiring-frontend.git` | 子代理执行 `git rev-parse`、`git status`、`git remote -v` |
| `acquiring-openapi-java-sdk` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk` | `master` | 存在修改、删除和未跟踪文件 | `https://github.com/wikerx/acquiring-openapi-java-sdk.git` | 子代理执行 `git status --short --branch`、`git remote -v` |
| `acquiring-delivery` | 待确认 | 待确认 | 待确认 | 待确认 | `git -C /Users/scott/Documents/code/ideaCodex/acquiring/acquiring-delivery rev-parse --show-toplevel` 返回 `fatal: not a git repository`；目录下未发现 `.git` |

## 3. 仓库结构总览

```text
/Users/scott/Documents/code/ideaCodex/acquiring
├── acquiring-orchestration        # 后端微服务仓库
├── acquiring-frontend             # 前端 monorepo
├── acquiring-openapi-java-sdk      # 商户 OpenAPI Java SDK
└── acquiring-delivery
    └── platform                    # 本阶段基线文档输出目录
```

依据：`find /Users/scott/Documents/code/ideaCodex/acquiring -maxdepth 2 -type d -name 'acquiring-*'`、`find /Users/scott/Documents/code/ideaCodex/acquiring/acquiring-delivery -maxdepth 2 -print`。

## 4. 技术栈基线

| 仓库 | 主要语言 | 构建工具 | 已确认版本 | 依据 |
|---|---|---|---|---|
| `acquiring-orchestration` | Java | Maven | 项目配置：Java 17、Spring Boot 3.5.14、Spring Cloud 2025.0.2、Spring Cloud Alibaba 2025.0.0.0、RocketMQ Spring 2.3.5、XXL-JOB 3.4.0、MyBatis-Plus 3.5.16、MySQL Connector/J 8.4.0 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/pom.xml`、`README.md` |
| `acquiring-frontend` | Vue SFC、TypeScript | npm workspaces；同时存在 pnpm workspace 文件 | Vue `^3.5.25`、Vite `^7.2.7`、TypeScript `5.9.3`、Pinia `3.0.4`、Vue Router `^4.6.3`、Element Plus `^2.12.0`、Tailwind CSS `^3.4.17` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/package.json`、各 app `package.json`、`pnpm-workspace.yaml`、`pnpm-lock.yaml` |
| `acquiring-openapi-java-sdk` | Java | Maven | SDK 项目版本 `0.9.0`；Java `1.8`；Jackson 2.15.4、JJWT 0.11.5、JUnit Jupiter 5.10.3、AssertJ 3.26.3 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/pom.xml`、`README.md` |
| `acquiring-delivery` | Markdown | 待确认 | 无独立构建配置 | 目录检查未发现构建文件 |

## 5. 构建工具本机版本

| 工具 | 本机输出 | 备注 |
|---|---|---|
| Maven | Apache Maven 3.9.16 | `mvn -v` 在 `acquiring-orchestration` 下执行 |
| Maven 运行时 Java | 26.0.1 | `mvn -v` 输出 Maven runtime 为 Homebrew OpenJDK 26.0.1，需确认是否符合后端 Java 17 基线 |
| Java CLI | OpenJDK 17.0.19 | `java -version` 输出 |
| Node.js | v26.0.0 | `node --version` 在 `acquiring-frontend` 下执行 |
| npm | 11.12.1 | `npm --version` |
| pnpm | 11.5.2 | `pnpm --version` |

## 6. 当前 AGENTS.md

| 仓库 | AGENTS.md | 已确认内容摘要 | 依据 |
|---|---|---|---|
| `acquiring-orchestration` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/AGENTS.md` | 定义跨境收单支付系统后端协作规范、模块边界、OpenAPI 安全规则、支付/代付核心边界、component/channel-library 禁止事项 | 已读取文件 |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/AGENTS.md` | 指定 Vue 3、TypeScript、Vite、Pinia、Vue Router、Axios；Admin/Merchant 使用 Element Plus；Hosted Checkout 使用 Tailwind CSS | 子代理读取文件 |
| `acquiring-openapi-java-sdk` | 未发现 | 待确认是否需要新增 SDK 专项协作规范 | `find . -name AGENTS.md -print` 无输出 |
| `acquiring-delivery` | 未发现 | 待确认是否需要新增交付文档治理规范 | `find /Users/scott/Documents/code/ideaCodex/acquiring -maxdepth 3 -name AGENTS.md -print` 未列出 delivery |

## 7. README 与已有开发文档

| 仓库 | README / 文档 | 状态 | 依据 |
|---|---|---|---|
| `acquiring-orchestration` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/README.md` | 存在，描述工程结构、技术基线、环境打包、测试入口、OpenAPI 边界 | 已读取文件 |
| `acquiring-orchestration` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/README.md` | 存在文档入口 | `find docs -maxdepth 3` |
| `acquiring-orchestration` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/architecture`、`docs/api`、`docs/standards`、`docs/deployment/nacos`、`docs/sql` | 存在架构、API、规范、Nacos、SQL 资料 | `find /Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs -maxdepth 3` |
| `acquiring-orchestration` | README 中 `docs/git-branching.md`、`docs/coding-standard.md` 等导航 | 部分链接当前未在对应路径发现 | 手动检查 README 导航路径，返回 `missing` |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/README.md` | 存在，描述三套应用、共享包、npm 命令、本地代理和接口边界 | 子代理读取文件 |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/admin-system/README.md` | 存在 | 子代理读取文件 |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/merchant-portal/README*` | 未发现 | 子代理 `rg --files` |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/hosted-checkout/README*` | 未发现 | 子代理 `rg --files` |
| `acquiring-frontend` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/docs/architecture/refactor-roadmap.md`、`docs/security/frontend-auth-hardening.md` | 存在 | 子代理读取文件 |
| `acquiring-openapi-java-sdk` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/README.md` | 存在，描述 Requirements、Installation、Configuration、Payment/Payout/ISO 示例 | 已读取文件 |
| `acquiring-openapi-java-sdk` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/docs/configuration.md`、`docs/security.md`、`docs/logging.md`、`docs/error-handling.md`、`CHANGELOG.md` | 存在 | 子代理扫描 |
| `acquiring-delivery` | `README*`、`AGENTS.md`、既有 Markdown | 未发现 | `find /Users/scott/Documents/code/ideaCodex/acquiring/acquiring-delivery -maxdepth 2` |

## 8. 需要关注的仓库结构信号

| 问题 | 影响 | 依据 |
|---|---|---|
| `acquiring-delivery` 当前不是独立 Git 仓库 | 发布资料版本控制边界待确认 | `git -C acquiring-delivery rev-parse --show-toplevel` 失败 |
| 后端 README 文档导航存在旧路径或缺失路径 | 新人入口可能失效 | README 链接检查显示 `docs/git-branching.md` 等路径 missing |
| 后端存在未跟踪 `outputs/` | 基线文档落盘前后需要避免误判为本次改动 | `git status --short --branch` |
| SDK 存在较多未提交修改和未跟踪文件 | SDK 基线可能处于开发中间态，版本/接口结论需二次确认 | `git status --short --branch` |
| 前端同时存在 npm workspaces 与 pnpm workspace/lock | 主包管理器需要明确 | 根 `package.json`、`pnpm-workspace.yaml`、`pnpm-lock.yaml` |
