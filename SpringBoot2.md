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

本章節內容介紹官方文檔中 Getting started 章節。使用 SpringBoot 2.4.5 GA。 

#### 系統要求

在 Getting started 章節中找到 System Requirements 小節，詳細說明該版本所需的系統要求。

2.4.5GA 的系統要求：

+ Java 8 ~ Java16
+ Maven3.3 +
+ Tomcate 9

#### HelloWorld

在 Getting started 章節中找到 Developing Your First Spring Boot Application 小節，詳細介紹如何開始你第一個 SpringBootu 應用。

+ 建立maven專案

+ POM配置

  + 導入父工程

    ``` xml
    <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.4.5</version>
        </parent>
    ```

  + 添加starter依賴

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
    ```

+ 編寫 SpringBoot 主程序

  這裡認識的第一個 SpringBoot 註解：`@SpringBootApplication`，作用是標註該類為SpringBoot應用

  ```java
  /**
   * 主程序類
   */
  @SpringBootApplication
  public class MainApplication {
      
      public static void main(String[] args) {
          SpringApplication.run(MainApplication.class, args);
      }
  }
  ```

+ 編寫Controller 處理請求

  ```java
  /*
   * 方法返回值要放入響應體中可以使用 @ResponseBody 註解修飾方法
   * 如果 Controller 下所有方法都是將返回值加入響應體中
   * 可以將 @RequestBody 註解使用在類上，可以達到同樣的效果
   * 而 springboot 新註解 @RestController，就是 @Controller 和 @ResponseBody 合體
   * 在類上使用 @RestController 可以直接取代上面兩個註解
   */
  
  
  //@Controller
  //@ResponseBody
  @RestController // 替代上面兩個註解
  public class HelloController {
  
      @RequestMapping("/hello")
      public String sayHi(){
          return "Hello SpringBoot 2";
      }
  }
  ```

  > 控制器必須和主程序在同層或是子層，在外層主程序的組件掃描會掃不到

+ 運行 main 方法測試(啟動SpringBoot)

#### 整合配置

SpringBoot誕生就是為了整合整個Spring生態圈，SpringBoot將所有配置都抽取在一個`Application.properties`配置文件中，裡面可以配置所有使用到的Spring框架，包含 Tomcat。而那些

可以在官方文檔的 [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)  找到 SpringBoot可使用的所有配置。

例如：修改 Tomcat 的端口號

``` properties
server.port=8888
```

#### 簡化部署

在 Getting started 章節中找到 Creating an Executable Jar 小節，詳細介紹如何建立可執行`jar`，進行部署。`jar`裡面包含了所有運行環境，所以可以直接運行在服務器上。

+ Maven引入插件

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  
  ```

+ 使用Maven打包成`jar`

+ 通過 `java -jar 檔案名稱` 運行

#### 自動配置原理

##### 依賴管理

+ 父項目做依賴管理

  ``` xml
  <!-- 我們的父POM -->
  <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.4.5</version>
  </parent>
  ```

+ 開發導入starter場景啟動器

  官方文檔的 Using Spring Boot 章節的 Starters 小節，內有詳細說明。

  starter 是一組依賴集合的描述，在開發時只要引入某個starter，就相當於將這組依賴都進行引入。SpringBoot官方的starters都是以`spring-boot-starter-*`，`*`表示某種場景，官方文檔內有所有starter的訊息。

  第三方的的starter通常以`*-spring-boot-starter`方式命名。

+ 無須關注版本號，自動版本仲裁

  點開父POM `spring-boot-starter-parent`，可以發現它裡面也有個父POM  `spring-boot-dependencies`，裡面幾乎定義了開發中常用的所有依賴的版本號，所以SpringBoot在引入依賴時可以不用寫版本號，因為SpringBoot已經定義好。

  **如果項目使用的依賴沒有在 `spring-boot-starter-parent`中有設定，依舊需要給定版本號**

  ``` xml
  <!-- 父POM的父POM -->
  <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.4.5</version>
  </parent>
  ```

+ 可以修改版本號

  方式一：maven的屬性就近原則

  `spring-boot-dependencies` 使用許多變數定義依賴所使用的版本訊息，如果不想使用默認的依賴版本，可以在自己SpringBoot項目中定義相同名稱的變數，指定需要的版本號。
  
  ```xml
  操作步驟：
  1. 查看spring-boot-dependencies裡面定義依賴版本使用的 key
  2. 在當前項目裡面重寫配置
  
  <properties>
      <mysql.version>5.1.43</mysql.version>
  </properties>
  ```
  
  方式二：maven的依賴就近原則
  
  直接在依賴指定具體的版本
  
  ``` xml
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.23</version>
  </dependency>
  ```
  
  

##### 自動配置

+ 自動配好 Tomcat
+ 自動配好 SpringMVC
  + 引入 SpringMVC全套組件
  + 自動配好 SpringMVC常用組件
+ 自動配好 Web 常見問題，如：字符編碼問題
+ 默認的包結構
  + 默認規則：主程序所在的包及其下面所有的子包裡面的組件都會被掃描進來
  + 想要改變默認掃描路徑，可以通過`@SpringBootApplication`的`scanBasePackages`屬性指定，或者使用`@ComponentScan`指定掃描路徑
+ 各種配置擁有默認值
  + 默認配置最終都是映射類上
  + 配置文件的值最終會綁定到每個類上，這個類會在容器中創建對象
+ 按需加載所有配置項
  + 引入那些場景這些場景的自動配置才會開啟
  + SpringBoot所有的自動配置功能都在`spring-boot-autoconfigure`裡面
+ …

### 容器功能

#### 組件添加 

##### @Configuration

+ 作用：聲明為配置類，配置類是用來取代xml配置文件的

+ 使用位置：類上

+ 屬性：

  + proxyBeanMethods：boolean值，是否代理該配置類下 `@Bean` 修飾的方法。可分為 Full 模式( True )與 Lite 模式( False )。
    `true`時，方法將被代理，`@Bean`方法被調用時，會先去IOC容器中找尋是否已存在該Bean，沒有才會調用方法創建。

    `false`時，方法不會被代理，`@Bean`方法調用時，總是執行方法返回新的Bean。

    > @Configuration標註的配置類，也會被放置到IOC容器中，可從容器中獲取配置類查看`proxyBeanMethods`屬性的變化，配置類是否被代理

+ 最佳實戰：

  + 配置類組件之間沒有依賴關係，使用Lite模式加速容器啟動，減少去容器查找的判斷。
  + 配置類組間之間有依賴關係，使用Full模式確保依賴的是單例模式。

+ 範例：User 與 Pet 有依賴關係，使用Full模式保證每次調用 `getPet` 方法都是獲取同一個 Bean

  ``` java
  @Configuration(proxyBeanMethods = true)
  public class MyConfig {
  
      @Bean
      public User getUser(){
          // user 依賴 pet
          User user = new User("James", "james@xxx.xxx", getPet());
          return user;
      }
  
      @Bean
      public Pet getPet(){
          return new Pet("godzilla");
      }
  }
  ```

  > `proxyBeanMethods`為`false`時，上面對 `getPet`方法的調用IDEA也會提醒你設置為`true`，或者使用`@Autowired`注入Pet。

##### @Bean

+ 作用：標示方法為創建Bean的方法，Spring會在容器啟動時自動調用，將返回值放入IOC容器。
+ 使用位置：方法上
+ 屬性：
  + value：指定Bean Id，如果**不指定默認使用方法名稱**。

##### @Component、@Controller、@Service、@Repository

+ 作用：都是標註該類為組件(Bean)，容器創建時會將有這些註解的類放入容器中，必須有無參構造器。
+ 使用位置：類上

> @Component、@Controller、@Service、@Repository 作用都相同，只是為了更好區分Bean的作用所以才分為 Controller、Service、Repository，如果無法分類，可以使用 Component。

##### @Import

+ 作用：@Configuration(配置類)、ImportSelector、ImportBeanDefinitionRegistrar或常規的組件類
+ 使用位置：配置類或組件類上(@Configuration、@Component、@Controller等)
+ 屬性：
  + value：指定類型。

ImportSelector 是接口，自定義導入選擇器可以實現該接口，接口定義了以下方法：

+ String[] selectImports(AnnotationMetadata importingClassMetadata) 

  方法返回要導入組件全類名數組，方法形參 importingClassMetadata 獲取當前類(標註@Import)的所有註解訊息

ImportBeanDefinitionRegistrar 是接口，自定義組件註冊器可以實現該接口，接口定義了以下方法：

+ void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry)

  Spring會調用自定義組件註冊器的該方法註冊Bean，該方法有兩個形參：

  + importingClassMetadata：獲取當前類(標註@Import)的所有註解訊息
  + registry：註冊器，可以調用該註冊器將組件註冊在IOC容器中。

> 配置類其實也是一個組件，所以也會被註冊IOC容器

##### @ComponentScan

+ 作用：組件掃描的規則

+ 使用位置：配置類上

+ 屬性：

  + value：指定基礎包
  + useDefaultFilters：是否啟用默認規則，默認將@Component、@Controller、@Service、@Repository註解標註的組件全加入IOC容器
  + includeFilters：指定**只包含規則**，欲使用該功能必須將`useDefaultFilters`設置為`false`。該屬性接收 `@Filter` 註解，每個 `@Filter`代表一個過濾規則，可以指定多個，先後順序有影響。
  + excludeFilters：指定**排除規則**，該屬性接收 `@Filter` 註解，每個 `@Filter`代表一個過濾規則，可以指定多個，先後順序有影響。



@Filter

+ 作用：指定一種過濾規則
+ 使用位置：`@ComponentScan` 的 `includeFilters` 和 `excludeFilters` 屬性。
+ 屬性：
  + type：FilterType 類型，指定是按照什麼類型進行過濾。有以下幾種：
    + `FilterType.ANNOTATION`：按照註解。
    + `FilterType.ASSIGNABLE_TYPE`：按照給定類型，包含其子類或實現類。
    + `FilterType.ASPECTJ`：按照ASPECTJ表達式。
    + `FilterType.REGEX`：按照正則表達式。
    + `FilterType.CUSTOM`：自定義過濾類型，實現TypeFilter接口。
  + classes：指定過濾類型的值，供過濾類型ANNOTATION、ASSIGNABLE_TYPE、CUSTOM使用
  + pattern：指定過濾類型的值，供過濾類型ASPECTJ、REGEX使用

> @SpringBootApplication註解中就包含了@ComponentScan所以兩者不可以一起使用

##### @Conditional

+ 作用：條件裝配，當滿足條件時配置才生效

+ 使用位置：方法或類上

  > 使用在類上時，就是對該類下所有方法都套用此條件裝配

`@Conditional` 是一個根註解，它派生許多子註解，下面列出常見的：

+ @ConditionalOnBean：當容器中存在指定的Bean時，才進行配置
+ @ConditionalOnMissBean：當容器不存在指定的Bean時，才進行配置
+ @ConditionalOnClass：當類路徑下存在指定Class時，才進行配置
+ @ConditionalOnMissClass：當類路徑下不存在指定Class時，才進行配置
+ @ConditionalOnResource：項目的類路徑下存在某個資源時，才進行配置
+ @ConditionalOnJava：是指定Java版本時，才進行配置
+ @ConditionalOnWebApplication：當應用是Web應用時，才進行配置
+ @ConditionalOnNotWebApplication：當應用不是Web應用時，才進行配置
+ @ConditionalOnProperties：當配置文件配置某個屬性時，才進行配置

範例：使用`@ConditionalOnBean`舉例

``` java
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    @Bean("pet")
    public Pet getPet() {
        return new Pet("godzilla");
    }

	// 當Pet存在IOC容器時，才進行該方法的配置
    @ConditionalOnBean(Pet.class)
    @Bean("user")
    public User getUser() {
        // user 依賴 pet
        User user = new User("James", "james@xxx.xxx", getPet());
        return user;
    }

}
```

#### 原生配置文件引入

##### @ImportResource

+ 作用：導入外部spring XML配置文件

+ 使用位置：配置類上

+ 屬性：

  + value：指定配置文件路徑

+ 範例：

  類路徑下的 ApplicationContext.xml 文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
      <bean id="pet2" class="org.learning.boot.entity.Pet">
          <property name="name" value="Peter"/>
      </bean>
  </beans>
  ```

  ``` java
  @Configuration(proxyBeanMethods = true)
  @ImportResource("classpath:applicationContext.xml")
  public class MyConfig {}
  ```

#### 配置綁定

##### @ConfigurationProperties

該註解需要與其他註解搭配使用才有效果，常見於SpringBoot的底層。

+ 作用：將SpringBoot集中配置文件`application.properties`中內容，綁定至Bean屬性中。
+ 使用位置：類上
+ 屬性：
  + prefix：指定`application.properites`文件中 key 的前綴

兩種方式使用配置綁定功能：

1. `@Component` + `@ConfigurationProperties`

   任何要使用Spring的強大功能，都必須要在IOC容器中，所以可以給 Bean 加上註解註冊進IOC容器中。

   ```java
   @ConfigurationProperties(prefix = "car")
   @Component
   public class Car {
       private String brand;
       private Long price;
   
       // 省略 getter、setter、toString方法
   }
   ```

   application.properties文件內容

   ```properties
   server.port=8888
   car.price=1000000
   car.brand=SYM
   ```

2. `@EnableConfigurationProperties` + `@ConfigurationProperties`

   `@EnableConfigurationProperties` 有兩個功能：啟動配置綁定功能指定屬性配置的Class、把指定的Class加入註冊到容器中。

   目標Class使用`@ConfigurationProperties`指定要讀取文件key的前綴

   ```java
   @ConfigurationProperties(prefix = "car")
   public class Car {
       private String brand;
       private Long price;
   
       // 省略 getter、setter、toString方法
   }
   ```

   ``` java
   @Configuration(proxyBeanMethods = true)
   @ImportResource("classpath:applicationContext.xml")
   @EnableConfigurationProperties(Car.class)
   public class MyConfig {
       
   }
   ```

   application.properties文件內容

   ```properties
   server.port=8888
   car.price=1000000
   car.brand=SYM
   ```



### 自動配置原理入門

#### 引導加載自動配置類

@SpringBootApplicat = @SpringBootConfiguration + @EnableAutoConfiguration + ComponentScan

@SpringBootApplicat 是一個三合一註解，想要理解自動配置的原理，需要一個個進去了解。

##### @SpringBootConfiguration

``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
}
```

註解定義來看，@SpringBootConfiguration 就是一個 @Configuration，所以SpringBoot主程序其實也是配置類，我們習慣上稱呼其為「核心配置類」。

##### @ComponentScan

指定組件掃描的基礎包

> 當沒有配置值時，默認的掃描路就是該類所在包與其子包以下，這解釋了@Component 及 其他組件註解要放到主程序所在包底下

##### @EnableAutoConfiguration

開啟自動配置，SpringBoot的自動配置就是使用此註解開啟

``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
}
```

###### @AutoConfigurationPackage：

自動配置包是什麼東西？可以查看其註解定義：發現它其實就是個 Import

``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
	// 利用 Register 給容器導入BasePackages組件
    // 內部紀錄基礎路徑，以後可以編寫自動配置類時可以通過AutoConfigurationPackages.get方法獲取所有基礎路徑
}
```

可以看到它其實是Import了`AutoConfigurationPackages.Registrar.class`，查看該類做了哪些事：

``` java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
        register(registry, new PackageImports(metadata).getPackageNames().toArray(new String[0])); 
    }

    @Override
    public Set<Object> determineImports(AnnotationMetadata metadata) {
        return Collections.singleton(new PackageImports(metadata));
    }

}
```

可以知道它是一個靜態內部類，通過debug可以知道`getPackageNames()`得到的就是標註了`@SpringBootApplication`所在的包名，並將該基礎包路徑保存下來，作為讀取配置類的依據。

>  這也解釋了為什麼將**配置類**放到主程序所在包或其子包下。

###### @Import(AutoConfigurationImportSelector.class)

 `@Import`可以通過實現ImportSelector接口，來自定義自己的導入規則，查看AutoConfigurationImportSelector類`selectImports`方法，該方法會返回所有要導入組件名稱的數組，而所有的組件名都是調用`getAutoConfigurationEntry`得到的。

```java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```

查看`getAutoConfigurationEntry`方法內部，預設加載的組件是由`getCandidateConfigurations`(獲取候選配置)方法得到，接下來對這些默認的配置進行去重複、排除、過濾，最終返回要加載的組件(配置)。

``` java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    }
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    // 獲取候選配置
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    // 去重複
    configurations = removeDuplicates(configurations);
    // 排除
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    configurations.removeAll(exclusions);
    // 過濾
    configurations = getConfigurationClassFilter().filter(configurations);
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
```

查看獲取候選配置方法 `getCandidateConfigurations` ，分析它是從哪裡得到默認配置。從以下源碼可以得知候選配置是由 `SpringFactoriesLoader`的靜態方法`loadFactoryNames`得到，再深入研究。

``` java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    // 獲取候選配置
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
                                                                         getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

注意`loadSpringFactories`方法，是從該方法獲取默認配置

``` java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoaderToUse == null) {
        classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }
    String factoryTypeName = factoryType.getName();
    // 返回候選配置
    return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

終於找到候選配置是從哪裡讀取，在類路徑下的`META-INF/spring.factories`文件，我們剛剛得到候選配置為130個，`spring-boot-autoconfigure` jar下就有該文件，且 org.springframework.boot.autoconfigure.EnableAutoConfiguration 下剛剛好就是有130個項目。

> 第三方框架提供starter場景，也是通過該機制，來加載自動配置類。而我們自定義starter 也是使用這樣的方式。

結論：**<font color="ff0000">SpringBoot默認加載starter場景spring.factories指定的配置類</font>**，而這些配置類通過**條件裝配**機制(@Conditional註解)，根據條件決定該配置類是否生效，更多訊息會下章節介紹。

``` java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
		Map<String, List<String>> result = cache.get(classLoader);
		if (result != null) {
			return result;
		}

		result = new HashMap<>();
		try {
            // 默認配置文件，類路徑下META-INF/spring.factories
			Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
            // 以下省略...

```

``` java

#### 按需加載

雖然SpringBoot所有自動配置默認啟動時全部加載，但是SpringBoot通過@Conditional註解來條件配置，只有所依賴的jar被導入時，這個自動配置才會生效。以下的 `AopAutoConfiguration` 就是很好的例子
    
@Configuration(proxyBeanMethods = false)
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass(Advice.class)
	static class AspectJAutoProxyingConfiguration {

		@Configuration(proxyBeanMethods = false)
		@EnableAspectJAutoProxy(proxyTargetClass = false)
		@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false",
				matchIfMissing = false)
		static class JdkDynamicAutoProxyConfiguration {

		}
	// 以下省略部分源碼
}
```



#### 總結

+ SpringBoot先加載所有自動配置類 `*AutoConfiguration`
+ 每個自動配置類按照條件決定是否生效，默認都會綁定`*Properties`類的值。而`*Properties`和配置文件進行了綁定。
+ 生效的自動配置類就會給ioc容器裝配許多組件
+ 只要容器中有這些組件，相當於這些功能啟動
+ 訂製化配置
  + 用戶向容器添加底層組件，自動配置類會以用戶配置為優先
  + 用戶去自動配置類看組件是綁定配置文件的哪個值，直接修改配置文件

### 最佳實踐

+ 引入場景依賴
+ 查看自動配置了那些(選做)
  + 自己分析，引入場景對應的自動配置一般都生效
  + 配置文件中 debug=true 開啟自動配置報告。Negative(不生效)、Positive(生效)
+ 是否要修改
  + 參照文檔修改配置項
    + 文檔 Application Properties 章節
    + 自己分析，`*Properties`綁定哪配置文件的那些值
  + 自定義加入或者替換組件
    + @Bean、@Component …
  + 自定義器 `*Customizer`
  + …

### 開發小工具

#### Lombok

簡化 JavaBean 開發，簡化編寫JavaBean的固定操作，如：建立getter、setter、toString等等。開發人員只要關注JavaBean的屬性即可。

1. 引入依賴

   ``` xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

2. 安裝lombok插件

3. 使用lombok註解修飾 JavaBean，lombok就會幫你生成getter、setter、toString等方法

lombok註解：

| 註解               | 說明                                           |
| ------------------ | ---------------------------------------------- |
| @Data              | 生成getter、setter、toString、hashCode、equals |
| @NoArgConstructor  | 生成無參構造器                                 |
| @AllArgConstructor | 生成全參構造器                                 |
| @ToString          | 生成toString                                   |
| @EqualsAndHashCode | 生成 equals 和 hashcode方法                    |
| @Slf4j             | 注入日誌類，之後使用日誌通過log屬性即可使用    |

#### dev-tools

作用是熱更新，以後項目有什麼改變，只要項目重新編譯(IDEA快捷鍵是Ctrl + F9)，dev-tool就會重新啟動項目

1. 引入依賴

   ``` xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-devtools</artifactId>
       <optional>true</optional>
   </dependency>
   ```

#### Spring Initializr

快速幫你創建一個SpringBoot初始化項目，建立目錄結構、引入場景依賴、建立主程序等等。強烈建議使用

### YAML配置文件

SpringBoot可以在全局編寫 `application.properties` 外，還兼容YAML格式的配置文件。

#### yaml簡介

YAML 是「YAML Ain't Markup Language」(YAML不是一種標記語言)的遞歸縮寫。在開發這種語言時，YAML 的意思其實是：「Yet Another Markup Language」(仍是一種標記語言)

非常適合用來做以數據為中心的配置文件。

注意事項：

+ yaml 與 properties 配置文件可以同時存在
+ properties 的優先級高於 yaml
+ ymal配置文件的副檔名可以是「.yaml」或「.yml」

#### 基本語法

+ key: value **<font color="ff0000">kv之間有1空格</font>**
+ 大小敏感
+ 使用縮進表示層級關係
+ 縮進不允許TAB，只允許空格
+ 縮進空格數不重要，只要相同層級的元素左邊對齊即可
+ #表示注釋
+ 字符串無須加引號，如果使用「''」與「""」表示字串內容，轉義符號(\\)會不解析或解析

#### 數據類型

+ 字面量：單個的、不可在分的值。date、boolean、number、string

  ``` yaml
  k: v
  ```

+ 對象：鍵值對的集合。map、hash、object

  ```yaml
  #行內寫法
  k: {k1: v1, k2: v2, k3: v3}
  #多行寫法
  K:
    k1: v1
    k2: v2
    K3: v3
  ```

+ 數組：一組按次序排列的值。array、list、set、queue

  ``` yaml
  #行內寫法
  k: [v1, v2, v3]
  #多行寫法
  k:
   - v1
   - v2
   - v3
  ```

#### 範例

將下列 JavaBean，與 yaml 配置文件進行綁定

``` java
@Data
@ConfigurationProperties(prefix = "user")
@Component
public class User {
    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animals;
    private Map<String,Object> score;
    private Set<Double> salaries;
    private Map<String,List<Pet>> allPets;
}

```

``` yaml
user:
  userName: James
  boss: true
  birth: 1994/01/01
  age: 18
  pet: {name: godzilla, weight: 36}
  interests: [reading, pcGames]
  animals:
    - cat
    - dog
    - rabbit
  score:
    math: 100
    chinese: 88
    history: 90
    english: 60
  salaries: [36000, 40000, 60000]
  allPets:
    sick:
      - {name: godzilla, weight: 36}
      - {name: littileBlack, weight: 29}
    health:
      - {name: buck, weight: 18}
```

#### 顯示自定義*Properties提示

在編寫`application.yaml`，SpringBoot相關的設定都有提示，自定義的類也可以讓其顯示提示，只需要以下設定：(官方文檔[Configuration Metadata](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-configuration-metadata.html#configuration-metadata)的Configuring the Annotation Processor小節)

1. 導入依賴

   ``` xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-configuration-processor</artifactId>
       <optional>true</optional>
   </dependency>
   
   ```

### Web開發

web開發章節參照官方文檔 [Spring Boot Features](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features) 中 7. Developing Web Applications 小節

#### SpringMVC 自動配置概覽

大多數場景我們都無須自定義配置，SpringBoot作了以下默認配置：

+ 內容協商視圖解析器和BeanName視圖解析器
+ 支持靜態資源(包括webjars)
+ 自動註冊 Converter、GenericConverter、Formatter
+ 支持 HttpMessageConverter(後來我們配合內容協商原理)
+ 自動註冊 MessageCodesResolver (國際化使用)
+ 靜態index.html頁支持
+ 自定義 Favicon
+ 自動使用 ConfigurableWebBindingInitializer (DataBinder負責將請求數據綁訂到 JavaBean 上)
+ …

If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.3.6/reference/html/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

如果你想要客製化SpringMvc的底層組件，**不使用@EnableWebMvc註解，使用`@Configuration` + WebMvcConigurer 自定義規則。**

If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.

If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.

#### 簡單功能分析

##### 靜態資源訪問

可以查看官方文檔中的[Static Content](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-application-events-and-listeners)章節，有更完整的介紹。

###### 靜態資源目錄

SpringBoot 默認靜態資源目錄在「類路徑下」的 `/static` (or `/public` or `resources` or `/META-INF/resources`) 資料夾或 ServletContext 的所指定的目錄

+ 原理：

默認「靜態資源 Controller」可以處理所有請求，但是**優先級最低**。當請求到來時，先由其他 Controller 來處理請求，只有當所有 Controller 都不能處理時，才由靜態資源 Controller 處理，它默認會去這些靜態資源資料夾中找尋相同名稱檔案，如果這些資料夾內都找不到，則返回404。

+ 案例：

  某個 TestController 可以處理與靜態資源名稱相同的請求，那麼該請求到來時，會由 TestController處理，而不是返回該靜態資源，因 TestController 優先級較高。

###### 修改靜態資源訪問目錄

 如果不想要使用SpringBoot默認的幾個靜態資源目錄，可以使用以下設定修改

``` yaml
spring:
  web:
    resources:
      static-locations: [classpath:/hello/]
```

修改資源訪問目錄後 `META-INF/resources` 依舊是資源目錄，為什麼 ? 

###### 修改靜態資源訪問前綴

SpringBoot靜態資源映射是 `/**`，默認是無前綴，只要輸入「項目路徑/靜態資源名稱」就可以訪問對應靜態資源。如果想為靜態資源訪問路徑設定前墜，可以通過以下設置：

``` yaml
spring:
  mvc:
    static-path-pattern: "/resources/**"
```

> 當前項目 + static-path-pattern + 靜態資源名稱

###### webjars

webjars相當於將CSS、JS這些東西弄成一個jar包，可以訪問webjars的官網(https://www.webjars.org/)找到相關資源，例如：JQurey。可以使用Maven將該依賴導入。

SpringBoot支持映射webjars中，映射路徑為 `/webjars/**`，可以獲取webjars內的靜態資源。

> `**`所填寫的路徑要按造依賴包裡面的路徑書寫。
>
> webjars中的靜態檔案都放在META-INF/resources/webjars資料夾下。META-INF/resources本就是默認的靜態資源目錄，webjars資料夾對應訪問路徑/webjars開頭，其實就跟一般靜態文件訪問寫法一致。

##### 歡迎頁支持

SpringBoot可以使用兩種方式支持的歡迎頁，當訪問應用根目錄時，就會顯示歡迎頁。

+ 靜態資源路徑下放置名稱為 index.html 的歡迎頁
  + **但是不可以配置靜態資源請求前綴**
+ 編寫Controller處理/index請求

##### Facicon 網站圖標

SpringBoot會在靜態資源路徑內找尋 `favicon.ico`檔案，並將其作為網站圖標。

+ **但是不可以配置靜態資源請求前綴**，會導致網站圖標失效

#### 靜態資源的配置原理

+ SpringBoot啟動時加載 `*AutoConfiguration` 類(自動配置類)

+ SpringMVC功能的自動配置類為 `WebMvcAutoConfiguration`

+ 確認 `WebMvcAutoConfiguration` 的條件裝配規則

  ``` java
  @Configuration(proxyBeanMethods = false)
  @ConditionalOnWebApplication(type = Type.SERVLET)
  @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
  @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
  @AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
  @AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
  		ValidationAutoConfiguration.class })
  public class WebMvcAutoConfiguration {
  ```

+ 靜態內部類`EnableWebMvcConfiguration` 構造器注入許多元件

  + ResourceProperties：讀取 spring.resources 的配置
  + WebMvcProperties：讀取 spring.mvc 的配置
  + WebProperties：讀取 spring.web 的配置
  + WebMvcRegistrations
  + ResourceHandlerRegistrationCustomizer：找到所有資源處理自定義器組件
  + ListableBeanFactory： spring的BeanFactory

+ 靜態內部類中有addResourceHandlers方法就是用來處理靜態文件映射

``` java
@Override
protected void addResourceHandlers(ResourceHandlerRegistry registry) {
    super.addResourceHandlers(registry);
    // 可以通過spring.web.resources.app-mapping 禁用所有靜態資源設置
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    ServletContext servletContext = getServletContext();
    // 設置webjars的映射
    addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
    // 設置靜態文件映射 讀取spring.mvc.static-path-pattern值，默認值為/**
    addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
 /* 設置靜態資源目錄 讀取spring.web.resources.static-locations 
        默認值: "classpath:/META-INF/resources/",
			"classpath:/resources/", "classpath:/static/", "classpath:/public/"
*/        registration.addResourceLocations(this.resourceProperties.getStaticLocations());
        // 默認一定會加入 servletContext的路徑
        if (servletContext != null) {
            registration.addResourceLocations(new ServletContextResource(servletContext, SERVLET_LOCATION));
        }
    });
}
```

+ 歡迎頁面設置

``` java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
                                                           FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
        new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
        this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
    welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
    return welcomePageHandlerMapping;
}
```

```java
WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
                          ApplicationContext applicationContext, Resource welcomePage, String staticPathPattern) {
    // 如果靜態文件路徑下有歡迎頁且 靜態文件請求為 /** 則設置歡迎頁的forward
    // 解釋了為什麼修改了靜態文件請求前綴，歡迎頁功能就失效
    if (welcomePage != null && "/**".equals(staticPathPattern)) {
        logger.info("Adding welcome page: " + welcomePage);
        setRootViewName("forward:index.html");
    }
    // 如果存在歡迎頁的模板
    else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
        logger.info("Adding welcome page template: index");
        setRootViewName("index");
    }
}
```

重點：

+ 可以通過設置 `spring.web.resources.add-mapping` 為 `false` 禁用所有靜態資源默認配置
+ 可以通過設置 `spring.mvc.static-path-pattern` 設置靜態支援請求前綴
+ 可以通過設置 `spring.web.resources.static-locations` 設置靜態文件資料夾 
+ 修改默認靜態頁面請求的前綴，歡迎頁功能失效
+ 靜態文件目錄下的歡迎頁優先級高於模板目錄下的歡迎頁

#### 請求參數處理

##### 請求映射

+ @RequestMapping
+ Rest 風格支持(使用 HTTP 請求方式的動詞來表示對資源的操作)
  + 以前： /getUser 獲取用戶 /deleteUser 刪除用戶 /updateUser 修改用戶 /saveUser 新增用戶
  + 現在：/user GET-獲取用戶 /user DELETE-刪除用戶 /user PUT-修改用戶 /user POST-新增用戶
  + 核心：HiddenHttpMethodFilter
    + 用法：表單 method=post，傳遞參數 _method=put

想要在SpringBoot使用`HiddenHttpMethodFilter`，配置 `spring.mvc.hiddenmethod.filter.enable = true`。可以在 `WebMvcAutoConfiguration` 源碼中體現：

``` java
@Bean
// 當容器中存在HiddenHttpMethodFilter則使用者配置Bean
@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
// 當配置沒有這項時，默認關閉
@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled", matchIfMissing = false)
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new OrderedHiddenHttpMethodFilter();
}
```

SpringBoot 也對 `@RequestMapping` 做了擴展，以往要編寫 REST 風格 API，都要在 method 屬性指定HTTP請求方式

+ @RquestMapping
  + @GetMapping
  + @PostMapping
  + @PutMapping
  + @DeleteMapping
  + …

直接使用擴展註解，那麼就可以省略指定 method 屬性的動作。

 ##### HiddenHttpMethodFilter 原理

當表單提交想要支持 REST 風格，需要透過該過濾器進行轉換，來分析其運作的原理，其內部源碼如下：

``` java
@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
    throws ServletException, IOException {
	
    HttpServletRequest requestToUse = request;

    if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {
        String paramValue = request.getParameter(this.methodParam);
        if (StringUtils.hasLength(paramValue)) {
            String method = paramValue.toUpperCase(Locale.ENGLISH);
            if (ALLOWED_METHODS.contains(method)) {
                requestToUse = new HttpMethodRequestWrapper(request, method);
            }
        }
    }

    filterChain.doFilter(requestToUse, response);
}
```

``` java
private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {

    private final String method;

    public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
        super(request);
        this.method = method;
    }

    @Override
    public String getMethod() {
        return this.method;
    }
}
```

源碼解析：

+ 過濾器會處理HTTP請求方式為 POST 且沒有錯誤的請求。
+ 過濾器獲取 `_method` 請求參數，判斷是否存在
+ `_method` 不區分大小寫
+ 過濾器判斷是否支持所指定的請求方式，過濾器支持以下方式：
  + PUT
  + DELETE
  + PATCH
+ 原來的 request 會經過 `HttpMethodRequestWrapper` 的包裝
  + `HttpMethodRequestWrapper` 構造器接收 method，過濾器將`_method`傳入
  + `HttpMethodRequestWrapper` 重寫了 `HttpServletRequest` 的 `getMethod`方法，將方法返回值改為`_mehtod`參數
+ 過濾器將包裝過的 request 傳遞下去

Spring 在進行請求映射就是調用 request 的 `getMethod`，Spring 取得的 request 為包裝過的，這也解釋為什麼 POST 表單變成其他請求。

##### 自定義請求方式的參數名稱

`HttpMethodRequestWrapper` 默認是使用 `_method` 作為請求方是參數的名稱。只要容器中配置了 `HttpMethodRequestWrapper` 那麼自動配置就不會在進行配置。

+ 手動配置 `OrderedHiddenHttpMethodFilter`(源碼中是配這個bean)。
+ 調用 `setMethodParam` 方法將設置為自定義的參數名稱。

``` java
@Configuration
class MyConfig(){
    @Bean
    public OrderedHiddenHttpMethodFilter orderedHiddenHttpMethodFilter(){
        OrderedHiddenHttpMethodFilter filter = new OrderedHiddenHttpMethodFilter();
        filter.setMethodParam("httpMethod");
        return filter;
    }
}
```

#### 普通參數與基本註解

SpringBoot中控制器方法形參的實際參數值，實際上是由不同的參數解析器負責解析，有專門處理特定註解，也有些是專門處理原生API等等。

##### 註解

+ @PathVariable：獲取 url-pattern 上的佔位符
+ @RequestParam：獲取指定名稱的請求參數值
+ @RequestHeader：獲取指定名稱的請求頭值
+ @ModelAttribute：用在方法參數是獲取在隱含模型中的數據，標註在方法上則是該 Controller 內的處理請求方法，執行前，該方法會先被運行。
+ @RequestBody：獲取請求體
+ @CookieValue：獲取指定名稱的cookie值
+ @RequestAttribute：獲取request域中的屬性值
+ @SessionAttribute：獲取session域中的屬性值
+ @MatrixVariable：獲取 url-pattern 上佔位符的矩陣變量

上面大多數的註解都已經在 spring-mvc 有詳細的介紹，這裡就不再多做講解，只會挑選出 spring-mvc 沒有提到的註解做說明

###### 矩陣變量

根據RFC3986的規範，**<font color="ff0000">矩陣變量應該綁定在路徑變量(佔位符)</font>**中。有多個矩陣變量，應該使用分號(;)進行分隔，一個矩陣變量有多個值，使用逗號(,)進行分隔，或者多個重複的key指定不同值。

例如：下面兩個寫法具有相同的作用

1. `/cars/sell;low=34;brand=byd,audi,yd` 
2.  `/cars/sell;low=34;brand=byd;brand=audi;brand=yd`。

@MatrixVariable註解，可以取得路徑變量(佔位符)中的矩陣變量值。

+ 屬性：
  + value：矩陣變量的key
  + pathVar：指定路徑變量的名稱

> **每層**路徑都可以宣告矩陣變量，所以才會限定使用在**路徑變量**，不然每當多層以上的路徑有相同名稱的矩陣變量無法分辨。

矩陣變量與常用的queryString不一樣，它們可以同時使用，當有比較特**別的變數就可以定義在矩陣變量中**，與查詢字串區分開。例如：被禁用cookie後，如果要使用session功能，就會改為使用請求參數方式傳遞 jsessionid 值，現在可以使用矩陣變量方式傳遞，與請求參數區分開來。

###### 開啟矩陣變量

SpringBoot 默認是關閉矩陣變量，使用時需要在配置中手動開啟。對於URL路徑的解析是通過 `UrlPathHelper` 組件，通過調整該組件的屬性值來開啟矩陣變量

`removeSemicolonContent` 屬性決定是否移除URL分號內容，因為矩陣變量是使用分號來作為分隔符，所以當該屬性默認為 `true` 時，矩陣變量失效。SpringBoot 默認為 true

``` java
@Override
public void configurePathMatch(PathMatchConfigurer configurer) {
    if (this.mvcProperties.getPathmatch()
        .getMatchingStrategy() == WebMvcProperties.MatchingStrategy.PATH_PATTERN_PARSER) {
        configurer.setPatternParser(new PathPatternParser());
    }
    configurer.setUseSuffixPatternMatch(this.mvcProperties.getPathmatch().isUseSuffixPattern());
    configurer.setUseRegisteredSuffixPatternMatch(
        this.mvcProperties.getPathmatch().isUseRegisteredSuffixPattern());
    this.dispatcherServletPath.ifAvailable((dispatcherPath) -> {
        String servletUrlMapping = dispatcherPath.getServletUrlMapping();
        if (servletUrlMapping.equals("/") && singleDispatcherServlet()) {
            // 使用默認的UrlPathHelper
            UrlPathHelper urlPathHelper = new UrlPathHelper();
            urlPathHelper.setAlwaysUseFullPath(true);
            configurer.setUrlPathHelper(urlPathHelper);
        }
    });
}
```

在Web的自動配置概覽有提到，如何克制化SpringMvc，通過在`@Configuration`配置類，將一個實現 `WebMvcConfigurer` 接口的實現類註冊到ioc容器中，通過 override 接口的默認方法，來替代默認配置。

複寫 `WebMvcConfigurer` 接口中的 `configurePathMatch` 方法，替換`WebMvcAutoConfiguration`中，`configurePathMatch`方法的默認行為，就可以開啟矩陣註解。 

有兩個方式：

1. 讓我們的配置類實現該 WebMvcConfigurer 接口，並且重寫`configurePathMatch`方法，取代自動配置類的設定。
2. 項目啟動後，通過 @Autowired 取得容器中的 UrlPathHelper，將該屬性值設置為 false。

範例：

``` java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper helper = new UrlPathHelper();
        helper.setRemoveSemicolonContent(false);

        configurer.setUrlPathHelper(helper);
    }
}
```

```java
@RestController
public class MatrixController {

    /**
     * 獲取矩陣變量1
     */
    @GetMapping("/user/{userId}")
    public Map<String, Object> test01(@PathVariable("userId") String userId,
                                      @MatrixVariable("username") String username) {
        HashMap<String, Object> map = new HashMap<>();
        map.put("userId", userId);
        map.put("username", username);

        return map;
    }

    /**
     * 矩陣變量2
     * 路徑不同層，有相同名稱的矩陣變量，需要指定路徑變量名稱
     */
    @GetMapping("/user/{departmentId}/{userId}")
    public Map<String, Object> test02(@PathVariable("departmentId") String departmentId,
                                      @PathVariable("userId") String userId,
                                      @MatrixVariable(value = "name", pathVar = "departmentId") String departmentName,
                                      @MatrixVariable(value = "name", pathVar = "userId") String username){
        HashMap<String, Object> map = new HashMap<>();
        map.put("departmentId", departmentId);
        map.put("userId", userId);
        map.put("departmentName", departmentName);
        map.put("username", username);

        return map;
    }

}
```

##### Servlet API

+ WebRequest
+ ServletRequest
+ MultipartRequest
+ HttpSession
+ javax.servlet.http.PushBulider
+ Principal
+ InputStream
+ Reader
+ HttpMethod
+ Locale
+ TimeZone
+ ZoneId

``` java
@Override
public boolean supportsParameter(MethodParameter parameter) {
    Class<?> paramType = parameter.getParameterType();
    return (WebRequest.class.isAssignableFrom(paramType) ||
            ServletRequest.class.isAssignableFrom(paramType) ||
            MultipartRequest.class.isAssignableFrom(paramType) ||
            HttpSession.class.isAssignableFrom(paramType) ||
            (pushBuilder != null && pushBuilder.isAssignableFrom(paramType)) ||
            (Principal.class.isAssignableFrom(paramType) && !parameter.hasParameterAnnotations()) ||
            InputStream.class.isAssignableFrom(paramType) ||
            Reader.class.isAssignableFrom(paramType) ||
            HttpMethod.class == paramType ||
            Locale.class == paramType ||
            TimeZone.class == paramType ||
            ZoneId.class == paramType);
}
```

> 控制器方法的原生Srvlet API參數的賦值由 ServletRequestMethodArgumentResolver 這個參數解析器決定

##### 複雜參數

+ Map、Model：裡面的數據會被放置到request域中
+ Errors 、BindingResult：參數綁定數據的錯誤封裝
+ RedirectAttribute：重定向攜帶數據
+ ServletResponse：response
+ SessionStatus
+ UriComponentBuilder
+ ServletUriComponentBuilder

###### Map 參數值

+ 由 MapMethodProcessor 解析控制器方法參數
+ 解析後返回 BindingAwareModelMap

###### Model參數值

+ 由 ModelMethodProcessor 解析控制器方法參數
+ 解析後返回 BindingAwareModelMap

> **控制器方法形參為Map、Model、ModelMap 最終會返回「相同」的BindingAwareModelMap 對象**

##### 自定義參數類型

SpringMvc支持將請求參數的封裝為自定義類型物件，在SpringBoot也是同樣的使用方式。只要參數名稱與自定義class屬性名稱相同，就會嘗試將請求參數轉換為對應的數據類型，並設置該屬性值。

###### 解析

+ 由 ServletModelAttributeMethodProcessor 參數值解析
  + 決定控制器方法參數的基礎物件(是要創建還是沿用其他來源已有的)
  + Web數據綁定器將請求參數綁定至基礎物件的屬性
    + ConversionService 會負責請求參數的「數據類型轉換」與「數據格式」
  + Web數據綁定器對基礎物件屬型值做數據驗證(方法參數有被 @Valid 標註才會進行數據校驗)
  + 數據校驗的結果封裝至 BindingResult 物件中。(一個自定義類型物件會對應一個 BindingResult 物件 )
+ 為方法參數解析後的自定義類型物件

###### 自定義類型轉換器

1. 實現 Converter 接口，自定義自己的類型轉換器。
2. 配置類實現 `WebMvcConfigurer` 接口，重寫`addFormatters`方法，註冊自己的轉換器

``` java

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        
        // 自定義 Converter
        Converter petConverter = new Converter<String, Pet>(){
            @Override
            public Pet convert(String source) {
                if(StringUtils.hasText(source)){
                    String[] petInfos = source.split(",");
                    if(petInfos.length == 2){
                        Pet pet = new Pet();
                        pet.setName(petInfos[0]);
                        pet.setWeight(Double.parseDouble(petInfos[1]));

                        return pet;
                    }
                }
                return null;
            }
        };
        
        // 註冊類型轉換器
        registry.addConverter(petConverter);
    }
}

```

```java
@RestController
public class PetController {

    @GetMapping("/pet01")
    public Pet converterTest(@RequestParam("petInfo") Pet pet){
        return pet;
    }
}
```

#### 數據響應

##### 響應頁面

控制器方法返回視圖名稱

##### 響應數據

SpringBoot在處理方法返回值時，是通過各種 `HandlerMethodReturnValueHandler`(返回值處理器)來處理返回值。

###### 內容協商原理

請求頭中(Accept)會標註瀏覽器可以接收的內容類型(MediaType)，而服務器本身可以響應不同類型的數據，**瀏覽器與服務器在溝通彼此都可以接收數據類型的過程，稱之為「內容協商」。**

> HttpMessageConverter 轉換是可以雙向了，取決於內部的設定，接口內部定義了canRead和canWrite方法

1. 判斷請求頭中是否有已經確定的媒體類型(MeidaType)。(需要攔截器)
2. 獲取客戶端支持接收的媒體類型。。
   1. 由內容協商管理器(`ContentNegotiationManager`)，它默認策略(`HeaderContentNegotiationStrategy`)是解析請求頭的Accept訊息
   2. 內容協商策略可以通過 `WebMvcConfigurer` 的`configureContentNegotiation`方法增加。
3. 遍歷所有`HttpMessageConverter`(消息轉換器)，將支持所有消息轉換器可以產出的媒體類型記錄下來，統計服務器可提供的媒體類型。
4. 客戶端可以接受的媒體類型與服務器可以提供的媒體類型，取其「交集」，決定最終要使用的媒體類型。(根據客戶端所提供的媒體類型權重排序，權重較高者優先級越高)
6. 遍歷所有`HttpMessageConverter`(消息轉換器)，看哪個支持控制器方法返回值類型又支持最終媒體類型，決定要最終要使用的消息轉換器。
7. 使用消息轉換器處理方法返回器。 

先前在數據綁定器中，介紹到類型轉換器，主要用於將請求數據轉換為方法的形參。而在這邊介紹到的`HttpMessageConverter`(消息轉換器)，是將方法返回值，轉為瀏覽器能接收的數據類型。

###### 開啟瀏覽器參數方式內容協商功能

在不使用 ajax 情況下，無法在瀏覽器設定自己想要的請求頭，SpringBoot支持以參數方式傳遞內容協商功能。**<font color="ff0000">默認只支持兩種媒禮類型，`xml`、`json`。</font>**

內部原理就是SpringBoot除了請求頭內容協商策略以外，在另外配置了`ParameterContentNegotiationStrategy` 這個內容協商策略，可以從請求參數值獲取媒體訊息(默認key是format)

``` yaml
spring:
  mvc:
    contentnegotiation:
      favor-parameter: true
```

```java
/**
 * Whether a request parameter ("format" by default) should be used to determine
 * the requested media type.
 */
 private boolean favorParameter = false;
```

開啟後可以傳遞 `format` 參數，傳遞可接受的媒體類型，當然也可以修改該參數名稱，在配置文件設定以下配置：

``` java
/**
 * Query parameter name to use when "favor-parameter" is enabled.
 */
private String parameterName;
```

``` yaml
spring:
  mvc:
    contentnegotiation:
      # 開啟請求參數傳遞媒體類型功能
      favor-parameter: true
      # 設定內容協商請求參數默認的名稱
      parameter-name: mediaType
```

> 當開啟這功能後，其實就是在內容協商管理器中增加了一個從「請求參數」讀取媒體類型的內容協商策略，且優先級比默認策略高

###### 自定義MessageConverter

當客戶端需要使用到客戶端自定義的媒體類型(MediaType)時，就需要自定義MessageConverter處理。想要自定義MessageConverter處理自訂媒體類型，需要以下步驟：

+ 編寫實現`HttpMessageConverter`接口實現類，該接口定義以下方法：

  + boolean canRead：是否支持讀取操作(@RequestBody標註的控制器方法參數)
  + boolean canWrite：是否支持寫出操作(控制方法返回值)
  + List\<MediaType\> getSupportedMediaTypes：<font color="ff0000">轉換器所支持的媒體訊息</font>
  + \<T\> read：讀取邏輯
  + void write：寫出邏輯

+ 實現類註冊進底層的MessageConverter數組(通過WebMvcConfigurer)

  ``` java
  // 配置MessageConverter，會替換掉所有默認MessageConverter
  // converters 內預設沒任何元素
  default void configureMessageConverters(List<HttpMessageConverter<?>> converters) {}
  // 擴展MessageConverter，可以增加修改底層的MessageConver
  // converters 內有默認的MessageConverter
  default void extendMessageConverters(List<HttpMessageConverter<?>> converters) {}
  ```

> MessageConverter 除了支持將控制器方法返回值進行轉換以外，也可以對請求體做轉換，只要方法的參數標註@RequestBody 那麼就會去找尋適合的 MessageCoverter 做轉換

範例：

``` java
@RestController
public class PetController {

    @GetMapping("/pet")
    public Pet getPet(){
        Pet pet = new Pet("Godzilla", 29d);

        return pet;
    }
}
```

``` java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(new PetMessageConverter());
    }
}
```



###### 字定義內容協商策略

SpringBoot在幫我們配置參數傳遞媒體類型內容協商策略時，默認只有JSON、XML兩種格式，如果自定義類型的媒體類型，使用SpringBoot配置的就不合適。所以也可以通過配置替換掉SpringBoot默認配置的幾種策略，SpringBoot默認策略如下：

+ HeaderContentNegotiationStrategy：讀取Accept請求頭
+ ParameterContentNegotiationStrategy：當有配置請求參數內容策略時

`WebMvcConfigurer` 的 `configureContentNegotiation()`方法，就是用來配置內容協商，可以在這裡添加內容策略，<font color="ff0000">需要注意的是，添加自己策略後會把SpringBoot的默認策略取消，如果還需使用到SpringBoot的默認策略，請手動添加上。</font>

自訂內容協商策略，有以下步驟：

+ 實現`ContentNegotiationStrategy` 接口，就可以自訂義自己的內容協商策略
+ 通過 WebMvcConfigurer 接口實現配置類，把自定義的策略註冊進內容協商管理器

範例：將參數的策略添加一個自定義的媒體類型`application/x-pet`

``` java
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
        HashMap<String, MediaType> supportMediaTypes = new HashMap<>();
        supportMediaTypes.put("json", MediaType.APPLICATION_JSON);
        supportMediaTypes.put("xml", MediaType.APPLICATION_XML);
        // 自定義媒體類型的支持
        supportMediaTypes.put("pet", MediaType.parseMediaType("application/x-pet"));
        // 參數策略
        ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(supportMediaTypes);
        // 請求頭策略
        HeaderContentNegotiationStrategy headerStrategy = new HeaderContentNegotiationStrategy();

        // 註冊自己的策略(會蓋掉SpringBoot默認)
        configurer.strategies(Arrays.asList(parameterStrategy, headerStrategy));
    }
}
```



###### 響應數據總結

當控制器方法返回的是數據而不是視圖名稱時，會使用 `@ResponseBody` 註解。

1. `@ResponseBody`註解標註的控制器方法，會調用<font color="ff0000">`RequestResponseBodyMethodProcessor`</font>來處理方法的返回值。
2. 返回值處理器，內部會調用 MessageConverter 來處理，不同的 MessageConverter 分別處理不同媒體類型的數據，也代表著這個web應用可以響應與處理的媒體數據。
3.  返回值處理器會根據客戶端可以接受的媒體類型與當前web應用可以提供的媒體類型進行比對(內容協商流程)
4. 最終決定出「合適」的 MessageConverter 對方法返回值進行處理。

###### 響應JSON

想要讓 SpringBoot響應JSON，首先需要兩步驟

+ 引入 spring-boot-starter-web 場景(裡面會自動引入 spring-boot-start-json 場景)
+ 使用相關註解，有以下方式
  + Controller上標註`@RestController`，表示類方法都被@ResponseBody註記。`@RestController` 相當於 @ResponseBody 與 @Controller 組合
  + Controller方法上使用 `@ResponseBody`標註，表示方法返回值為ResponseBody
  + 方法返回值宣告為 ResponseEntity，代表響應，可以設定響應頭、響應體、響應狀態碼。

`MappingJackson2HttpMessageConverter` 可以處理 JSON 的 MimeType

#### 視圖解析

##### 視圖解析

處理完請求進行跳轉到頁面的過程，被稱為視圖解析。

SpringBoot 默認不支持 JSP，需要引入第三方模板引擎技術實現頁面渲染。原因是SpringBoot默認打包成jar包，屬於壓縮包的一種，而JSP不支持在壓縮包內編譯。

SpringBoot支持以下第三方模板引擎，freemarker、groovy-templates、thymeleaf

##### 視圖解析原理

+ 控制器方法返回值為視圖名稱時，會使用`ViewNameMethodReturnValueHandler`返回值處理器來處理
+ 目標方法處理過程中，所有數據都會放在 ModelAndViewContainer 裡面，包括數據和視圖地址。
+ 如果方法的參數是一個自定義類型對象(從請求參數中確定)，它也會重新放在 ModelAndViewContainer
+ 任何目標方法執行完後都會返回ModelAndView
+ processDispatchResult 方法處理派發結果(頁面如何響應)
  + 如果目標方法執行後返回的ModelAndView不為null，rander() 進行頁面渲染
    + **遍歷所有視圖解析器，解析視圖名稱得到View對象，直到有一個成功解析為止，如果全部失敗，拋出異常**
    + 調用View對象的render()方法，進行試圖的渲染

> 目標方法執行後的 ModelAndView 如果為 null ，那不會經過後續的視圖名稱解析出視圖，視圖渲染過程。因為在處理方法返回值時，由返回值處理器直接處理完畢了。
>
> @ResponseBody 如果標註在控制器方法上，就會有上述的情況。當然也有可能是其他類型返回值造成的。

> 視圖解析器作用是解析視圖名稱得到視圖對象，視圖對象作用為決定如何展示數據
>
> 數據會以各種不同方式展現，除了網頁已外，還能是各種檔案pdf、excel ... 。

#### 控制器方法執行

目標方法由 HanlderAdapter(執行器適配器)來執行，HTTP請求通常會由 RequestMappingHandlerAdapter 來處理，下面講解該適配器執行的邏輯

+ 參數解析器，用來解析目標方法的參數值(參數解析器會有多個)
  + 會依據請求參數上的註解、參數類型等為依據，決定使用哪個參數解析器
+ 返回值處理器，用來處理執行完目標方法後的返回值(返回值處理器會有多個)
  + 會依據目標方法的註解、目標方法返回值類型等訊息，決定由哪個返回值類型處理器

> HanlderAdapter 執行完目標方法，會返回 ModelAndView 對象(可能為null)，如果不為null，會接續後續視圖解析過程。



#### 模板引擎 - Thymeleaf

##### Thymeleaf 簡介

現代化、服務端 Java 模板語言

##### 基本語法

###### 表達式

| 表達式     | 語法 | 用途                           |
| ---------- | ---- | ------------------------------ |
| 變量取值   | ${…} | 獲取請求域、session域、對象等  |
| 選擇變量   | *{…} | 獲取上下文對象值               |
| 消息       | #{…} | 獲取國際化等值                 |
| 鏈結       | @{…} | 生成鏈結                       |
| 片段表達式 | ~{…} | jsp:include 作用，引入公共頁面 |

###### 字面量

+ 文本值：'one'、'two'、'three' … (單引號)
+ 數字：0、1、2.0、3.4
+ 布爾值：true、false
+ 空值：null
+ 變量：a1、a2、a3、…(變量不可以有空格)

###### 文本操作

+ 字串符拼接：+
+ 變量替換：|The name is ${name}|

###### 數學運算

運算符：「+」、「-」、「*」、「/」、「%」

###### 布爾運算

+ 二元運算符：and、or
+ 一元運算符：!、not

###### 比較運算符

+ 比較：>(gt)、<(lt)、>=(ge)、<=(le)
+ 等式：==(eq)、!=(ne)

###### 條件運算符

+ if-then：(if) ? (then)
+ if-then-else：(if) ? (then) : (else)
+ Default：(value) ?: (defaultvalue)

###### 特殊操作符

無操作：_

##### 設置屬性值 th:attr

+ 設置單個值

  ``` html
  <form action="subscribe" th:attr="action=@{/subscribe}">
      <input type="text" name="email">
      <input type="text" value="Subscribe!!!" th:attr="value=#{subscribute.submit}">
  </form>
  ```

+ 設置多個屬性值(逗號分隔)

  ``` html
  <img th:attr="src=@{/images/logo.png},title="#{logo}">
  ```

+ 替代寫法(th:xxx)

  ``` html
  <form action="subcribe" th:action="@{/subcribe}"> // 會自動拼接上項目路徑
      <input type="text" value="Subscribe!!!" th:value="#{subcribute.submit}"/>
  </form>
  ```

##### 迭代 th:each

``` html
<tr th:each="prod : ${prods}">
    <td th:text="${prod.name}">defaultName</td>
    <td th:text="${prod.value}">defaultValue</td>
    <td th:text="${prod.inStack}? #{true} : #{false}">yes</td>
</tr>
```

```` html
<tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd? 'odd'}">
    <td th:text="${prod.name}">defaultName</td>
    <td th:text="${prod.value}">defaultValue</td>
    <td th:text="${prod.inStack}? #{true} : #{false}">yes</td>
</tr>
````

##### 條件運算

``` html
<a th:href="@{/product/commons/${prod.id}" th:if="${not lists.isEmpty}">view</a>
```

```` html
<div th:switch="${user.role}">
    <p th:case="'admin'">
        User is an administrator
    </p>
    <p th:case="{roles.manager}">
        User is a manager
    </p>
    <p th:case="*">
        User is some orther role
    </p>
</div>
````

##### 行內語法

如果要顯示的文字為標籤文本內容，那麼可以使用`th:text`替換掉標籤文本內容，但是如果我們並不想替換掉全部的文本內容，又或者是文字並不在標籤中，那可以使用行內寫法。

想要在標籤頭以外使用thymeleaf表達式，表達式加上前綴「[[ 或 [(」和後綴「]] 或 )]」。

``` html
<h1>
    Hello, [[${user.name}]]
</h1>
```



##### Thymeleaf使用

1. 引入spring-boot-start-thymeleaf 依賴

2. SpringBoot自動配置(`ThymeleafAutoConfiguration`)

   1. 所有的Thmyeleaf的配置值都在 `ThymeleafProperties`
   2. 模板解析器 `SpringSourceTemplateResolver`
   3. 模板引擎 `SpringTemplateEngine`
   4. 視圖解析器 `ThymeleafViewResolver`

3. 開發頁面

   1. 視圖名默認前墜：`classpath:/templates/` 且 默認後墜：`.html`，所以默認要放到類路徑下的templates資料夾且副檔名為.html。

   2. 頁面引入thymeleaf名稱空間

      ``` html
      <html xmlns:th="http://www.thymeleaf.org">    
      </html>
      ```

#### 攔截器

web應用通常有登入功能，只有登入後才能訪問網站的功能，所以勢必要在每個功能前面都加上是否登入的驗證，而這個工作就可以通過攔截器來處理。

通過實現 `HandlerInterceptor` 接口，自定義攔截器，該接口有以下方法：

+ preHandler：在目標方法執行前處理
+ postHandler：在目標方法執行完後，頁面渲染前處理
+ afterComplete：在視圖渲染完成後處理

> 執行過preHandler方法且返回true的攔截器，不論後續是否有異常，它的afterComplete必定會執行。

使用攔截器步驟：

1. 編寫自定義攔截器
2. 定義攔截器攔截那些請求
3. 把攔截器放入容器中
   1. 通過`WebMvcAutoConfigurer`接口的 `addInterceptosrs`方法，配置攔截器。

範例：

``` java
@Slf4j
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("preHandle");
        Object currentUser = request.getSession().getAttribute("currentUser");
        if(currentUser != null){
            return true;
        }
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        log.info("afterCompletion");
    }
}
```

``` java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginInterceptor())
        .addPathPatterns("/**") //攔截所有請求
        .excludePathPatterns("/static/**","/login","/"); //排除對靜態文件的請求、登入請求
}
```

##### 攔截器原理

+ 根據當前請求，找到可以處理請求的handler(controller)和所有符合的攔截器，它們被封裝為`HandlerExecutionChain`對象。
+ 目標方法執行之前，「**<font color="ff0000">順序</font>**」執行所有攔截器的 `preHandle` 方法
  + 返回 `true`，則執行下一個攔截器的 `preHandle`
  + 返回 `false`，「**<font color="ff0000">倒序</font>**」執行之前「`preHandle`返回`true`」攔截器的 `afterComplete` 方法
+ 如果任何一個攔截器`preHandle`返回 `false`，直接跳出不執行目標方法
+ 如果所有攔截器的`preHandle`都返回`true`執行目標方法
+ 目標方法執行完，「**<font color="ff0000">倒序</font>**」執行全部攔截器的 `postHandle`
+ 頁面渲染完成，「**<font color="ff0000">倒序</font>**」執行全部攔截器的 `afterCompletion`

> 前面的步驟有任何異常都會直「**<font color="ff0000">倒序</font>**」執行之前「`preHandle`返回`true`」攔截器的 `afterCompletion` 方法

#### 文件上傳

+ 表單文件上傳
  + method 設置為 post
  + enctype 設置為 multipart/form-data
  + input 標籤指定 type=file，為上傳表單元件
    + input標籤可以設置 multipart 屬性(沒有值)，指定上傳表單元件為多檔案上傳

Spring在處理文件上傳時，可以通過控制器方法上 `MultipartFIle` 類型形參，表示上傳的檔案，一個檔案對應一個，所以是多文件上傳時，可以指定為`MultipartFIle`數組。

`MultipartFIle`接口定義以下方法：

+ getName()：獲得表單的name值(請求參數的key)
+ getOriginFilename()：獲得客戶端上檔案的文件名稱
+ getContentType()：獲得檔案的類型
+ isEmpty()：檔案是否為空(表單上傳元件沒選檔案或選擇的檔案為空)
+ getSize()：獲得檔案的大小(byte)
+ getBytes()：獲得檔案的內容
+ getInputStream()：獲得檔案的輸入流
+ transferTo：將檔案儲存到指定目錄

如果 `MultipartFIle` 類型的形參名稱與表單上傳元件的 name 不一致時，可以通過 `@RequestPart` 註解指定表單文件的 name，進行數據綁定動作。

`MultipartAutoConfiguration`自動配置了文件上傳的設定，`MultipartProperties`內定義可以調整的設定。例如：要修改默認的檔案大小設置，可以進行以下設定

```yaml
spring:
  servlet:
    multipart:
      # 設置單檔案大小限制
      max-file-size: 10MB
      # 設置請求總檔案大小限制
      max-request-size: 100MB
```

範例：

``` html
<form action="upload" method="post" enctype="multipart/form-data">
    頭像：<input type="file" name="headImg"><br>
    <!-- 多檔案上傳 -->
    生活照：<input type="file" name="liveImg" multiple><br>
    <input type="submit">
</form>
```

``` java
@PostMapping("/upload")
public String upload(@RequestPart("headImg") MultipartFile userImage, List<MultipartFile> liveImg) throws IOException {

    log.info("userImg formName={}, fileName={}, fileSize={}",
             userImage.getName(), userImage.getOriginalFilename(), userImage.getSize());

    // 保存前確認檔案是否為空
    if(!userImage.isEmpty()){
        userImage.transferTo(new File("C:\\upload\\" + userImage.getOriginalFilename()));
    }

    // 多檔案上傳
    if(!liveImg.isEmpty()){
        for(MultipartFile file : liveImg){
            log.info("userImg formName={}, fileName={}, fileSize={}",
                     file.getName(), file.getOriginalFilename(), file.getSize());

            // 保存前確認檔案是否為空
            if(!file.isEmpty()){
                file.transferTo(new File("C:\\upload\\" + file.getOriginalFilename()));
            }
        }
    }

    return "success";
}
```

#### 異常處理

##### 錯誤處理

###### SpringBoot默認規則：

+ 默認情況下，SpringBoot提供 `/error` 處理所有錯誤映射
+ 對於機器客戶端，它會生成JSON響應，其中包含錯誤，HTTP狀態和異常消息的詳細訊息。對於瀏覽器，響應一個「whitelabel」錯誤視圖，以HTML格式呈現相同數據。
+ **要對whitelabel進行自定義，註冊`View` bean id為 `error`**
+ 要完全替換默認行為，可以實現 `ErrorController` 並註冊該類型的 Bean 定義，或添加 `ErrorAttributes` 類型組件以使用現有機制替換其內容

###### SpringBoot提供的錯誤處理機制：

+ 自定義錯誤頁面，根據響應狀態碼跳轉到對應錯誤頁。
  + 錯誤頁面需要放到靜態/動態資源目錄下的`error`資料夾。例如：error/404.html、error/5xx.html(以5開頭的響應狀態碼都使用該頁)
+ `@ControllerAdvice` + `@ExceptionHandler` 註解定義**全局**異常處理方法
+ 在各自的 Controller + `@ExceptionHandler` 註解定義**區域**異常處理方法
+ 實現 `HandlerExceptionResolver` 接口處理異常。**可通過 @Order 註解調整異常解析器的優先級**。

###### 異常處理底層組件功能分析

SpringBoot 錯誤自動配置類為 ：`ErrorMvcAutoConfiguration`。裡面自動配置了以下組件：

+ `DefaultErrorAttribute` 組件 (`id=errorAttributes`)

  定義錯誤響應中包含哪些內容

+ `BasicErrorController` 組件 (`id=basicErrorController`)

  + SpringBoot默認處理 /error 路徑的請求的控制器類

    ``` java
    @Controller
    // 1. server.error.path 配置訊息
    // 2. error.path 配置訊息
    // 3. /error 都沒配置的映射
    // 優先度由上而下
    @RequestMapping("${server.error.path:${error.path:/error}}")
    public class BasicErrorController extends AbstractErrorController {}
    ```

  + 客戶端為瀏覽器時響應白頁，其他都響應JSON

+ `View` 組件(`id=error`)，默認響應錯誤頁

+ `BeanNameViewResolver` 組件，依視圖名作為組件的id去容器找view對象。為了找上一項配置的 `View`。

+ `DefaultErrorViewResolver` 組件 (`id=conventionErrorViewResolver`)

  如果發生錯誤，會以HTTP的狀態碼作為視圖地址(viewName)，找到真正的頁面。例如：error/4xx.html、error/5xx.html

###### 異常處理步驟流程

1. 執行目標方法，方法執行期間有任何異常，都會被 catch 保存到 dispatchEcxeption區域變數，且會將當前請求標註為「已完成」，將在頁面渲染流程使用。

2. 頁面渲染流程

   `processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);`

   1. **處理handler異常，返回ModelAndView**

      `mv = processHandlerException(request, response, handler, exception);`

      1. 遍歷所有的handler異常解析器(`HandlerExceptionResolver`)解析異常，看誰能處理當前異常並返回ModelAndView，以下為默認的異常解析器：

         1. DefaultErrorAttributes(**<font color="ff0000">「必定執行」，順位第一，且返回null值，執行完會交由後續解析器繼續解析</font>**)

            將發生的異常保存到請求域中，key：DefaultErrorAttributes全類名 + .ERROR

         2. HandlerExceptionResolverComposite(三個異常解析器的組合)

            1. ExceptionHandlerExceptionResolver

               **用來處理@ExceptionHandler註解**

            2. ResponseStatusExceptionResolver

               **用來處理@ResponseStatus註解**

            3. DefaultHandlerExceptionResolver

               專門用來處理Spring的異常

      2. 異常解析器**有**解析出ModelAndView，做一些處理，返回試圖後接續視圖解析流程

         1. 判斷視圖與模型是否為空，為空返回null
         2. 判斷視圖是否為空，為空設定視圖名
         3. …

      3. 異常解析器**沒有**解析出ModelAndView，代表沒人處理該異常，則拋出當前異常，頁面渲染失敗，**SpringBoot底層再會次發出/error請求，由`BasicErrorController`處理該請求。**

以下說明 BasicErrorController 的錯誤解析流程：

+ 如果沒有人可以處理異常，程序中止，SpringBoot底層會轉發(forward)/error請求，交由BasicErrorController 處理
+ 使用解析錯誤視圖解析器，遍歷所有的 `ErrorViewResolver`，解析出錯誤視圖，默認使用 DefaultErrorViewResolver
  + 嘗試查找是否有對應的「具體」錯誤頁面。例如：error/404.html、error/500.html。
  + 嘗試查找是否有對應的「模糊」錯誤頁面。例如：error/4xx.html、error/5xx.html。
+ 如果錯誤試圖解析器都沒解析出，則使用視圖名稱為error的視圖(白頁)，在自動配置就加入至ioc容器中，id=error，會使用BeanNameViewResolver判斷出使用該error視圖
+ 接續後續的目標方s法執行完後的流程

#### Web原生組件注入(Servlet、Filter、Listener)

##### 使用 Servlet API

SpringBoot 提供 `@ServletComponentScan` 註解：作用是掃描 `@WebServlet`、`@WebListener`、`@WebFilter` 註解標註的類，可以通過這種方式讓Web原生組件註冊到容器中。

相對於使用第二種方式，第一種方式更便捷一些。

##### 使用 ReqistrationBean

通過 SpringBoot 提供的組件：

+ ServletReqistrationBean：註冊Servlet
+ FilterReqistrationBean：註冊Filter
+ ServletListenerReqistrationBean：註冊Listener

通過將這三個組件註冊到ioc容器中，它們都構造器都接收自定義的Servlet、Filter、Listener

##### 註冊的Servlet無法被攔截器攔截

學習SpringMvc時知道，框架是通過前端控制器(DispatcherServlet)，來進行一系列的操作，各種請求參數轉換、視圖解析、錯誤處理、攔截器等等，都是前端控制器的功能。

而我們自己定義註冊到Spring的Servlet，卻是沒有doDispatcher方法中那些神奇的功能，當然沒有辦法使用到框架相關功能。

題外話：DispatcherServlet 是通過 DispatcherServletAutoConfiguration 來進行自動配置，前端控制器是通過ServletRegistration方式註冊到容器被Spring管理。

#### 嵌入式Servlet容器

+ 當前項目是一個Web應用，會自動配置一個特殊的web版ioc容器(`ServletWebServerApplicationContext`)
+ `ServletWebServerApplicationContext`對嵌入式server容器進行支持。
+ 容器啟動時，會去找尋 `ServletWebServerFactory`，該功能會創建對應的server容器
+ `ServletWebServerFactory`工廠的實現類，對應著SpringBoot底層默認支持的web容器：
  + TomcatServletWebServerFactory：Tomcat支持
  + JettyServletWebServerFactory：Jetty支持
  + UndertowServletWebServerFactory：Undertow支持
+ 內嵌服務器其實就是導入服務器的依賴，通過代碼的方式來設定服務器並啟動，與先前手動點擊start.bat來啟動並無區別。

##### 切換服務器

ServletWebServerFactoryAutoConfiguration自動配置類，根據導入的server依賴自動配置了Tomcat、Jetty、Undertow。

所以只要切換項目導入的服務器依賴，就可以達到切換的效果。有以下步驟

+ `spring-boot-starter-web`內嵌`spring-boot-starter-tomcat`，想要其他服務器時，在導入web場景依賴時須要排除 `spring-boot-starter-tomcat`

  ``` xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <exclusions>
          <exclusion>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-tomcat</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

  

+ 導入其它服務器場景依賴，例如：`spring-boot-starter-jetty`、`spring-boot-starter-undertow`

  ``` xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jetty</artifactId>
  </dependency>
  ```

##### 服務器設定

+ 方式一：通過配置文件 (推薦)

  知道web服務器啟動是通過`ServletWebServerFactory`，而該工廠的自動配置類為`ServletWebServerFactoryAutoConfiguration`，自動配置類是讀取`ServerProperties`，所以在配置文件以`server.`開頭自定義web服務器

+ 方式二：通過自定義 `ServletWebServerFactory`直接修改創建啟動web容器的邏輯。 推薦使用`ServletWebServerFactory`的子接口`ConfigurableServletWebServerFactory`

+ 方式三：自定義 `WebServerFactoryCustomizer`實現類，實現以編程方式配置，默認是使用`ServletWebServerFactoryCustomizer`，內部是通過讀取`ServerProperties`方式配置

#### SpringBoot 訂制化原理

##### 自動配置套路

<font color="ff0000">引入場景依賴</font> -> xxxAutoConfiguration -> 註冊組件 -> 綁定xxxProperties -> <font color="ff0000">綁定配置文件項(application.properties)</font>

中間的配置過程都由SpringBoot進行封裝，通常情況下開發時只需導入相關場景依賴，並根據業務邏輯的需求，通過配置文件修改默認配置。只有少數情況，才會通過自定義組件去替換底層組件。

##### 自訂義的常見方式

+ 修改配置文件
+ xxxCustomizer
+ @Configuration + @Bean，替換或添加容器中默認的組件。例如：視圖解析器
+ 容器中配置`WebMvcConfigurer`實現類，訂制化web功能。
+ @EnableWebMvc + WebMvcConfigurer 全面接管SpringMvc，全部規則自訂義
  + @EnableWebMvc註解會讓 WebMvcAutoConfiguration 自動配置類所有配置失效

### 數據訪問

#### SQL

##### 數據源自動配置

###### 導入JDBC

+ 導入 `spring-boot-starter-data-jdbc` 場景

  ``` xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jdbc</artifactId>
      </dependency>
  ```

+ 導入數據庫驅動(版本與數據庫建議一致)

  ``` xml
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
  </dependency>
  ```

> 關於如何修改版本仲裁的默認依賴版本，依賴管理章節有介紹

###### 分析

自動配置類：

+ DataSourceAutoConfiguration：數據源的配置

+ DataSourceTrancesactionManagerAutoConfiguration：事務管理器配置

+ JdbcTemplateAutoConfiguration：JdbcTemplate配置

+ XADataSourceAutoConfiguration：分布式事務配置

  

:green_book:DataSourceAutoConfiguration：

+ 數據源配置，修改配置文件項 **spring.datasource** 開頭相關設定 。
+ 數據庫連接池配置生效與否，取決於是否有 DataSource 在IOC容器中。
+ 默認使用的數據連接池為 Hikari，spring-boot-starter-data-jdbc 默認使用該數據源
+ 當存在多個數據源依賴時，可以通過配置文件項 **spring.datasource.type**  指定。



:large_blue_circle: 數據源配置：

``` yaml
datasource:
    url: jdbc:mysql://localhost:3306/spring_demo
    username: root
    password: 1qaz2wsx
    driver-class-name: com.mysql.cj.jdbc.Driver
```

##### 使用 Druid 數據源

整合第三方技術的方式：

+ 自定義
+ starter

這裡介紹使用阿里巴巴提供的starter，可以去github找到相關訊息。

:one: 引入Durid場景

``` xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.6</version>
</dependency>
```

:two: 查看DuridDataSourceAutoConfiguration或[官方文檔](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)，對相應功能進行開啟。

##### 整合 Mybatis

:one: 引入Mybatis提供的starter

``` xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

MybatisAutoConfiguration 為MyBatis自動配置類，內部作了以下事情：

+ **容器中只能有一個 DataSource，否則MybatisAutoConfiguration失效**
+ 想要修改MyBatis的默認配置，可已修改applicat配置文件 **mybatis** 開頭配置項。
+ 配置 SqlSessionFactory
+ 配置 SqlSessionTemplate (實現SqlSession)
+ 配置 MapperScannerRegistrarNotFoundConfiguration ：掃描@Mapper修飾的Mpper

默認配置已經做完大部分的事情，我們只需要接續以下步驟

:two: application配置文件，配置Mybatis設定

``` yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/spring_demo
    username: root
    password: 1qaz2wsx
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  # 指定mapper映射文件放置位置
  mapper-locations: classpath:mybatis/mapper/*.xml
  # 指定mybatis主配置文件位置
#  config-location: classpath:mybatis/mybatis-config.xml
  configuration:
    map-underscore-to-camel-case: true
```

> mybatis主配置文件可以直接使用 mybatis.configuration.* 配置項直接替代。
>
> **注意：指定主配置文件位置的 mybatis.config-location 不可與 mybatis.configuration.* 配置項共存，只能選擇一個使用。**

:three: 編寫Mapper接口。(標註@Mapper註解)編寫

``` java
@Mapper
public interface AccountMapper {
    Account getAccountById(String id);
}
```

:four: SQL映射文件對應Mapper接口

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.learning.springboot.mapper.AccountMapper">
    
    <select id="getAccountById" resultType="org.learning.springboot.entity.Account" >
        select * from account
        <where>
            name = #{name}
        </where>
    </select>
    
</mapper>
```

######  @MapperScan

@MapperScan 作用是告訴 Mybatis Mapper接口的基礎路徑，可以標註在SpringBoot主程序上，這樣每個Mapper接口可以不用標註@Mapper，也可以被Mybatis識別。

> 不建議這樣使用

###### MyBatis-Plus

Mybatis-Plus 是一個 Mybatis 增強工具，在Mybatis的基礎上只做增強不做改變，為簡化開發，提高效率而生。

[MyBatis-Plus官網](https://baomidou.com/)

**IDEA使用MyBatis-Plus建議安裝MybatisX插件。**

#### NO-SQL

##### Redis

###### 簡介

REmote DIctionary Server(Redis) 是一個由 Salavatore Sanfilipo 寫的 key-value 儲存系統，是**跨平台**的**非關係型數據庫**。

Redis 是一個開源的使用 ANSI C 語言編寫，遵守 BBS 協議、支持網路、可基於內存、分布式、可持久性的**鍵值對(key-value)儲存數據庫**，並提供多種語言的 API。

Redis 通常被稱為數據結構服務器，因為值可以是字符串(String)、哈希(Hash)、列表(List)、集合(Set)和有序集合(Sorted Set)等類型。

###### 特色

Redis 和其他 key-value 緩存產品對比有以下特色：

+ Redis 支持數據的持久化，可以將內存中的數據保存在硬碟中，重啟的時候可以在次加載進行使用。
+ Redis 不僅僅支持簡單的 key-value 類型的數據，同時還提供 list、set、zset、hash 等數據結構的儲存
+ Redis 支持數據的備份，即 master-slave 模式的數據備份

###### 優勢

:one: 性能極高：Redis 能讀的速度是 110000次/s，寫的速度是 81000次/s

:two: 豐富的數據類型：Redis 支持二進制案例的 Strings、Lists、Hashes、Sets 即 Ordered Sets 數據類型。

:three: 原子：Redis 的所有操作都是原子性的，要麼成功要麼失敗全部不執行。單個操作是原子性的。多個操作支持事務，即原子性。

:four: 豐富的特性：Redis 還支持 publish/subscribe、通知、key、過期等等特性。 

###### Redis自動配置

導入 spring-boot-starter-data-redis 場景後，`RedisAutoConfiguration` 自動生效，Redis 相關設定值都綁定在 `RedisProperties`，**想要修改默認配置值，通過application配置文件中 spring.redis 配置項。**

自動配置類還導入以下組件：

+ LettuceConnectionConfiguration：lettuce 客戶端的連接配置

  配置 string.redis.client-type=lettuce 時，該自動配置類生效

+ JedisConnectionConfiguration：Jedis 客戶端的連接配置

  配置 string.redis.client-type=jedis 時，該自動配置類生效

+ RedisTemplate<Object, Object> 

+ StringRedisTemplate<String, String>

> xxxTemplate 都是 Spring 用來操作相關技術的工具類

###### SpringBoot 整合 Redis

:one: 引入 `spring-boot-starter-data-redis` 場景

:two: 配置redis配置連接訊息，使用以下格式：

``` yaml
redis:
  host:
  password:
  port: 
#上面三個可以使用url取代 格式: redis://user:password@example.com:6379
  url: 
```

###### 切換客戶端

spring-boot-starter-data-redis 場景默認是依賴於 Lettuce 客戶端，它是基於 netty 開發，併發性能較高。如果你習慣於 Jedis，可以通過以下方式切換：

:one: 導入 Jedis 依賴(SpringBoot有進行版本仲裁)

``` xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
<dependency>
```

:two: 通過application配置文件指定使用 Jedis 客戶端

``` yaml
  redis:
    client-type: jedis
```



### 單元測試

#### JUnit5的變化

SpringBoot 2.2.0 版本引入 JUnit 5 作為單元測試默認庫。

作為最新版本的 JUnit 框架，JUnit 5 與之前版本的 JUnit 框架有很大不同。由三個不同子項目的幾個不同模塊組成。

>  JUnit5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

+ JUnit Platform：JUnit Platform是在JVM上啟動單元測試框架的基礎，不僅支持Junit自製的測試引擎，其他測試引擎也可以接入。
+ JUnit Jupiter：JUnit Jupiter 提供了 JUnit5 的新編程模塊，是 JUnit5 新特性的核心，內部包含一個測試引擎，用於在 JUnit Platform 上運行。
+ JUnit Vintage：由於JUnit已經發展多年，為了兼容老舊項目，JUnit Vintage 提供兼容JUnit4.x、JUnit3.x的測試引擎。

> SpringBoot 2.4 以上版本移除了默認對 Vintage 的依賴。如果需要兼容 Junit4 需要自行引入。

##### SpringBoot 整合 JUnit

+ 測試類上使用一個 @SpringBootTest 標註即可
+ 測試方法使用 @Test 標註(import org.junit.jupiter.api.Test)
+ 測試類具有容器的功能，@Autowired 依賴注入、@Transactional 測試方法執行完自動回滾等

#### 前後使用方式對照

以前

``` java
@SpringBootTest
@RunWith()
public class Test{   
}
```

JUnit 5

``` java
@SpringBootTest
public class Test{    
}
```

#### 使用方式

:one: 引入 spring-boot-starter-test 場景

:two: 編寫測試類

#### JUnit5 常用註解

+ @Test：表示方法是測試方法，但是與 JUnit4 的 @Test 不同。它的職責非常單一不能聲明任何屬性，擴展的測試將由 Jupiter 提供額外的測試。
+ @ParamterizedTest：表示方法是參數化測試，下方有詳細介紹
+ @RepeatedTest：表示測試方法為重複執行測試
+ @DisplayName：為測試類或測試方法設置展示名稱
+ @BeforeEach：表示在**每個**單元測試之前執行
+ @AfterEach：表示在**每個**單元測試之後執行
+ @BeforeAll：方法在**所有**單元測試之前運行，方法須為static
+ @AfterAll： 方法在**所有**單元測試之後運行，方法須為static
+ @Tag：表示單元測試類別，類似 JUnit4 中的 @Categories
+ @Disable：表示測試類或測試方法不運行，類似於 JUnit4 中的 @Ignore
+ @Timeout：表示測試方法運行如果超過指定時間將會返回錯誤
+ @ExtendWith：為測試類或測試方法提供擴展類引用，取代@RunWith

``` java
@SpringBootTest
public class Junit5Test {

    @Test
    public void test01() {
        System.out.println("測試01");
    }

    @DisplayName("測試 Display 註解")
    @Test
    public void testDisplayName() {
        System.out.println("測試 DisplayName 註解");
    }

    @BeforeEach
    public void beforeEach() {
        System.out.println("單元測試開始");
    }

    @AfterEach
    public void afterEach() {
        System.out.println("單元測試結束");
    }

    @BeforeAll
    public static void beforeAll() {
        System.out.println("所有測試準備開始 ...");
    }

    @AfterAll
    public static void after() {
        System.out.println("所有測試已經結束 ...");
    }

    @Timeout(value = 300, unit = TimeUnit.MILLISECONDS)
    @Test
    public void testTimeout() throws InterruptedException {
        Thread.sleep(1000);
    }

    @RepeatedTest(6)
    public void testRepeatedTest() {
        System.out.println("Hello World");
    }

}

```

#### 斷言機制

斷言 (assertions) 是測試方法中的核心部分，用來對測試需要滿足的條件進行驗證。這些斷言方法都是 org.junit.jupiter.api.Assertions 的靜態方法，**用於檢查業務邏輯返回的數據是否合理**。JUnit 5 內置Assertions工具類內的斷言可以分成如下幾個類別：

##### 簡單斷言

| 方法            | 說明                                                     |
| --------------- | -------------------------------------------------------- |
| assertEquals    | 判斷兩個對象或兩個原始類型是否"相等"(使用equals方法判斷) |
| assertNotEquals | 判斷兩個對象或兩個原始類型是否"不相等"                   |
| assertSame      | 判斷兩個對象引用是否指向同一個對象(使用==判斷)           |
| assertNotSame   | 判斷兩個對象引用是否指向不同的對象                       |
| assertTrue      | 判斷給定的布林值是否為 true                              |
| assertFalse     | 判斷給定的布林值是否為 false                             |
| assertNull      | 判斷給定的對象是否為 null                                |
| assertNotNull   | 判斷給定的對象是否不為 null                              |

##### 數組斷言

通過 assertArrayEquals 方法來判斷兩個對象或原始類型的數組是否相等

``` java
@Test
public void arrayAssertTest() {
    int[] expect = new int[]{1, 2, 3, 4};
    int[] actual = new int[]{1, 2, 3, 4};
    Assertions.assertArrayEquals(expect, actual);
}
```

##### 組合斷言

assertAll 方法接受多個 org.junit.jupiter.api.Executable 函數式接口的實例作為要驗證的斷言，可以通過 lambda 表達式。

``` java
 @Test
public void test01(){
    Assertions.assertAll("測試max方法",
                         () -> Assertions.assertEquals(1, Math.max(1, -19)),
                         () -> Assertions.assertEquals(10, Math.abs(-10)),
                         () -> Assertions.assertTrue(Math.pow(2, 2) == 4));
}
```

##### 異常斷言

| 方法               | 說明                                       |
| ------------------ | ------------------------------------------ |
| assertThrows       | 判斷拋出的異常是否為指定類型或相容類型異常 |
| assertDoseNotThrow | 判斷程式是否沒有拋出異常                   |

##### 超時斷言

| 方法                      | 說明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| assertTimeout             | 測試可執行程序是否在給定時間內完成，可執行程序會與當前方法在**同一個線程**中，當超時發生時，**不會停止可執行程序執行** |
| assertTimeoutPreemptively | 測試可執行程序是否在給定時間內完成，可執行程序會與當前方法在**不同線程**中，當超時發生時，**停止可執行程序執行** |

``` java
@Test
public void timeoutTest() {
    System.out.println(Thread.currentThread().getName());
    Assertions.assertTimeout(Duration.ofMillis(2000)
                             , () -> {
                                 // 判斷是否為相同線程
                                 System.out.println(Thread.currentThread().getName());
                                 Thread.sleep(3000);
                                 // 判斷超時會不會繼續打印
                                 System.out.println("Hello!!");
                             });
}
```

##### 快速失敗

通過 fail 方法直接使測試失敗

```java
@Test
public void fail(){
Assertions.fail("主動失敗");
}
```

#### 前置條件(assumptions)

JUnit 5 中的前置條件，assumptions(假設)類似於斷言，不同之處在於**<font color="ff0000">不滿足的斷言會使程式測試方法失敗，而不滿足的假設會使測試方法中止執行(沒有錯誤報告)</font>**。前置條件可以看做是方法執行的前提，當前提不滿足時，也沒有繼續執行的必要了。

Assumptions 工具類假設方法：

| 方法         | 說明                   |
| ------------ | ---------------------- |
| assumeTrue   | 判斷假設是否成立       |
| assumeFalse  | 判斷假設是否失敗       |
| assumingThat | 判斷假設是否為成立失敗 |

#### 嵌套測試

JUnit 5 可以通過 Java 中的**內部類加上 @Nested 註解實現嵌套測試**，從而更好的把相關的測試方法組織在一起，而且嵌套的層次沒有限制。

@Nested 註解只能使用在 non-static 內部類上

> 外層的單元測試方法**不會**使內層的 @BeforeXXX 或 @AfterXXX 方法被調用
>
> 內層的單元測試方法**會**使外層的 @BeforeXXX 或 @AfterXXX 方法被調用

``` java
public class NestedTest {

    @BeforeAll
    public static void beforeAll() {
        System.out.println("before all test [begin]");
    }

    @AfterAll
    public static void afterAll() {
        System.out.println("after all test [end]");
    }

    @Test
    public void outTest() {
        System.out.println("out test");
    }

    @Nested
    class Inner {

        @Test
        public void innerTest() {
            System.out.println("inner");
        }

        @BeforeEach
        public void beforeEach() {
            System.out.println("before each test [begin]");
        }

        @AfterEach
        public void afterEach() {
            System.out.println("after each test [end]");
        }
    }
}

```

#### 參數化測試

參數化測試是JUnit5很重要的一個新特性，它使得用不同的參數多次運行測試程為了可能，也為我們單元測試帶來許多便利。

@XXXSource 等註解，指定方法入參，我們將可以使用不同的參數進行多次單元測試，而不需要新增一個參數就新增一個單元測試，省略很多冗餘代碼。

| 註解           | 說明                                                         |
| -------------- | ------------------------------------------------------------ |
| @ValueSource   | 為參數化測試指定入參來源，支持八大基礎類、String類型、Class類型 |
| @NullSource    | 表示未參數化測試提供一個null入參                             |
| @EmptySource   | 表示未參數化測試提供一個空字串、空數組、空集合入參。         |
| @EnumSource    | 表示未參數化測試提供一個枚舉入參                             |
| @CsvFileSource | 表示讀取指定csv文件內容作為參數化測試的入參                  |
| @MethodSource  | 表示讀取指定**靜態方法**返回值(須為一個流Stream<T>)作為參數化測試測試入參 |

> 參數化測試真正強大的地方在於它可以支持外部各類入參，如csv、json、yaml、甚至方法的返回值。只需要實現 ArgumentsProvider，任何外部文件都可以作為參數化測試的入參。

``` java
@SpringBootTest
public class ParameterTest {

    @Autowired
    JdbcTemplate jdbcTemplate;

    @ParameterizedTest
    @ValueSource(ints = {1,2,3,4,5})
    public void test01(int i){
        System.out.println(i);
        System.out.println(jdbcTemplate);
    }

    @ParameterizedTest
    @MethodSource("getAllUsers")
    public void test02(String names){
        System.out.println(names);
    }

    public static Stream<String> getAllUsers(){
        return Stream.of("James","Peter");
    }
}
```

### 指標監控

#### 簡介

未來每一個微服務在雲端部署後，需要對其進行監控、追蹤、審計、控制等。SprngBoot 就抽取了 Actuator 場景，使得我們的微服務快速引用即可獲得生產級別的應用監控、審計等功能。

``` xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### 如何使用

+ 引入 Actuator 場景依賴

+ 訪問 http://localhost:8080/actuator/\*\*(\*\*為開啟的監控端點(Endpoint))。

+ 暴露所有監控訊息為HTTP

  ``` yaml
  management:
    endpoints:
      enabled-by-default: true # 暴露所有端點訊息
      web:
        exposure:
          include: '*' # 允許以web訪問所有端點
  ```

+ 瀏覽器測試端點

  http://localhost:8080/actuator/beans

  http://localhost:8080/actuator/configprops

  http://localhost:8080/actuator/env

  http://localhost:8080/actuator/metrics

  http://localhost:8080/actuator/metrics/http.server.requests

#### Actuator Endpoint

##### 端點

| 端點               | 說明                                                         |
| ------------------ | ------------------------------------------------------------ |
| `auditevents`      | 暴露當前應用程序的審核事件訊息。需要一個 `AuditEventRepository` 組件 |
| `beans`            | 顯示應用程序中所有 Spring Bean 的完整列表                    |
| `cache`            | 暴露可用的緩存                                               |
| `conditions`       | 顯示自動配置的所有條件訊息，包含生效與不生效的原因           |
| `configprops`      | 顯示所有  `@ConfigurationProperties`                         |
| `env`              | 暴露所有屬性 `ConfigurableEnviroment`                        |
| `flyway`           | 顯示已應用的所有 Flyway數據庫遷移，需要一個或多個 Fly 組件   |
| `health`           | 顯示應用程序運形狀況信息                                     |
| `httptrace`        | 顯示 http 跟蹤訊息 ( 默認情況下，紀錄最近100個HTTP請求-響應)。需要一個 `HttpTraceRepository` 組件 |
| `info`             | 顯示應用程序訊息                                             |
| `integrationgraph` | 顯示 Spring integrationgraph。需要依賴 `spring-integrationgraph-core` |
| `loggers`          | 顯示和修改應用程序中日誌的配置                               |
| `liquibase`        | 顯示已應用的所有 Liquibase 數據庫遷移，需要一個或多個 `Liquibase` 組件 |
| `metrics`          | 顯示當前應用的「指標」訊息                                   |
| `mappings`         | 顯示所有 `@RquestMapping` 路徑訊息                           |
| `scheduledtasks`   | 顯示應用程序中的計畫任務                                     |
| `sessions`         | 允許從 `Spring Session` 支持的會話存儲中檢索和刪除用戶會話，需要使用 `Spring Session` 的基於servlet 的 web 應用程序 |
| `startup`          | 顯示由 `ApplicationStartup` 收集的啟動步驟數據。需要使用 `SpringApplication` 進行配置 `BufferingApplicationStartup` |
| `shutdown`         | 使應用程序正常關閉，默認禁用                                 |
| `threaddump`       | 執行線程轉儲                                                 |

如果你的應用程序是web應用程序 (Spring MVC、Spring Webflux 或 Jersey)，則可以使用以下附加端點：

| 端點         | 說明                                                         |
| ------------ | ------------------------------------------------------------ |
| `headdump`   | 返回 `hprof` 堆轉儲文件                                      |
| `jolokia`    | 通過 HTTP 暴露 JMX bean (需要引入 Jolokia，不適用於 WebFlux)，需要引入依賴 `jolokia-core` |
| `logfile`    | 返回日誌文件的內容(如果以設置`logging.file.name` 或 `logging.file.path` 屬性)。支持使用 HTTP Range 標頭來索引部分日誌文件的內容 |
| `prometheus` | 以 `Prometheus` 服務器可以抓取的格式公開指標。需要依賴 `micrometer-registry-prometheus` |

最常用的端點

+ Health：健康狀況
+ Metrics：運行時指標
+ Loggers：日誌紀錄

#### Health Endpoint

健康監控端點，一般用於雲平台，平台定時的檢查應用的健康狀態，我們就需要Health Endpoint可以為平台返回當前應用的一系列組件健康狀況組合

重點：

+ Health Endpoint 返回的結果，應該是一系列健康檢查後的一系列彙總報告
+ 很多健康檢查默認已經自動配置好了，例如：數據庫、redis等
+ 可以很容易的添加自定義的健康檢查機制

健康檢查端點默認返回一個匯總訊息，但是我們應用中整合許多東西，例如：數據庫、redis等等。我們想要知道是哪一個部分出現問題，這時就可以通過以下配置開啟詳細訊息

``` yaml
management:
  endpoints:
    enabled-by-default: false # 開啟所有端點監控，默認true
    web:
      exposure:
        include: '*' # 允許以web方式訪問所有端點，默認 health、info
  endpoint:
    health:
      show-details: always # 顯示健康檢查監控詳細訊息
```

> `management.endpoints.xxx` 下的配置是針對所有監控端點
>
> `management.endpoint.端點名.xxx` 是針對各別端點的配置

#### Metrics  Endpoint

提供詳細的、層級的、空間指標訊息。這些訊息可以被主動推送或被動獲取方式得到

+ 通過 Metrics 對接多種監控系統
+ 簡化核心 Metrics 開發
+ 添加自定義 Metrics 或者擴展已有的 Metrics

#### 開啟與關閉各別端點

在如何使用章節時，我們有做過以下配置，該設定會開啟所有的端點監控功能，即應用可以使用的端點。

``` yaml
management:
  endpoints:
    enabled-by-default: true # 開啟所有端點監控，默認true
```

但是有些敏感的訊息我們並不想要開放，這時我們就可以關閉所有端點，然後在通過各別端點下的 `enable` 設置開啟，這樣就可以達到控制開啟的端點。

``` yaml
management:
  endpoints:
    enabled-by-default: false # 將使用默認開啟端點改為false，我們要自己決定開啟的端點
    web:
      exposure:
        include: '*' # 允許以web訪問所有端點
  endpoint:
    health:
      show-details: always # 顯示健康檢查監控詳細訊息
      enabled: true
    info:
      enabled: true
    beans:
      enabled: true
```

#### 暴露 Endpoint

支持訪問監控端點的方式：

+ HTTP：默認只暴露 health 和 info 監控端點
+ JMX：默認暴露所有端點(cmd使用jconsole開啟)

> 除過 health 和 info，剩下的監控端點都應該進行保護訪問。如果引入 SpringSecurity，則會默認配置安全訪問規則。

#### 自定義 Endpoint

健康檢查默認的詳細訊息可能不符合你的要求，當你需要對應用中的組件做健康檢查，SpringBoot 提供自定義 EndPoint，以符合應用具體的需求。

實現 `xxxIndicator` 接口，並放置到ioc容器中，SpringBoot會自動應用這些自訂一的監控端點

##### 訂制 Health 訊息

+ 實現 HealthIndicator接口

  SpringBoot 有個 `AbstractHealthIndicator` 抽象類已經實現該接口，所以可以直接繼承該抽象類，實現`doHealthCheck`抽象方法 

+ 將實現類加入倒ioc容器中

範例：

``` java
/**
 * 針對Pet組件的健康檢查
 * 健康檢查訊息階層是：
 * components > 類名(去掉 HealthIndicator) > 詳細訊息
 */
@Component //要放入ioc容器中
public class PetHealthIndicator extends AbstractHealthIndicator {
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        boolean result = true;

        // 健康檢查邏輯
        if(result){
            builder.up(); //健康
        }else {
            builder.down(); // 不健康
        }

        // 也可以添加詳細訊息
        Map<String, String> detailMessage = new HashMap<>();
        detailMessage.put("weight", "99kg");
        detailMessage.put("message", "too heavy");

        builder.withDetails(detailMessage);
    }
}
```

##### 訂制 info 訊息

有兩種方式：

+ 編寫配置文件
+ 實現 InfoContributor 接口

###### 配置文件方式

只要在 application 配置文件 info 下編寫的訊息， info 監控端點就會顯示所定義的訊息。

``` yaml
info:
  projectName: springBootLearning
  projectVersion: 1.0.0
```

如果你想獲取pom文件中定義的版本訊息，在info中可以使用「@標籤階層@」表達式。如果你觀察pom文件的階層，它是由最外層的project標籤包裹其他標籤，所以使用「project.**.標籤名」取得pom文件定義的訊息。

``` yaml
info:
  projectName: springBootLearning
  projectVersion: 1.0.0
  # 使用 @階層@ 取得maven pom文件訊息
  mavenArtifactId: @project.artifactId@
  mavenVersion: @project.version@
  description: @project.description@
```

###### 實現 InfoContributor 接口

如果 info 信息無法一開始就定義好，而是要根據運行的狀況做改變的話，那麼直接在配置文件中寫死就不符合該需求，**SpringBoot 會將容器中實現 InfoContributor 接口的組件，也加入到 info 信息中。**

``` java
// 要放入容器中才會生效
@Component
public class MyInfo implements InfoContributor {
    @Override
    public void contribute(Info.Builder builder) {
        // 輸出 info 自定義訊息
        builder.withDetail("版本", "v1.0.0");
    }
}
```

##### 訂制 Metrics 訊息

SpringBoot 自動配置了許多指標，如果還有其他的需求，可以通過自定義的方式，添加指標。

引入 actuator 場景依賴時，裡面引入了 `micrometer-core`，提供了 metrics 訂制的相關工具。我們可以在想要做監控的位置，例如：controller、service等地方，在**構造器注入 `MeterRegistry`，進行指標的登記。**

``` java

@RestController
public class PetController {
    private final Counter counter;
    
    //構造器注入
    public PetController(MeterRegistry registry){
        // 註冊並取得 Counter
        counter = registry.counter("petService.pet01.count");

    }

    @GetMapping("/pet01")
    public Pet converterTest(@RequestParam("petInfo") Pet pet){
        // 每次呼叫 +1
        counter.increment();
        return pet;
    }
}

```

##### 自定義監控端點

除了對已有的監控端點做內容的定制以外，SpringBoot 也提供自定義監控端點。`org.springframework.boot.actuate.endpoint.annotation` 提供許多定義端點會使用到的註解

+ @Endpoint 標註在類上，指明這是一個監控端點，註解的 id 屬性指定端點的名稱
+ @ReadOperation 註解修飾在方法上(無參數)，標註該方法會在監控端點被訪問時呼叫，方法返回值可以是 Map，它會以 json 格式輸出。
+ @WriteOperation 註解修飾在方法上，可以定義端點內部的互動，讓你可以透過端點來管理整個應用。(需要透過JMX方式操作，web無法)

> 監控端點會參與應用的管理

``` java
@Component
@Endpoint(id = "myEndpoint")
public class MyEndpoint {

    @ReadOperation
    public Map<String, Object> getMessage() {
        Map<String, Object> message = new HashMap<>();
        message.put("info", "自定義監控端點");

        return message;
    }

    @WriteOperation
    public void sayHi() {
        System.out.println("Hello");
    }
}

```

#### 可視化

GitHub上的一個開源項目 [spring-boot-admin](https://github.com/codecentric/spring-boot-admin)

原理就是架設一個 spring-boot-admin 的 server，它會給註冊的應用，每過一小段時間就發送請求，獲取監控指標訊息，把它轉換為一個大的web儀表板。

在架設 server 時，需要注意以下幾點：

+ 本機同時開啟 server 和 自有專案，端口號可能會有衝突，所以須修改 server的端口號。(`server.port`)
+ 自有專案向 server 進行註冊時，默認是使用hostname方式進行註冊，但是本機名稱並不能作為域名，所以要開啟使用本機ip方式(spring.boot.admin.client.instance.prefer-ip = true)

### Profile

為了簡化多環境適配，SpringBoot 簡化了 profile 功能

> 當沒有指定環境，環境名稱是 default(默認)

#### application-profile 功能

+ 默認配置文件 application.xml **任何時候都會加載**

+ 指定環境配置文件 application-(env).xml

+ 啟動環境配置

  + 默認配置文件上指定啟動的環境

    ``` yaml
    spring:
      profiles:
          active: test #指定環境 
    ```

  + 命令行啟動

    用於項目打包後，配置文件已經設定死環境，啟動時可以通過傳遞參數切換

    ``` 
    java -jar jar檔名稱 --spring.profiles.active=環境名
    ```

+ 默認配置與環境配置同時生效

+ **同名配置項，profile配置優先** 

#### @Profile

@Profile 修飾在類上、方法上，可以根據環境條件裝配類或方法的配置。

``` java
@Configuration
public class EnvConfig {

    @Profile("prod")
    @Bean
    public Pet godzilla() {
        return new Pet("godzilla", 38d);
    }

    @Profile("test")
    @Bean
    public Pet superMan() {
        return new Pet("superMan", 100d);
    }
}

```

``` properties
spring.profiles.active=test
```

#### profile 分組

SpringBoot 提供環境標示進行分組的功能。相同組別的環境名稱以索引來加入 [0]、[1]、…等。

``` properties
spring.profiles.group.組名[0]=環境名稱
spring.profiles.group.組名[1]=環境名稱
spring.profiles.group.組名[2]=環境名稱
...
```

``spring.profiles.active`，可以指定組名，這樣可以一次性加載多個環境名稱。

``` properties
spring.profiles.active=t2
# t1組
spring.profiles.group.t1[0]=test
# t2組
spring.profiles.group.t2[0]=prod
spring.profiles.group.t2[1]=abc
```

### 外部化配置

SpringBoot 官方文檔對外部化配置講解得非常詳細，詳情請見[外部化配置(Externalized Configuration章節)](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config-typesafe-configuration-properties)。

#### 外部配置允許的資源

常用：properties文件、yaml文件、環境變量、命令行參數

#### apolication配置文件查找位置

下方以**查找順序**，列出查找的位置：

1. classpath 根路徑
2. classpath 根路徑下 config 目錄
3. jar包當前目錄
4. jar包當前目錄的 config 目錄
5. /config 子目錄的直接子目錄(linux下才能演示)

>  **當配置項有衝突時，先加載的配置項會被後加載的覆蓋掉**

#### 有無環境的配置文件加載順序

1. 當前jar包內部的 application.properties 和 application.yaml
2. 當前jar包內部的 application-{profile}.properties 和 application-{profile}.yaml
3. 引用外部jar包的 application.properties 和 application.yaml
4. 引用外部jar包的 application-{profile}.properties 和 application-{profile}.yaml

### 自定義 starter

觀察官方提供的 starter 依賴內包含哪些共通的東西，可以發現大部分的 starter 都有以下結構：

+ xxx starter 會引入 xxx AutoConfiguration 依賴
+ xxxAutoConfiguration 會將自動配置中可配置項抽取到 xxxProperties中
+ resources 下會提供 <font color="ff0000">META-INF/spring.factories</font> 文件
  + 文件內部 EnableAutoConfiguration 配置項的值，指定要加載的自動配置類

經過上方的分析，得知要創建自己的starter，需要建構兩個項目：

+ starter 項目：目的只是引入我們自定義的AutoConfiguration項目依賴
+ AutoConfiguration項目：內部有自動配置的邏輯(條件裝配)，內部會引入 spring-boot-starter 依賴

建立編寫完這兩個個項目後，將其打包至 maven 中央倉庫(install指令)中，就可以在其他SpringBoot專案，使用這個 starter 

#### 範例

+ pet-service-spring-boot-starter

  引入 pet-service-autoconfiguration 依賴

  ``` xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>org.godzilla</groupId>
      <artifactId>pet-service-spring-boot-starter</artifactId>
      <version>1.0-SNAPSHOT</version>
  
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.godzilla</groupId>
              <artifactId>pet-service-autoconfiguration</artifactId>
              <version>0.0.1-SNAPSHOT</version>
          </dependency>
      </dependencies>
  
  
  </project>
  ```

+ pet-service-autoconfiguration

  引入 spring-boot-start 依賴

  ``` java
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.4.5</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
      <groupId>org.godzilla</groupId>
      <artifactId>pet-service-autoconfiguration</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>pet-service-autoconfiguration</name>
      <description>pet service autoconfiguration</description>
      <properties>
          <java.version>1.8</java.version>
      </properties>
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter</artifactId>
          </dependency>
      </dependencies>
  
  </project>
  
  ```

  編寫*Properties類，抽取配置項

  ``` java
  @ConfigurationProperties("pet.service")
  public class PetServiceProperties {
      private String prefix = "寵物：";
      private String suffix = "say hi";
  
      public String getPrefix() {
          return prefix;
      }
  
      public void setPrefix(String prefix) {
          this.prefix = prefix;
      }
  
      public String getSuffix() {
          return suffix;
      }
  
      public void setSuffix(String suffix) {
          this.suffix = suffix;
      }
  }
  
  ```

  編寫具體starter的邏輯，正常是抽取在另外一個依賴，這裡方便演示

  ``` java
  public class PetService {
  
      private List<Pet> pets = new ArrayList<>();
  
      private final PetServiceProperties petServiceProperties;
  
      public PetService(PetServiceProperties petServiceProperties) {
          this.petServiceProperties = petServiceProperties;
      }
  
      public void registeredPet(String name, Integer age) {
          if (StringUtils.hasText(name) && age >= 0) {
              pets.add(new Pet(name, age));
          } else {
              throw new RuntimeException("registered pet fail");
          }
      }
  
      public void greet() {
          String prefix = petServiceProperties.getPrefix();
          String suffix = petServiceProperties.getSuffix();
          for (Pet pet : pets) {
              System.out.println(prefix + pet.getName() + suffix);
          }
      }
  
  }
  
  ```

  編寫自動配置類

  ```java
  @EnableConfigurationProperties(PetServiceProperties.class)
  @Configuration
  public class PetServiceAutoConfig {
  
      @Bean
      @ConditionalOnMissingBean(PetService.class)
      public PetService petService(PetServiceProperties petServiceProperties) {
          return new PetService(petServiceProperties);
      }
  }
  ```

  META-INFO/spring.factories，指定執行的自動配置類

  ``` properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    org.godzilla.auto.PetServiceAutoConfig
  ```

### SpringBoot 啟動原理

#### 啟動過程

##### 創建SpringApplication

+ 保存一些訊息
+ 判斷當前應用類型(通常是servlet)
+ 找 <font color="ff0000">BootstrapRegistryInitializer (引導程序登記處初始化器)</font>
  + 找 bootstrappers (初始啟動引導器)：**至 spring.factories 文件中找**org.springframework.boot.Bootstrapper。
+ 找 <font color="ff0000">ApplcationContextInitializer</font>：**至 spring.factories 文件中找**org.springframework.context.ApplicationContextInitializer。
+ 找 <font color="ff0000">ApplicationListener (應用監聽器)</font>：**至 spring.factories 文件中找**org.springframework.context.ApplicationListener

##### 運行SpringApplication

+ 創建 StopWatch

+ 使用 StopWatch 紀錄應用的啟動時間

+ 創建**引導程序上下文** createBootstrapContext()

  + 調用所有之前找到 bootstrapRegistryInitializer 的 initialize 方法，為引導程序上下文設置東西

+ 讓當前應用進入 **healess** 模式。**java.awt.headless**(詳情請google)

+ 獲取所有 <font color="ff0000">SpringApplicationRunListener (運行時監聽器)</font>

  + **至 spring.factories 文件中找**，SpringApplicationRunListener

+ <font color="ff0000">調用所有SpringApplicationRunListener (運行時監聽器)的 starting 方法，通知它們項目正在啟動中  </font> 

+ 保存命令行參數 ApplicationArguments

+ 準備環境

  + 返回或創建基礎環境訊息對象：StandardServletEnviroment(根據應用類型有所不同，這裡是servlet的)
  + 配置基礎環境訊息對象
    + 讀取所有配置源配置屬性值
  + 綁定環境訊息
  + <font color="ff0000">調用所有SpringApplicationRunListener (運行時監聽器)的 enviromentPrepared() 方法，通知它們當前環境準備完成 </font>

+ <font color="ff0000">創建 ioc 容器 createApplicationContext()</font>

  + 根據當前項目類型創建容器，servlet會創建 AnnotationConfigServletWebServerApplicationContext

+ 準備 ioc 容器的基本訊息 prepareContext()

  + IOC容器保存環境訊息

  + IOC容器的後置處理流程

  + <font color="ffoooo">應用初始化器，applyInitializers()</font>

    + <font color="ff0000">forEach 所有 ApplcationContextInitializer 調用initialize 方法對ioc容器進行初始化擴展</font>

    + <font color="ff0000">forEach 所有 SpringApplicationRunListener 調用 contextPrepared()，通知它們ioc容器已經「初始化完成」</font>

      > 此時ioc容器內，沒有配置的組件

  + <font color="ff0000">forEach 所有 SpringApplicationRunListener 調用 contextLoaded()，通知它們ioc容器「已經加載」</font>

+ 刷新ioc容器 reflashContext()

  + 創建ioc容器中的所有組件

+ 刷新完成後的工作 afterReflash()

+ <font color="ff0000">forEach 所有 SpringApplicationRunListener 調用 started()，通知它們當前項目已經啟動</font>

  > 此時ioc容器已經，加載所有配置的組件

+ 調用所有 runners callRunners()

  + 獲取所有ioc容器中的 **ApplicationRunner.class** 組件
  + 獲取所有ioc容器中的 **CommandLineRunner.class** 組件
  + 將所有Runner都放到集合中，並按照 @Order 排序
  + 遍歷執行所有集合中的Runner的run()方法

+ 如果以上步驟有異常

  + <font color="ff0000">forEach 所有 SpringApplicationRunListener 調用 failed()，通知它們當前項目「啟動失敗」</font>

+ 如果全部步驟都正常

  + <font color="ff0000">forEach 所有 SpringApplicationRunListener 調用 running()，通知它們當前項目「運行中」</font>

#### Application Events and Listeners

+ ApplicationContextInitializer：ioc容器創建好後，進行初始化動作

+ ApplicationListener

  應用各事件的發生時的監聽器，比SpringApplicationRunListener 還要更強大，**它不只可以監聽應用啟動時的事件**，而兩者在啟動事件發生時誰先執行，根據啟動事件會有所不同

  > 在實現該應用監聽器時，泛型指定要監聽的 ApplicationEvent 事件類，SpringBoot會再事件發生時調用該監聽器的 onApplicationEvent 方法

+ SpringApplicationRunListener

  應用啟動時，監聽各啟動流程的監聽器，會再某些重要流程執行前後被調用對應的方法。

## SpringBoot響應式編程

