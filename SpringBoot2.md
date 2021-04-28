# SpringBoot2

## SpringBoot核心技術

### SpringBoot 簡介

SpringBoot出生的意義就是整合龐大的Spring生態圈，能夠快速地建立出生產級別的應用。

> SpringBoot是整合Spring技術棧的一站式框架
>
> SpringBoot是簡化Spring技術棧的快速開發腳手架

#### SpringBoot的優點

+ 創建獨立的 Spring 應用
+ 內建 Web 服務器
+ 自動 starter 依賴，簡化構建配置
+ 自動配置spring以及第三方功能
+ 提供生產級別的監控，健康檢查及外部化配置
+ 無代碼生成，無須編寫XML

#### SpringBoot的缺點

+ 版本迭代速度快，需要時刻關注變化
+ 封裝太深，內部原理複雜，不容易精通

#### 時代背景

##### 微服務

James Lewis and Martin Fowler(2014) 提出微服務的完整概念。https://martinfowler.com/microservices/

+ 微服務是一種架構風格
+ 一個應用拆分為一組小型服務
+ 每個服務運行在自己的進程內，也就是可獨立部署與升級。
+ 微服務之間使用輕量級HTTP交互
+ 服務圍繞業務功能拆分
+ 可以由全自動部署機制獨立部署
+ 去中心化，服務自治。服務可以使用不同的語言，不同的存儲技術。

##### 分布式

分部式的困難：

+ 遠程調用
+ 服務發現
+ 負載均衡
+ 服務容錯
+ 配置管理
+ 服務監控
+ 鍊路追蹤
+ 日誌管理
+ 任務調度
+ …

解決方案：

SpringBoot + SpringCloud 

##### 雲原生

上雲的困難

+ 服務自愈
+ 彈性伸縮
+ 服務隔離
+ 自動化部署
+ 灰度發布
+ 流量治理
+ …

### 如何學習 SpringBoot

#### 官方文檔架構

The reference documentation consists of the following sections:

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Legal](https://docs.spring.io/spring-boot/docs/current/reference/html/legal.html#legal) | Legal information.                                           |
| [Documentation Overview](https://docs.spring.io/spring-boot/docs/current/reference/html/documentation-overview.html#boot-documentation) <br />文檔概覽 | About the Documentation, Getting Help, First Steps, and more. |
| [Getting Started](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started)<br />入門 | Introducing Spring Boot, System Requirements, Servlet Containers, Installing Spring Boot, Developing Your First Spring Boot Application |
| [Using Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot)<br />基礎原理 | Build Systems, Structuring Your Code, Configuration, Spring Beans and Dependency Injection, DevTools, and more. |
| [Spring Boot Features](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features)<br />高級特性 | Profiles, Logging, Security, Caching, Spring Integration, Testing, and more. |
| [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready)<br />監控 | Monitoring, Metrics, Auditing, and more.                     |
| [Deploying Spring Boot Applications](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment.html#deployment)<br />部署 | Deploying to the Cloud, Installing as a Unix application.    |
| [Spring Boot CLI](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-cli.html#cli) | Installing the CLI, Using the CLI, Configuring the CLI, and more. |
| [Build Tool Plugins](https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins.html#build-tool-plugins)<br />插件 | Maven Plugin, Gradle Plugin, Antlib, and more.               |
| [“How-to” Guides](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto)<br />小技巧 | Application Development, Configuration, Embedded Servers, Data Access, and many more. |

The reference documentation has the following appendices:

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties) | Common application properties that can be used to configure your application. |
| [Configuration Metadata](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-configuration-metadata.html#configuration-metadata) | Metadata used to describe configuration properties.          |
| [Auto-configuration Classes](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-auto-configuration-classes.html#auto-configuration-classes) | Auto-configuration classes provided by Spring Boot.          |
| [Test Auto-configuration Annotations](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-test-auto-configuration.html#test-auto-configuration) | Test-autoconfiguration annotations used to test slices of your application. |
| [Executable Jars](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-executable-jar-format.html#executable-jar) | Spring Boot’s executable jars, their launchers, and their format. |
| [Dependency Versions](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-dependency-versions.html#dependency-versions) | Details of the dependencies that are managed by Spring Boot. |

### SpringBoot 入門

#### 系統要求

+ Java8兼容Java14
+ Maven3.3+

## SpringBoot響應式編程