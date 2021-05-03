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

+ 作用：註冊Bean或導入配置類
+ 使用位置：配置類或組件類上(@Configuration、@Component、@Controller等)
+ 屬性：
  + value：Class類型，指定要導入的類。Bean ID 為全類名。

> 配置類其實也是一個組件，所以也會被註冊IOC容器

##### @ComponentScan

+ 作用：指定組件掃描的基礎包

+ 使用位置：配置類上

+ 屬性：

  + value：全包名

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

自動導入包是什麼東西？可以查看其註解定義：

``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
	// 利用 Register 給容器導入一系列組件
    // 將主程序包下的所有組件導入
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

 查看AutoConfigurationImportSelector類`selectImports`方法，返回要導入組件名稱數組，而所有的組件名都是調用`getAutoConfigurationEntry`得到的。

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



## SpringBoot響應式編程