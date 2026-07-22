# Acquiring Platform 环境矩阵

> 本文记录本地配置、环境 profile、Nacos DataId、前端环境变量和 SDK 配置方式。敏感值不在本文展开；无法确认的运行时配置标记为“待确认”。

## 1. 后端 Maven profile

| 环境 | Maven profile | `profiles.active` | 依据 |
|---|---|---|---|
| 开发 | `dev` | `dev`，默认激活 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/pom.xml` |
| 测试 | `test` | `test` | 后端根 `pom.xml` |
| UAT | `uat` | `uat` | 后端根 `pom.xml` |
| 生产 | `prod` | `prod` | 后端根 `pom.xml` |

README 说明打包时通过 Maven 资源过滤把 `@profiles.active@` 写入各服务 `application.yml` 的 `spring.profiles.active`。

## 2. 后端服务端口与本地基础配置

| 服务 | 端口 | `spring.application.name` | 本地配置文件 | 备注 |
|---|---:|---|---|---|
| `service-gateway` | 8000 | `service-gateway` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-gateway/src/main/resources/application.yml` | 网关不直接连接数据库、分表、MQ、Seata、XXL-JOB；动态网关规则放到 `service-gateway-{env}.yaml` |
| `service-admin` | 8001 | `service-admin` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-admin/src/main/resources/application.yml` | 含 OpenAPI 商户密钥导出 SDK 版本和 crypto-mode 默认值 |
| `service-merchant` | 8002 | `service-merchant` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-merchant/src/main/resources/application.yml` | 含 OpenAPI 商户密钥导出 SDK 版本和 crypto-mode 默认值 |
| `service-checkout` | 8003 | `service-checkout` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-checkout/src/main/resources/application.yml` | Hosted Checkout 后端 |
| `service-openapi` | 8004 | `service-openapi` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-openapi/src/main/resources/application.yml` | 本地默认 `openapi.security.replay.required=false` |
| `service-payment` | 8005 | `service-payment` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payment/src/main/resources/application.yml` | 本地默认 `payment.risk-client.remote-enabled=false`，评估 URL 为 `http://service-risk/internal/risk/evaluate/payment` |
| `service-payout` | 8006 | `service-payout` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-payout/src/main/resources/application.yml` | 代付服务 |
| `service-job` | 8007 | `service-job` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-job/src/main/resources/application.yml` | 任务调度服务 |
| `service-risk` | 8008 | `service-risk` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/service-risk/src/main/resources/application.yml` | 风控服务 |

依据：逐个读取 `service-*/src/main/resources/application.yml`。

## 3. 后端环境文件覆盖

| 服务 | 已发现环境文件 | 依据 |
|---|---|---|
| `service-admin` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-admin/src/main/resources/application*.yml` |
| `service-merchant` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-merchant/src/main/resources/application*.yml` |
| `service-checkout` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-checkout/src/main/resources/application*.yml` |
| `service-openapi` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-openapi/src/main/resources/application*.yml` |
| `service-payment` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-payment/src/main/resources/application*.yml` |
| `service-payout` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-payout/src/main/resources/application*.yml` |
| `service-job` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-job/src/main/resources/application*.yml` |
| `service-risk` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-risk/src/main/resources/application*.yml` |
| `service-gateway` | `application.yml`、`application-dev.yml`、`application-test.yml`、`application-uat.yml`、`application-prod.yml`、`application-sample.yml` | `find service-gateway/src/main/resources/application*.yml` |

## 4. Nacos 配置矩阵

| 配置类型 | DataId / 文件 | 已确认用途 | 依据 |
|---|---|---|---|
| 公共配置 | `common-dev.yaml` | 公共配置线索 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/common-dev.yaml` |
| 数据库 | `dataSource-dev.yaml` | 动态数据源配置，含 dynamic-datasource 策略类名说明 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/dataSource-dev.yaml` |
| Redis | `redis-dev.yaml` | Redis 配置线索 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/redis-dev.yaml` |
| RocketMQ | `rocketmq-dev.yaml` | `rocketmq.name-server` 默认 `${ROCKETMQ_NAME_SERVER:127.0.0.1:9876}` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/rocketmq-dev.yaml` |
| Seata | `seata-dev.yaml` | Seata registry/config type 为 Nacos，使用 `spring.cloud.nacos.*` 配置 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/seata-dev.yaml` |
| 分表 | `sharding-dev.yaml` | `transaction_date_time` 作为分表字段线索 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/sharding-dev.yaml` |
| 分表评审草案 | `transaction-sharding-dev-draft.yaml` | 文件注释说明用于评审，实际 dev 加载 `sharding-dev.yaml` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/transaction-sharding-dev-draft.yaml` |
| XXL-JOB | `xxl-job-dev.yaml` | XXL-JOB admin 默认 `${XXL_JOB_ADMIN:http://127.0.0.1:8088/xxl-job-admin}` | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos/xxl-job-dev.yaml` |
| 服务配置 | `service-admin-dev.yaml`、`service-gateway-dev.yaml`、`service-job-dev.yaml`、`service-merchant-dev.yaml`、`service-openapi-dev.yaml`、`service-payment-dev.yaml`、`service-risk-dev.yaml` | 各服务 dev 专属配置 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-orchestration/docs/deployment/nacos` |
| 服务配置缺口 | `service-checkout-dev.yaml`、`service-payout-dev.yaml` 未发现 | 需确认是否尚未拆出 DataId，或依赖公共配置即可 | `for s in service-*; test -f docs/deployment/nacos/${s}-dev.yaml` |

除 `service-risk` 的 env 配置只确认导入服务专属与 common 外，多数服务环境配置注释说明会按顺序拉取服务专属、公共、数据库、分表、Redis、MQ、Seata、XXL-JOB 等 Nacos yaml 配置。依据：`service-*/src/main/resources/application-*.yml`。

## 5. 中间件使用线索

| 类型 | 已确认使用方式 | 依据 |
|---|---|---|
| 数据库 / MyBatis | `component-db` 依赖 MyBatis-Plus、dynamic-datasource、MySQL Connector/J；Nacos 有 `dataSource-dev.yaml`；docs/sql 存在 SQL 资料 | `component-db/pom.xml`、根 `pom.xml`、`docs/deployment/nacos/dataSource-dev.yaml`、`docs/sql` |
| Redis | `component-redis` 依赖 `spring-boot-starter-data-redis`；服务依赖 `component-redis`；Nacos 有 `redis-dev.yaml` | `component-redis/pom.xml`、服务 `pom.xml`、`redis-dev.yaml` |
| RocketMQ | `component-mq` 依赖 RocketMQ starter/tools；`RocketMqProducer` 使用 `RocketMQTemplate.syncSend`；Admin/Merchant/Payment 有 `@RocketMQMessageListener` 消费者线索 | `component-mq/pom.xml`、`RocketMqProducer.java`、`AdminOperationLogConsumer.java`、`MerchantOperationLogConsumer.java`、`TransactionMerchantNotificationConsumer.java` |
| 任务调度 | `service-job` 存在 `@Scheduled` 的 `JobTriggerScanner`、`JobTimeoutScanner`、`JobExecutorNodeHeartbeatReporter`；Nacos 有 `xxl-job-dev.yaml`；是否实际使用 XXL-JOB handler 待确认 | `service-job/src/main/java/.../scheduler`、`xxl-job-dev.yaml` |
| Seata | Nacos 有 `seata-dev.yaml`；服务是否显式使用分布式事务注解待确认 | `docs/deployment/nacos/seata-dev.yaml` |
| 配置中心 / 注册中心 | 服务依赖 Nacos discovery/config；环境配置导入 `optional:nacos:*` | 各服务 `pom.xml`、`application-*.yml` |

## 6. 前端环境矩阵

| 应用 | Dev 端口 | 环境变量样例 | 说明 | 依据 |
|---|---:|---|---|---|
| 管理系统 | 5173 | `VITE_ADMIN_API_BASE_URL`、`VITE_ADMIN_DEV_LOGIN_ASSIST`、`VITE_ADMIN_DEV_LOGIN_ACCOUNT`、`VITE_ADMIN_DEV_LOGIN_PASSWORD` | dev login assist 仅本地开发使用，注释要求生产不配置这些 key | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/admin-system/.env.example`、`package.json` |
| 商户系统 | 5174 | `VITE_MERCHANT_API_BASE_URL` | API base URL 样例为空 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-frontend/apps/merchant-portal/.env.example`、`package.json` |
| Hosted Checkout | 5175 | 待确认，未发现 `.env.example` | 当前扫描未发现 checkout 环境样例 | `find acquiring-frontend -maxdepth 3 -name '.env*'` |

## 7. SDK 配置矩阵

| 配置项 | 作用 | 依据 |
|---|---|---|
| `merchant.id` | 平台商户号，写入 JWT `merchantId` claim，并用于请求参数一致性校验 | `/Users/scott/Documents/code/ideaCodex/acquiring/acquiring-openapi-java-sdk/src/main/resources/merchant-config.example.properties`、README |
| `merchant.jwt.secret` | 商户 JWT HS256 签名密钥 | `merchant-config.example.properties`、`MerchantJwtSigner.java` |
| `merchant.platform.public-key-file` | 平台请求公钥 PEM 文件，用于加密请求体 `data`；支持 `classpath:`、`file:`、普通路径 | `merchant-config.example.properties`、`docs/configuration.md` |
| `merchant.response.private-key-file` | 商户响应私钥 PEM 文件，用于解密成功响应 `data` | `merchant-config.example.properties`、`docs/configuration.md` |
| `merchant.platform.public-key` | 文本模式兼容旧配置 | `merchant-config.example.properties` |
| `merchant.response.private-key` | 文本模式兼容旧配置 | `merchant-config.example.properties` |
| `merchant.openapi.base-url` | OpenAPI 网关基础地址，SDK 拼接 `/api/rest/{domain}/{version}/**` | `merchant-config.example.properties`、README |

SDK 安全协议已确认：HTTP POST、`Authorization: Bearer <jwt>`、JWT HS256、默认 JWT TTL 180 秒、请求 `data` 加密、成功响应 `data` 解密、`RSA-OAEP-256` + `A256GCM`、密钥文件优先于 inline 文本。依据：子代理扫描 `OpenApiClient.java`、`OpenApiConstants.java`、`MerchantJwtSigner.java`、`OpenApiPayloadCrypto.java`、`RsaKeyUtils.java`、`docs/security.md`。

## 8. 环境待确认项

| 待确认项 | 原因 | 建议确认人/来源 |
|---|---|---|
| `service-checkout-dev.yaml`、`service-payout-dev.yaml` 是否应存在 | Maven 声明服务模块，但 Nacos dev 配置目录未发现对应服务专属 DataId | 后端负责人 / Nacos 配置负责人 |
| 生产环境 `openapi.security.replay.required` 是否强制开启 | 本地 `service-openapi/application.yml` 默认 false，并注释生产必须开启 | OpenAPI 安全负责人 |
| `payment.risk-client.remote-enabled` 生产配置 | 本地默认 false，注释要求生产替换 Noop 风控 | 支付/风控负责人 |
| Hosted Checkout 环境变量规范 | 未发现 `.env.example` | 前端负责人 |
| SDK 真实发布仓库和发布凭证流程 | 未发现 `distributionManagement` 或发布脚本 | SDK 负责人 |
