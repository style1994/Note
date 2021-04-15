# Spring MVC

### 環境搭建

1. 導入依賴 `javax.servlet-api`、`javax.servlet.jsp-api`
2. 搭建三層架構`controller`、`service`、`dao`
3. `Spring`配置

### ApplicationContext應用上下文獲取方式

如果每個 `controller` 都通過創建 Spring 容器獲取`ApplicationContext`，這樣有個嚴重的弊端，每當請求來到時就創建一個新的容器，嚴重影響效能。

在web項目中，可以使用**`ServletContextListener`**監聽web應用的啟動，可以在web應用啟動時，就加載Spring 配置創建`ApplicationContext`對象，並將其存入最大的域**`servletContext`**域中，這樣就可以在任意的位置從域中獲取`ApplicationContext`對象，達到共享的目的。

#### 操作步驟

1. 編寫`ServletContextListener`實現類
2. web.xml 配置剛編寫的監聽器
3. 修改`controller`中獲取`ApplicationContext`的方式

#### 範例

```java
public class ContextLoadListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        ServletContext servletContext = sce.getServletContext();
        servletContext.setAttribute("app", app);
        System.out.println("Spring容器初始化完成");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置監聽器 -->
    <listener>
        <listener-class>org.learning.listener.ContextLoadListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>userController</servlet-name>
        <servlet-class>org.learning.controller.UserController</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>userController</servlet-name>
        <url-pattern>/user</url-pattern>
    </servlet-mapping>
</web-app>
```

```java
public class UserController extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = getServletContext();
        ApplicationContext app = (ApplicationContext) servletContext.getAttribute("app");
        UserService service = app.getBean(UserService.class);
        service.doSave();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}
```

#### 範例優化

`ContextLoadListener`類中的 Spring 配置文件名寫死了，可以通過讀外部文件的方式來解偶，web.xml 可以定義全局初始化參數，通過讀取全局初始化參數的方式，設定配置文件名稱。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置全局初始化參數 -->
    <context-param>
        <param-name>springConfigPath</param-name>
        <param-value>applicationContext.xml</param-value>
    </context-param>
    <!-- 配置監聽器 -->
    <listener>
        <listener-class>org.learning.listener.ContextLoadListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>userController</servlet-name>
        <servlet-class>org.learning.controller.UserController</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>userController</servlet-name>
        <url-pattern>/user</url-pattern>
    </servlet-mapping>
</web-app>
```

```java
public class ContextLoadListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        String springConfigPath = servletContext.getInitParameter("springConfigPath");
        ApplicationContext app = new ClassPathXmlApplicationContext(springConfigPath);
        servletContext.setAttribute("app", app);
        System.out.println("Spring容器初始化完成");
    }
}
```

在取得Spring上下文對象`ApplicationContext`也必須進行封裝，提供一個工具類給使用者，這樣使用者不必知道`ApplicatContext`是使用什麼 key 存放在 `ServletContext` 域這些細節，方便以後的維護。

```java
public class SpringConfigUtils {
    public static ApplicationContext getApplicationContext(ServletContext servletContext){
        return (ApplicationContext) servletContext.getAttribute("app");
    }
}
```

#### Spring提供獲取應用上下文工具

Spring 其實提供了工具方便開發者獲取應用程序上下文 `ApplicationContext`，Spring 所做的事情其實和上面的範例大致相同。Spring 通過 `ContextLoaderListener` 就是對上述功能的封裝，該監聽器內部加載 Spring 配置文件，創建應用程序上下文對象，並儲存到`ServletContext`域中，提供了一個工具類 `WebApplicationContextUtils` 供使用者獲得應用上下文對象。

想要使用Spring 提供的工具，需要以下操作步驟：

1. 導入 `spring-web` 依賴
2. web.xml 中配置 `ContextLoaderListener`監聽器，並配置全局初始化參數`contextConfigLocation`指定配置文件位置。
3. 通過 `WebApplicationContextUtils`獲得應用程序上下文對象`ApplicationContext`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!-- 配置初始化參數 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!-- 配置監聽器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>userController</servlet-name>
        <servlet-class>org.learning.controller.UserController</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>userController</servlet-name>
        <url-pattern>/user</url-pattern>
    </servlet-mapping>
</web-app>
```

### Spring MVC 簡介

#### Spring MVC 概述

Spring MVC 是一種基於 Java 實現 **MVC設計模型**的請求驅動類型的**輕量級Web框架**，屬於 Spring FrameWork 的後續產品，已經融合在 Spring Web Flow 中。

Spring MVC 已經成為目前最主流的MVC框架之一，並且隨著 Spring3.0 的發布，全面超越 Struts2。成為最優秀的 MVC 框架。它通過一套註解，讓一個簡單的Java類成為處理請求的控制器，無須實現任何接口。同時它還支持 **RESTful 編程風格**的請求。

#### Spring 快速入門

需求：客戶端發起請求，服務端接收請求，執行邏輯並進行視圖跳轉

開發步驟：

1. 導入 `spring-webmvc` 依賴

2. web.xml 配置 SpringMVC 核心控制器 `DispathcerServlet`

   > 配置 `DispathcerServlet`時，需要指定初始化參數`contextConfigLocation`指定SpringMVC的配置文件位置。
   > Spring 的配置文件最好與 SpringMVC 分開，方便以後做維護。

3. 編寫 `controller` 類和 `view`

   `controller` 類中方法返回值如果為`String`，Spring 會自動`forward`到**指定路徑**下的視圖。

   > 需要注意相對位址與絕對位址的書寫，根目錄("/") 相當於webapp資料夾。

4. 使用註解配置 `controller` 類中業務方法的映射地址(`url pattern`)

   使用`@Controller`註解將類加入Spring容器管理，`@RequestMapping`註解在方法上指定`url-pattern`

5. 配置 SpringMVC 核心配置文件 spring-mvc.xml

   > 配置組件掃描時，建議只掃描`controller`類所在包下的。不要一併掃描`dao`、`service`，因為那是屬於 Spring 配置的領域。SpringMVC 配置只做有關於 SpringMVC 的配置就好。

6. 客戶端發起請求並測試

#### 快速入門案例

+ maven 依賴

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.5</version>
  </dependency>
  ```

+ web.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <!-- 配置初始化參數 -->
      <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:applicationContext.xml</param-value>
      </context-param>
  
      <!-- 配置監聽器 -->
      <listener>
          <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      </listener>
  
      <!-- 配置spring mvc 前端控制器 -->
      <servlet>
          <servlet-name>dispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!-- 配置局部初始化參數，目的告知spring mvc 配置文件位置 -->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:spring-mvc.xml</param-value>
          </init-param>
          <!-- 服務器啟動時就載入 -->
          <load-on-startup>1</load-on-startup>
      </servlet>
      <servlet-mapping>
          <servlet-name>dispatcherServlet</servlet-name>
          <!-- url-pattern配置"/"，攔截所有請求，spring mvc 前端控制器會根據註解進行 forward -->
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

+ spring-mvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
      <!-- 配置組件掃描(只掃controll包)-->
      <context:component-scan base-package="org.learning.controller"/>
  </beans>
  ```

+ UserController

  ```java
  @Controller
  public class UserController {
      @Autowired
      UserService userService;
  
      //指定方法的 url-pattern
      @RequestMapping("/user")
      public String doSave(){
          System.out.println("execute userController");
          userService.doSave();
          // spring mvc 前段控制器接收到字串返回值時，會forward相同指定路徑的視圖。
          // 這裡是以相對路徑指定視圖資源
          return "success.jsp";
      }
  }
  
  ```

+ success.jsp

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>Hello</title>
  </head>
  <body>
      <h1>Hello SpringMVC</h1>
  </body>
  </html>
  ```

#### SpringMVC 流程

![image-20210415160929518](images/Spring MVC/image-20210415160929518.png)

### SpringMVC 組件解析

![image-20210415162449207](images/Spring MVC/image-20210415162449207.png)

從上面的流程圖可以得知，`DispatcherServlet`只負責分派任務，映射、處理、視圖解析都是由不同的組件去處理，最後將結果返回給使用者。

1. 用戶請求至前端控制器`DispatherServlet`
2. 調用`HandlerMapper`處理器映射器進行資源映射
3. `HandlerMapper`根據XML配置、註解找到具體的處理器，生成處理器對象即處理器攔截器(如果有就生成)，然後將這些對象全部返回給`DispatcherServlet`
4. `DispatcherServlet`將這組處理器交由`HandlerAdaptor`處理
5. `HandlerAdaptor`調用每個處理器進行處理，最後返回`ModelAndView`給`DispatherServlet`
6. `DispatherServlet` 將接收到的 `ModelAndView` 交由視圖解析器`ViewResolver`處理
7. `ViewResolver`解析後返回具體的視圖給`DispatherServlet`
8. `DispatherServlet`根據視圖進行渲染(將數據模型填充至視圖中)，最終響應給用戶

### SpringMVC 註解解析

`@RequestMapping`

+ 作用：用於建立請求URL和請求方法之間的對應關係。

+ 使用位置

  + 類上：請求URL的一級訪問目錄，此處不寫的話，相當於應用的根目錄。
  + 方法上：請求URL的二期訪問目錄，與類上的`@RequestMapping`標註的一級目錄一起組成訪問虛擬路徑。

   例如以下要訪問以下的`doSave`方法需要使用`/user/save`

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController{
  	@RequestMapping("/save")
      public void doSave(){
          // dosomething...
      }
  }
  ```

+ 屬性

  + value：用於指定請求url，與path屬性相同作用
  + method：用於指定請求的方式。指定`RequestMethod`枚舉類物件
  + param：限定請求參數的條件。它支持簡單的表達式，要求請求參數的key和value必須一致。
    + `params={"accountName"}` 表示請求參數必須有 `accountName`
    + `params={"money!100"}` 表示請求參數中`money`不能是100
  + path：與value屬性相同作用。

### 進階組件掃描

之前都有學習過使用`<context:component-scan>`標籤通過`base-package`屬性指定包名，進行組件掃描。其實它內部還有兩個子標籤`context:exclude-filter`和`context:include-filter`用來更詳細的指定掃描時包含或不包含的範圍。

它們都有兩個屬性：

+ type：指定表達式的類型。常見的有annotation、aspectj、regex等。
+ expression：表達式的內容。

### SpringMVC XML配置解析

`DispatcherServlet`調用的組件都是可以通過XML來自定義配置的，默認使用元件類都設定在`org.springframework.web.servlet`包下的`DispatcherServlet.properties`文件中。

SpringMVC 可配置的東西很多，這裡舉個例子。

+ 通過查看默認視圖解析器`ViewResolver`，`InternalResourceViewResolver`及它的父類`UrlBasedViewResolver`，可以注意到有`prefix`和`suffix` 兩個成員變量。可以通過set注入配置這兩個變量的值，它會將我們`@RequestMapping`標註方法返回的字串加上前綴和後綴。

  同時可以注意的`UrlBasedViewResolver`類中的兩個常量`REDIRECT_URL_PREFIX`、`FORWARD_URL_PREFIX`。通過英文可以大致了解它的含意，在**<font color="FF0000">返回視圖名稱時可以通過指定這兩個常量的值來決定到該視圖的方式，使用`forward`前綴加上`foward:`，使用`redirect`前綴加上`redirect`</font>**

### SpringMVC的數據響應

#### Spring MVC 的數據響應方式

+ 頁面跳轉
  + 直接返回字符串
  + 通過ModelAndView對象返回
+ 傳回數據
  + 直接返回字符串
  + 返回對象或集合

##### 頁面跳轉-直接返回字符串

此種方式會將返回的字符串與視圖解析器`ViewResolver`的前後綴拼接後跳轉。亦可以通過指定`foward:`進行轉發或`redirect:`進行重定向。

配置視圖解析器的前後綴為，那麼方法返回字串值`index`。返回值與前後綴拼接起來為 `/WEB-INF/index.jsp`，最後將轉發到該視圖。

```xml
<property name="prefix" value="/WEB-INF/jsp/" />
<property name="suffix value=".jsp" />
```

SpringMVC 默認的跳轉方式為`forward`，以下範例可以指定跳轉方式：

+ 轉發：`forward:/index.jsp`
+ 重定向：`redirect:/index.jsp`

##### 頁面跳轉-返回ModelAndView對象

```

```



