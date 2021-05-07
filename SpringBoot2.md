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

  `spring-boot-dependencies` 使用許多變數定義依賴所使用的版本訊息，如果不想使用默認的依賴版本，可以在自己SpringBoot項目中定義相同名稱的變數，指定需要的版本號。

  ```xml
  操作步驟：
  1. 查看spring-boot-dependencies裡面定義依賴版本使用的 key
  2. 在當前項目裡面重寫配置
  
  <properties>
      <mysql.version>5.1.43</mysql.version>
  </properties>
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

可以知道它是一個靜態內部類，通過debug可以知道`getPackageNames()`得到的就是標註了`@SpringBootApplication`所在的包名，然後通過`register()`註冊該包底下的所有組件。

這也解釋了為什麼要把組件放到與主程序同包或其子包下。

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

終於找到候選配置是從哪裡讀取，在類路徑下的`META-INF/spring.factories`，我們剛剛得到候選配置為130個，`spring-boot-autoconfigure` jar下就有該文件，# Auto Configure 註解下的的默認配置類就剛好是130項。

結論：**<font color="ff0000">SpringBoot默認將所有場景一開始就進行加載</font>**，然後在通過**按需加載**機制，只為應用加載需要的場景，會下章節介紹。

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
			

#### 按需加載

雖然SpringBoot所有自動配置默認啟動時全部加載，但是SpringBoot通過@Conditional註解來條件配置，只有所依賴的jar被導入時，這個自動配置才會生效。以下的 `AopAutoConfiguration` 就是很好的例子

​``` java
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

### 核心功能

#### yaml配置文件

SpringBoot可以在全局編寫 `application.properties` 外，還兼容YAML格式的配置文件。

##### yaml簡介

YAML 是「YAML Ain't Markup Language」(YAML不是一種標記語言)的遞歸縮寫。在開發這種語言時，YAML 的意思其實是：「Yet Another Markup Language」(仍是一種標記語言)

非常適合用來做以數據為中心的配置文件。

注意事項：

+ yaml 與 properties 配置文件可以同時存在
+ properties 的優先級高於 yaml
+ ymal配置文件的副檔名可以是「.yaml」或「.yml」

##### 基本語法

+ key: value **<font color="ff0000">kv之間有1空格</font>**
+ 大小敏感
+ 使用縮進表示層級關係
+ 縮進不允許TAB，只允許空格
+ 縮進空格數不重要，只要相同層級的元素左邊對齊即可
+ #表示注釋
+ 字符串無須加引號，如果使用「''」與「""」表示字串內容，轉義符號(\\)會不解析或解析

##### 數據類型

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

##### 範例

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

##### 顯示提示

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

###### 靜態資源目錄

SpringBoot默認靜態資源目錄放置在「類路徑下」的 `/static` (or `/public` or `resources` or `/META-INF/resources`)資料夾，ServletContext 的根目錄

+ 原理：

SpringBoot默認「靜態資源處理器」可以處理所有請求，但是**靜態資源處理器的優先級最低**。當請求到來時，會先由其他處理器來處理請求，當所有處理器都不能處理，才會交由靜態資源處理器來處理，它會去這些資料夾中找相同名稱的靜態資源，如果找不到，則404。

+ 例：

  有個Controller可以處理一個與靜態資源名稱相同的請求，那麼該請求到來時，結果是由Controller處理，而不是返回靜態資源，因Controller優先級較高且能處理。

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

#### 靜態資源配置原理

+ SpringBoot啟動時加載 `*AutoConfiguration` 類(自動配置類)

+ SpringMVC功能的自動配置類 `WebMvcAutoConfiguration`

+ 確認 `WebMvcAutoConfiguration` 是否生效

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

+ @PathVariable：獲取URL上的佔位符
+ @RequestParam
+ @RequestHeader
+ @ModelAttribute
+ @RequestBody
+ @CookieValue
+ @RequestAttribute
+ @SessionAttribute
+ @MatrixVariable

###### 矩陣註解

要使用矩陣變量需要在SpringBoot中手動開啟，根據RFC3986的規範，**<font color="ff0000">矩陣變量應該綁定在路徑變量</font>**中。若是有多個矩陣變量，應該使用「;」進行分隔，一個矩陣變量有多個值，使用「,」進行分隔，或者命名多個重複的key即可。

例如：下面兩個寫法具有相同的作用

1. `/cars/sell;low=34;brand=byd,audi,yd` 
2.  `/cars/sell;low=34;brand=byd;brand=audi;brand=yd`。

要取得路徑變量中的矩陣變量訊息，使用@MatrixVariable註解。

+ @MatrixVariable：獲取路徑變量中的矩陣變量

  + 屬性：
    + value：矩陣變量的key
    + pathVar：指定路徑變量的名稱

> **每層**路徑都可以宣告在矩陣變量中，所以才所是綁定在**路徑變量**。與我們常用的queryString不一樣，兩個可以同時使用，比較特別的變數可以定義在矩陣變量中，與查詢字串區分開。
>
> 例如：被禁用cookie後，會改由請求參數傳遞 jsessionid，可以改成使用矩陣變量方式傳遞，跟請求參數區分開來。

###### 開啟矩陣變量

SpringBoot默認關閉矩陣變量，如果要使用矩陣變量，需要手動開啟。對於URL路徑的解析是通過 `UrlPathHelper`，通過調整屬性值來開啟矩陣變量

+  `removeSemicolonContent` 是否移除URL分號內容，因為矩陣變量是使用分號來作為分隔符，所以當該屬性默認為 `true` 時，矩陣變量失效

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

1. 讓我們的配置類實現該接口，並且重寫`configurePathMatch`方法
2. 通過`@Bean`註冊至容器中

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

> 原生Srvlet API參數的解析賦值由 ServletRequestMethodArgumentResolver 這個參數解析器決定

##### 複雜參數

+ Map、Model：裡面的數據會被放置到request域中
+ Errors 、BindingResult：參數綁定數據的錯誤封裝
+ RedirectAttribute：重定向攜帶數據
+ ServletResponse：response
+ SessionStatus
+ UriComponentBuilder
+ ServletUriComponentBuilder

###### Map 參數值

+ MapMethodProcessor
+ 解析後返回 BindingAwareModelMap

###### Model參數值

+ ModelMethodProcessor 解析
+ 解析後返回 BindingAwareModelMap

> 控制器方法形參為Map、Model最終會返回相同的BindingAwareModelMap對象

##### 自定義參數類型

SpringMvc支持請求參數的封裝為自定義類型物件，在SpringBoot也是同樣的使用方式。

###### 解析

+ ServletModelAttributeMethodProcessor解析
  + 決定基礎物件
  + Web綁定器將請求參數綁定至對象屬性
    + 轉換器做類型轉換與數據格式化
  + Web綁定對物件做數據驗證
  + 驗證結果封裝至 BindingResult
+ 解析後返回自定義類型物件

###### 自定義類型轉換器

實現Converter接口，在`WebMvcConfigurer`組件中，重寫`addFormatters`方法，註冊自己的轉換器

``` java

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        
        // 實現轉換器
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

##### 響應數據

SpringBoot在處理方法返回值時，是通過各種 `HandlerMethodReturnValueHandler`(返回值處理器)來處理返回值。

###### 內容協商原理

請求頭中(Accept)會標註瀏覽器可以接收的內容類型(MediaType)，而服務器本身可以響應不同類型的數據，**瀏覽器與服務器在溝通彼此都可以接收數據類型的過程，稱之為「內容協商」。**

> HttpMessageConverter 轉換是可以雙向了，取決於內部的設定，接口內部定義了canRead和canWrite方法

1. 判斷請求頭中是否有已經確定的媒體類型(MeidaType)。(需要攔截器)
2. 獲取客戶端支持接收的媒體類型。。
   1. 由內容協商管理器(`ContentNegotiationManager`)，它默認策略(`HeaderContentNegotiationStrategy`)是解析請求頭的Accept訊息
   2. 內容協商策略可以通過 `WebMvcConfigurer` 的`configureContentNegotiation`方法增加。
3. 遍歷所有`HttpMessageConverter`(消息轉換器)，將支持控制器方法返回值的消息轉換器可以產出的媒體類型記錄下來，統計服務器可提供的媒體類型。
4. 客戶端可以接受的媒體類型與服務器可以提供的媒體類型，取其「交集」，決定最終要使用的媒體類型。
5. 根據客戶端所提供的媒體類型權重排序，權重較高者優先級越高
6. 遍歷所有`HttpMessageConverter`(消息轉換器)，看哪個支持控制器方法返回值類型又支持最終媒體類型，決定要最終要使用的消息轉換器。
7. 使用消息轉換器處理方法返回器。 

先前在數據綁定器中，介紹到類型轉換器，主要用於將請求數據轉換為方法的形參。而在這邊介紹到的`HttpMessageConverter`(消息轉換器)，是將方法返回值，轉為瀏覽器能接收的數據類型。

###### 開啟瀏覽器參數方式內容協商功能

在不使用 ajax 情況下，無法在瀏覽器設定自己想要的請求頭，SpringBoot支持以參數方式傳遞內容協商功能。**<font color="ff0000">參數方式只支持兩種媒禮類型，`xml`、`json`。</font>**

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

+ 編寫實現HttpMessageConverter接口實現類

+ 實現類註冊進底層的MessageConverter數組(通過WebMvcConfigurer)

  ``` java
  // 配置MessageConverter，會替換掉所有默認MessageConverter
  // converters 內預設沒任何元素
  default void configureMessageConverters(List<HttpMessageConverter<?>> converters) {}
  // 擴展MessageConverter，可以增加修改底層的MessageConver
  // converters 內有默認的MessageConverter
  default void extendMessageConverters(List<HttpMessageConverter<?>> converters) {}
  ```

範例：







###### 響應JSON

想要讓 SpringBoot響應JSON，首先需要兩步驟

+ 引入 spring-boot-starter-web 場景(裡面會自動引入 spring-boot-start-json 場景)
+ 使用相關註解，有以下方式
  + Controller上標註`@RestController`，表示類方法都被@ResponseBody註記。`@RestController` 相當於 @ResponseBody 與 @Controller 組合
  + Controller方法上使用 `@ResponseBody`標註，表示方法返回值為ResponseBody
  + 方法返回值宣告為 ResponseEntity，代表響應，可以設定響應頭、響應體、響應狀態碼。

`MappingJackson2HttpMessageConverter` 可以處理 JSON 的 MimeType

## SpringBoot響應式編程

