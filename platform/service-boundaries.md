# Acquiring Platform 服务边界

> 本文记录后端服务、公共组件、渠道库、三套前端和 OpenAPI Java SDK 的边界。职责描述仅采信 README、AGENTS、`pom.xml`、启动类、配置类和明确客户端代码。

## 1. 后端 Maven 模块树

```text
acquiring-orchestration
├── component-library
│   ├── component-core
│   ├── component-excel
│   ├── component-web
│   ├── component-security
│   ├── component-http
│   ├── component-db
│   ├── component-redis
│   ├── component-mq
│   └── component-job
├── channel-library
│   ├── payment-channel-library
│   └── payout-channel-library
├── service-gateway
├── service-admin
├── service-merchant
├── service-checkout
├── service-openapi
├── service-payment
├── service-risk
├── service-payout
└── service-job
```

依据：`/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/pom.xml`、`component-library/pom.xml`、`channel-library/pom.xml`。

## 2. 后端服务边界

| 服务 | 端口 | 启动类 | 职责 | 主要依赖 | 调用关系线索 | 依据 |
|---|---:|---|---|---|---|---|
| `service-gateway` | 8000 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-gateway/src/main/java/com/scott/payment/gateway/GatewayApplication.java` | 网关服务，外部请求入口；README 描述商户侧链路经 gateway 到 openapi | Spring Cloud Gateway WebFlux、Nacos discovery/config、LoadBalancer、`component-core` | README 描述 `merchant/client -> service-gateway -> service-openapi -> service-payment/service-payout` | `service-gateway/pom.xml`、`application.yml`、README |
| `service-admin` | 8001 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-admin/src/main/java/com/scott/payment/admin/AdminApplication.java` | 管理后台服务，包含系统管理、交易管理、监控治理、汇率管理等管理端接口线索 | `component-core`、`component-excel`、`component-web`、`component-security`、`component-db`、`component-redis`、`component-mq`、`component-job` | 调用 `service-payment` 内部交易/退款/撤销/日志查询；调用 `service-job` 内部任务/日志/分表治理接口 | `AGENTS.md`、`service-admin/pom.xml`、`PaymentInternalClientProperties.java`、`JobSchedulerClientProperties.java` |
| `service-merchant` | 8002 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-merchant/src/main/java/com/scott/payment/merchant/MerchantApplication.java` | 商户后台服务，AGENTS 要求后续风格对齐 admin；当前有商户后台发起退款等动作入口线索 | `component-core`、`component-web`、`component-db`、`component-security`、`component-mq`、`component-redis`、Mail、MapStruct | 调用 `service-payment` 内部退款接口 | `AGENTS.md`、`service-merchant/pom.xml`、`PaymentInternalClient.java`、`PaymentInternalClientProperties.java` |
| `service-checkout` | 8003 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-checkout/src/main/java/com/scott/payment/checkout/CheckoutApplication.java` | Hosted Checkout 收银台后端服务；已确认 Country Config 应用服务线索 | `component-core`、`component-web`、`component-db`、`component-security`、`component-redis` | 深层调用关系待确认 | README、`service-checkout/pom.xml`、`CheckoutCountryConfigApplicationService.java` |
| `service-openapi` | 8004 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-openapi/src/main/java/com/scott/payment/openapi/OpenApiApplication.java` | 商户开放接口入口：JWT 鉴权、请求解密、参数校验、防重放、响应加密、商户通知和渠道回调入口 | `component-core`、`component-web`、`component-security`、`component-redis`、`component-mq`、`component-db`、`component-http`、MapStruct | 调用 `service-payment` 和 `service-payout` 内部接口；README 描述开放接口统一进入该服务 | `AGENTS.md`、README、`service-openapi/pom.xml`、`PaymentInternalClient.java`、`PayoutInternalClient.java` |
| `service-payment` | 8005 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payment/src/main/java/com/scott/payment/payment/PaymentApplication.java` | 收单支付核心服务；AGENTS 指出当前部分实现仍偏骨架或模拟，不应误判为完整交易核心 | `component-core`、`component-web`、`component-db`、`component-redis`、`component-mq`、`component-security`、`payment-channel-library` | 调用 `service-risk` 做支付路由前风控评估；向商户通知地址发起 HTTP 通知；消费商户通知 MQ | `AGENTS.md`、README、`service-payment/pom.xml`、`RiskInternalRestClient.java`、`MerchantNotificationConfig.java`、`TransactionMerchantNotificationConsumer.java` |
| `service-payout` | 8006 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payout/src/main/java/com/scott/payment/payout/PayoutApplication.java` | 代付核心服务；AGENTS 指出当前代付实现偏模拟，不允许继续堆完整代付逻辑 | `component-core`、`component-web`、`component-db`、`component-redis`、`component-mq`、`component-security`、`payout-channel-library` | 被 `service-openapi` 调用；更深调用关系待确认 | `AGENTS.md`、`service-payout/pom.xml`、`PayoutTransactionStatusEnum.java` |
| `service-job` | 8007 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-job/src/main/java/com/scott/payment/job/JobApplication.java` | 轻量级任务调度中心与定时任务服务，包含任务扫描、节点心跳、分表建表、商户通知重试等线索 | `component-core`、`component-job`、`component-web`、`component-db`、`component-redis`、Spring Web、MapStruct | 调用 `service-payment` 内部补偿接口；被 `service-admin` 管理端调用 | README、`service-job/pom.xml`、`PaymentInternalClientProperties.java`、`JobTriggerScanner.java`、`JobTimeoutScanner.java` |
| `service-risk` | 8008 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-risk/src/main/java/com/scott/payment/risk/RiskApplication.java` | 风控服务，包含风险评估应用服务、风控决策枚举、风控原因码 | `component-core`、`component-web`、Nacos discovery/config | 被 `service-payment` 调用做支付风控评估 | `service-risk/pom.xml`、`RiskEvaluationApplicationServiceTests.java`、`RiskDecisionEnum.java`、`RiskInternalRestClient.java` |

## 3. 公共组件边界

| 模块 | 职责 | 禁止/边界 | 依据 |
|---|---|---|---|
| `component-core` | 统一返回模型、基础异常、认证上下文、密码与 token 工具、基础 ID/脱敏工具线索 | 不承载具体支付交易业务 | `AGENTS.md`、`component-core/pom.xml`、测试目录 |
| `component-web` | Web 通用配置、统一异常处理、操作日志切面、内部鉴权拦截 | Controller 业务规则不应下沉到组件 | `AGENTS.md`、`component-web/pom.xml`、`GlobalExceptionHandlerTest.java`、`InternalServiceAuthInterceptorTest.java` |
| `component-db` | MyBatis Plus 公共实体/Mapper、认证与 RBAC 支撑、ISO 字典、动态数据源/分表基础能力 | Mapper/Repository 只做数据访问 | `AGENTS.md`、`component-db/pom.xml`、分表测试目录 |
| `component-security` | OpenAPI JWT、签名、加密、密钥、防重放工具 | 不修改外部签名、加密规则；不得绕过安全注解 | `AGENTS.md`、`component-security/pom.xml`、`OpenApiMerchantKeyMaterialServiceTest.java` |
| `component-redis` | Redis 基础封装、Redis ID 生成、服务时间线索 | 不承载业务状态机 | `component-redis/pom.xml`、Redis 测试目录 |
| `component-mq` | RocketMQ 基础封装、生产者能力 | 不承载业务消费规则 | `component-mq/pom.xml`、`RocketMqProducer.java` |
| `component-http` | HTTP 基础封装，依赖 Hutool HTTP | 不承载渠道/商户私有业务 | `component-http/pom.xml` |
| `component-job` | 任务调度共享契约基础封装 | 具体任务属于 `service-job` | `component-job/pom.xml`、`AGENTS.md` |
| `component-excel` | Excel 导出能力 | 业务导出字段由业务服务控制 | `component-excel/pom.xml`、`ExcelExportServiceImplTest.java` |

未发现名为 `common` 的 Maven 模块；当前跨服务共享能力主要落在 `component-library`。依据：根 `pom.xml` 和 `find ... -name pom.xml`。

## 4. 渠道库边界

| 模块 | 职责 | 被谁使用 | 边界 | 依据 |
|---|---|---|---|---|
| `payment-channel-library` | 收单支付渠道适配抽象和渠道模型，已确认 MPGS、WorldPay 测试线索 | `service-payment` | AGENTS 禁止把平台交易状态机写入渠道库，渠道响应需映射为平台统一状态 | `channel-library/pom.xml`、`payment-channel-library/pom.xml`、测试目录 |
| `payout-channel-library` | 代付渠道适配抽象和模型 | `service-payout` | 禁止写商户业务规则、平台状态机或后台管理规则 | `channel-library/pom.xml`、`payout-channel-library/pom.xml`、`PayoutChannelRequest.java` |

## 5. 前端边界

| 前端应用 | 目录 | 入口 | 技术栈 | 请求层 | 路由/权限/状态 | 依据 |
|---|---|---|---|---|---|---|
| 管理系统 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/admin-system` | `src/main.ts` | Vue 3、Vite、TypeScript、Pinia、Vue Router、Element Plus | `src/api/http.ts` 使用 `@acquiring/shared` 的 `createHttpClient`；另有旧封装 `src/utils/request.ts` | `src/router/index.ts` 静态路由 + 后端 `AuthMenu` 动态路由；`directives/permission.ts` 注册 `v-hasPermi`、`v-hasRole`；Pinia modules 包含 `user`、`permission`、`app`、`settings`、`tagsView` | 子代理扫描 |
| 商户系统 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/merchant-portal` | `src/main.ts` | Vue 3、Vite、TypeScript、Pinia、Vue Router、Element Plus | `src/api/http.ts` 使用 `createHttpClient` | `src/router/index.ts` 静态 home/profile/system/transaction/openapi 页面 + 后端菜单动态路由；`src/utils/permission.ts` 提供权限判断；Pinia stores 包含 `authStore`、`settingsStore` | 子代理扫描 |
| 收银台 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/hosted-checkout` | `src/main.ts` | Vue 3、Vite、TypeScript、vue-i18n、Tailwind CSS | `src/api/checkoutConfig.ts` 直接使用 `axios.get('/checkout/config/countries')` | 未发现 `src/router` 或 `createRouter`；未使用 Pinia；`App.vue` 使用本地 `ref/computed` 和 localStorage 保存 locale/country | 子代理扫描 |
| 共享包 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/packages/shared` | `src/index.ts` | TypeScript、Axios、Vue 组件 | 导出 `createHttpClient`、`unwrapResult`、通用响应/Auth 类型 | 导出品牌配置和支付 Logo 组件 | `packages/shared/package.json`、子代理扫描 |

已确认三套前端均依赖 `@acquiring/shared: 0.1.0`，Vite alias 指向 `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/packages/shared/src/index.ts`。是否存在更深层业务组件复用：待确认。

## 6. SDK 边界

| 包/模块 | 路径 | 职责 | 依据 |
|---|---|---|---|
| SDK 入口 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/OpenApiClient.java` | 客户端创建、POST 加密请求、响应转换、本地参数校验 | 子代理扫描、README |
| 配置 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/config` | 常量、配置加载、密钥文件加载 | 子代理扫描 |
| 加密 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/crypto` | RSA 密钥解析、请求/响应 payload 加解密 | 子代理扫描、`docs/security.md` |
| JWT | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/jwt/MerchantJwtSigner.java` | 商户 JWT HS256 签名 | 子代理扫描 |
| HTTP | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/http` | `HttpTransport` 抽象与 Java 8 `HttpURLConnection` 实现 | 子代理扫描 |
| JSON | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/json` | Jackson JSON 支持 | `pom.xml`、子代理扫描 |
| 日志脱敏 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/logging/OpenApiLogSanitizer.java` | 商户号、卡号、账户号、token、key 脱敏 | 子代理扫描、`docs/logging.md` |
| 模型 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/java/com/scott/openapi/sdk/model` | payment、payout、iso、common 请求/响应模型 | 子代理扫描 |

## 7. SDK 对外接口能力

| SDK 方法 | OpenAPI 路径 | 依据 |
|---|---|---|
| `createPayment` | `/api/rest/payment/v1/payment` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `createAuthorization` / `authorizePayment` | `/api/rest/payment/v1/authorization` | SDK README、`OpenApiClient.java`、`PaymentApiTest.java` |
| `createPreAuthorization` | `/api/rest/payment/v1/pre-authorization` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `createIncrementalAuthorization` | `/api/rest/payment/v1/incremental-authorization` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `capture` | `/api/rest/payment/v1/capture` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `refund` | `/api/rest/payment/v1/refund` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `voidPayment` | `/api/rest/payment/v1/void` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `queryPayment` | `/api/rest/payment/v1/query` | SDK README、`OpenApiConstants.java`、`PaymentApiTest.java` |
| `createPayout` | `/api/rest/payout/v1/create` | `OpenApiConstants.java`、`PayoutCreateApiTest.java` |
| `queryCountries` | `/api/rest/iso/v1/countries/query` | `OpenApiConstants.java`、`IsoCountryQueryApiTest.java` |
| `queryCurrencies` | `/api/rest/iso/v1/currencies/query` | `OpenApiConstants.java`、`IsoCurrencyQueryApiTest.java` |
| `authorizePaymentV2` | 调用 `createAuthorization("v2", request)`，并标记 `@Deprecated` | `OpenApiClient.java` |
