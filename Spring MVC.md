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

+ Spring MVC 隨著 Spring3.0 的發布，全面超越 Struts2。成為**最優秀的 MVC 框架**。
+ 通過一套**MVC註解**，讓一個簡單的Java類成為處理請求的控制器，無須實現任何接口。
+ 支持 **RESTful 編程風格**的URL請求。
+ 採用鬆散耦合可插拔設計結構，比其他MVC框架更具擴展性和靈活性。

#### Spring 快速入門

需求：客戶端發起請求，服務端接收請求，執行邏輯並進行視圖跳轉

開發步驟：

1. 導入 `spring-webmvc` 依賴

2. web.xml 配置 SpringMVC 核心控制器 `DispathcerServlet`

   `DispatcherServlet` 就是一個 Servlet，配置與一般 Servlet 無異。需要注意的是 `load-on-start`需要配置，因為需要在服務器啟動就創建前端控制器，這樣它才能在請求時進行調度工作。

   第二個需要注意的點是前端控制器的`url-pattern`配置，下面解釋`/`與`/*`的區別：

   + `/`：攔截所有請求，範圍較`/*`小，不攔截對JSP頁面的請求。
   + `/*`：攔截所有請求，包括`*.jsp`這種對頁面的請求，而這些應該是交給 tomcat 處理。

   可是前端控制配置`url-pattern`為`/`，還是無法.html等靜態文件，原因如下：

   > Tomcat服務器中也有web.xml配置，簡稱大xml。每個web項目中也有個web.xml配置，簡稱小xml。**<font color="ff0000">所有的小xml都會繼承大xml的設定，如果小xml設定與大xml衝突，則以小xml為主。</font>**
   >
   > 如果去翻閱大xml的內容，當中有個`DefaultServlet`的配置，它的用途是用來處理靜態文件，它的`url-pattern`也是 `/`，而在使用 SpringMVC 在配置前端控制器時的`url-pattern`也是 `/`，這就導致設定被**覆蓋**，前端控制器接收到靜態文件請求時，會去找是否有與該靜態文件名稱相同的`@RequestMapping`註記方法來處理，因為根本沒有這個東西，所以異常發生。問題在之後會有解決辦法。
   >
   > 那為什麼JSP文件的請求可以呢？查看大xml，可以找到`JspServlet`配置它的`url-pattern`為`*.jsp`，用來負責處理JSP，前端控制器的`url-pattern`沒有與其衝突，`*.jsp`請求還是由`JspServlet`處理，這就是JSP可以正常請求的原因。
   >
   > **而當你將前端控制器的`url-pattern`設置為`/*`時，它的優先級是最高，所以`/`與`*.jsp`的請求都會被前端控制器接收到，所有的靜態文件與JSP請求都會有問題。**

   > 配置 `DispathcerServlet`時，需要配置初始化參數 `contextConfigLocation` 目的是指定SpringMVC 的配置文件的位置。Spring 的配置文件最好與 SpringMVC 分開，方便以後做維護。
   > <font color="FF0000">如果不配置初始化參數，Spring MVC 會去找 WEB-INF 資料夾底下檔名為的 前端控制器名-servlet.xml檔案。 </font>

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
          <!-- url-pattern配置"/"，攔截所有請求，spring mvc 前端控制器會根據註解進行 forward 			/*和/都是攔截所有請求，但是/*的範圍更大，它會攔截到對*.jsp的請求，所以配置 / 就好
  -->
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

### @RequestMapping 註解解析

`@RequestMapping`

+ 作用：用於將請求中請求參數、請求方式(post、get …)、請求頭，映射到具體的方法上。

+ 使用位置

  + 類上：請求URL的一級訪問目錄，相當於**設置該類下所有方法的基準路徑**。此處不寫的話，相當於應用的根目錄。
  + 方法上：請求URL的二期訪問目錄，相當於**設置該方法的訪問路徑**。會與類上設定的基準路徑一起組成完整的訪問路徑。

   例如以下要訪問以下的`doSave`方法需要使用`/user/save`

  > 不論是類還是方法上的`@RequestMapping`，都可以去掉開頭`/`，SpringMVC還是會為你加上。
  > **建議`@RequestMapping`開頭都以`/`開頭**

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController{
  	@RequestMapping("/save")
      public void doSave(){
          // dosomething...
      }
  }
  
  /**
   * 如果方法上開頭少了/，類上指定/user，那你覺得要如何訪問該方法呢?
   * 1. /usersave
   * 2. /user/save 
   * 答案是 /user/save，因為 @RequestMapping 指定的是每層的路徑。
   * SpringMVC 會自動幫你加上 "/"，如果你的類、方法不以 "/" 作為開頭的話
   */
  @Controller
  @RequestMapping("/user")
  public class UserController{
  	@RequestMapping("save")
      public void doSave(){
          // dosomething...
      }
  }
  ```

+ 屬性

  + value：用於指定方法訪問請求url。
  
    > 當傳給註解的屬性值為value，且只有這一個時，`value=`可以省略，所以直接寫在`@RequestMapping`括號中的`url-pattern`，就是指定`value`屬性的值。
    > 當需要指定多個屬性時，那麼`value=`就不可指定。多個屬性間用`,`分開。
  
  + method：用於**限定請求的方式**。指定`RequestMethod`枚舉類物件
  
    ```java
    /*
     * 使用method屬性限定該方法只能接受POST方式的請求
     */
    @RequestMapping(value="/user", method = RequestMethod.POST)
    public String doSave(){
        System.out.println("execute userController");
        userService.doSave();
        return "success.jsp";
    }
    ```
  
    
  
  + params：**限定請求傳遞的參數**。它支持簡單的表達式，要求請求參數的key和value必須一致。
    + `params={"accountName"}` 表示請求參數**必須有** `accountName`
    
    + `params={"!accountName"}` 表示請求參數**不能有** `accountName`
    
    + `params={"money=100"}` 表示請求參數中`money`值是100
    
    + `params={"money!=100"}` 表示請求參數中`money`值不能是100或沒有`money`參數
    
      > `/update?abc=123` 此時username的值為 null
      >
      > `/update?username=` 此時username的值為空字串
    
  + path：與value屬性相同作用。
  
  + headers：規定請求頭，和`params`一樣，可以寫簡單的表達式。例如可以通過`User-Agent`限制訪問的瀏覽器
  
  + consumes：只接收內容類型是哪種類型的請求，規定**請求頭**中的`Content-Type`
  
  + produces：告訴瀏覽器返回的內容類型是什麼，相當於給**響應頭**加上`Content-Type`

#### Ant風格的資源地址

Ant 為古老的構件工具，它能通過一些符號來模糊比對硬碟中的資源。Spring 也借鏡了它優點，將它使用到**`url-pattern`的模糊比對**。

Ant通配符號有以下幾種：

+ ?：能替代任意**一個**字符

+ *：能替代0~N個字符或一層路徑。

+ **：能替代0~N層路徑。

  > 當一個請求同時符合多個`url-pattern`模糊比對時，採用相對嚴格那個。

範例：假設項目為springmvc

+ `/test01`：精確路徑，只能匹配 `/spring/test01`
+ `/test0?`：能將`?`替換為任意字元，不匹配`/spring/test0`(沒有字元)、`/spring/test111`(超過一個)
+ `/test0*`：可以將`*`替換為0~N個字元，但是不匹配`/test00/hello`(路徑)
+ `/a/*/c`：可以將`*`替換為N個字元，但是不能省略該層路徑。例如：不匹配`/a/c`或`/a//c`
+ `/a/**/z`：可以將`**`替換為0~N層路徑。例如：`/a/z`或`/a/b/c/z`。

#### @PathVariable 映射 URL 綁定的占位符

帶占位符的 URL 是 Spring 3.0 新增的功能，該功能在 SpringMVC 向 REST 目標挺進發展過程中具有里程碑的意義。

`@PathVariable` 可以將 URL 中占位符參數綁定到 controller 方法的形參中。URL 中的 {xxx} 占位符可以綁定通過 `@PathVariable("xxx")` 綁訂到方法形參中。

``` java
/**
 * 測試路徑上占位符獲取
 */
@RequestMapping("/user/{name}")
public String mapping03(@PathVariable("name") String name){
    System.out.println("mapping03 working...");
    System.out.printf("URL占位符為: %s", name);
    return "/success.jsp";
}
```

> 路徑上的佔位符只能占一層路徑，且該層不能省略

#### REST思想

REST 是 Representational State Transfer 的縮寫。可譯為「具象狀態傳輸」。REST是一種軟體架構風格，目的是幫助在世界各地不同軟體、程式在網際網路中能夠互相傳遞訊息。任何東西都可以視為資源(Resource)，用來唯一標示網際網路上的資源使用的是 URL，而使用者的請求無非就是獲取資源或改變資源的狀態。

在最初HTTP誕生時，REST就跟著問世了，對資源進行的操作與HTTP請求的方式相互呼應。

+ <font color="ff0000">GET：讀取資源</font>
+ <font color="ff0000">PUT：替換資源</font>
+ PATCH：替換資源部分內容
+ <font color="ff0000">DELETE：刪除資源</font>
+ <font color="ff0000">POST：新增資源</font>
+ OPTIONS：回傳該資源所資源的 HTTP 請求方式
+ CONNECT 將連接請求轉換至 TCP/IP 隧道

使用HTTP來表達語意的路由設計風格稱為 RESTful API，以「資源」為中心，在搭配 HTTP method 的動詞，以及 CRUD 等資料操作。

對資料的操作，不外乎是：新增(Create)、讀取(Read)、更新(Update)、刪除(Delete)四種動作，通常會簡稱為 CRUD。而HTTP method 是有意義的動詞，如GET、PUT、DELETE、POST等。

固定的結構是：

- 瀏覽全部資料：GET + 資源名稱
- 瀏覽特定資料：GET + 資源名稱 + :id
- 新增一筆資料：POST + 資源名稱
- 修改特定資料：PUT + 資源名稱 + :id
- 刪除特定資料：DELETE + 資源名稱 + :id

下面是有無使用REST思想來構建API的區別：

``` java
// no REST API
/getBooks?id=1 //取得id=1的書籍資料
/updateBooks?id=1&name=HelloWorld //更新id=1的書籍
/insertBook?name=JAVA8&auth=Peter //新增一本書，書籍訊息就是參數值
/deleteBooks?id=1 // 刪除id=1的書籍
// RESTful API
/books/1 //使用GET方式 表示取得id=1的書籍
/books/1?name=HelloWorld //使用PUT方式，表示更新id=1的書籍
/books?name=JAVA&auth=Peter //使用POST方式 表示新增一本書
/books/1 //使用DELETE方式 表示刪除id=1的書籍
```

看完上面的差異，可以發現REST的URL更好理解，也很有規律。當然不可能所有的請求都能完美對齊 RESTful。例如；登入通常設定為`PUT/login`，完全是以動詞為中心。身為開發者，有時候還是須因地制宜，風格只是參考，不是阻礙。

> 從網址上，你會看出要操作的資源叫做 **books**，這邊習慣用複數名詞。

#### Spring 對 REST 的支持

在頁面上不通過 Ajax，只能發送`PUT`、`POST`請求。SpringMVC 中有一個 `HiddenHttpMethodFilter`，它可以把普通的請求轉化為規定形式的請求。

操作步驟：

1. 配置 Filter

   ``` xml
   <filter>
       <filter-name>hiddenHttpMethodFilter</filter-name>
       <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
   </filter>
   <filter-mapping>
       <filter-name>hiddenHttpMethodFilter</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
   ```

2. 發起請求

   發起請求時必須依照以下要求

   1. 建立一個使用 POST 方式的表單。
   2. 請求發送時攜帶一個`_method`的參數。
   3. `_method`的值就是 HTTP 請求方式。例如：DELETE、PUT。



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

`ModeAndView` 顧名思義，就是由Mode(模型)和View(視圖)組成。模型用來封裝數據，視圖用來展示數據。常用得方法如下：

+ setViewName(String name) 設置視圖名稱，與頁面跳轉時回傳字符串一樣效果。
+ addObject(String attributeName, Object attributeValue)  設置屬性至`request`域中

###### 範例一：手動創建ModelAndView對象

```java
/**
 * 頁面跳轉 - 返回ModeAndView方式
 *
 */
@RequestMapping("/delete")
public ModelAndView doDelete(){
    ModelAndView modelAndView = new ModelAndView();
    // 設置要跳轉的視圖路徑位置
    modelAndView.setViewName("/success.jsp");
    // 設置模型數據
    modelAndView.addObject("username","James");
    System.out.println("delete running");
    return modelAndView;
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Hello</title>
</head>
<body>
    <h1>Hello SpringMVC</h1>
    <h2>request ${requestScope.get("username")}</h2>
    <h2>session ${sessionScope.get("username")}</h2>
    <h2>application ${applicationScope.get("username")}</h2>
    <h2>page ${pageScope.get("username")}</h2>
</body>
</html>
```

範例二：由 SpringMVC 注入`ModelAndView` 對象

> 將ModelAndView宣告為方法的參數，SpringMVC 在調用該方法時，發現方法需要ModelAndView對象，就會幫創建並注入該對象

```java
/**
 * 由Spring mvc 幫你注入 ModelAndView 對象
 */
@RequestMapping("/update")
public ModelAndView doUpdate(ModelAndView modelAndView){
    modelAndView.addObject("username","Tommy");
    modelAndView.setViewName("/success.jsp");
    return modelAndView;
}
```

範例三：由 SpringMVC 注入 `Model` 對象，方法返回字符串。

這種方式 Spring MVC 仍然可以設置屬性到 `request` 域中，因為 `Model` 對象事由 Spring MVC 幫你注入的，你不必返回也能得知該對象內部的訊息。

``` java
/**
 * 由Spring mvc 幫你注入 Model 對象
 */
@RequestMapping("/insert")
public String doInsert(Model model){
    model.addAttribute("username", "Peter");
    return "/success.jsp";
}
```



