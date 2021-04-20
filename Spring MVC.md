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

在頁面上不通過 Ajax，只能發送`PUT`、`POST`請求。spring-mvc 提供 `HiddenHttpMethodFilter`，它可以把POST請求轉化為其他HTTP請求方式。

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

#### Tomcat8以上JSP無法接受PUT、DELETE解決方式

當web容器為Tomcat 8 以上，設計RESTful API 時，會發現通`HiddenHttpMethodFilter` 過濾器轉換後確實可以讓後端控制器方法接收到 `PUT`、`DELETE` 方式請求，但是處理完請求還是會使用 `PUT`、`DELETE` 方式轉發到JSP頁面時，Tomcat 會因為無法處理 `PUT`、`DELETE` 而出現異常。

目前有4種方法解決：

1. 使用 Tomcat7：沒有實質解決問題

2. 設置目標頁面為錯誤頁面：錯誤訊息會被封裝在JSP內置物件error中，所以看不到轉發錯誤訊息。還是出現錯誤了，沒有根本解決問題。

   ``` jsp
   <%@ page isErrorPage="true" %>
   ```

3. 使用重定向(Redirect)：可以在控制器方法的字串返回值加上前綴 `redirect:`，指定使用重定向。

4. 使用`@ResponseBody`：在負責處理`PUT`、`DELETE`請求的控制器方法加上該註解，但只能返回方法返回值，無法進行轉發。

### 請求處理

#### 處理請求參數

假設請求的 URL `http:localhost:8080/springmvc/books?name=Java8`。從URL得知 username 請求參數 ，其值為 Java8。

獲取請求參數有以下方式：

1. spring-mvc 默認的方式：

   直接在控制器的方法加上與請求參數同名的方法參數。當沒有該請求參數時，方法參數值為 `null`。

   ```java
   @RequestMapping(value = "/books", method = RequestMethod.POST)
   public String insertBook(String username) {
       System.out.println("username: " + username);
       System.out.println("insertBook working");
       return "/success.jsp";
   }
   ```

2. `@RequestParam`註解

   使用 `@RequestParam` 註解在方法形參上，並在註解的`value`屬性指定請求參數的名稱。作用會獲取對應的請求參數並注入到註解修飾的方法參數上。該方式使請求參數與方法形參不同名稱。

   `@RequestParam`屬性：

   + value：指定請求參數的值
   + required：這個參數是否為必須的。默認值為`true`
   + defaultValue：當沒有該請求參數時，設置其默認值。默認值為`null`。

   ``` java
   /**
    * 註解可以讓請求參數與方法形參不同名稱，且可以指定默認值
    */
   @RequestMapping(value = "/books", method = RequestMethod.POST)
   public String insertBook(
       @RequestParam( value = "username", required = false, defaultValue = "Peter") String name) {
       System.out.println("username: " + name);
       System.out.println("insertBook working");
       return "/success.jsp";
   }
   ```

   > 當 required 默認是 true，且沒有該請求參數時，會出現異常

#### 處理請求參數

+ `RequestHeader`

  + 作用：用於獲取請求頭值，可以取代之前的操作：`request.getHeader("User-Agent")`。

  + 使用位置：控制器方法形參

  + 屬性：

    + value：指定請求參數的值
    + required：指定請求頭是否必須存在，默認值為 `true`。
    + defaultValue：當沒有該請求 header 時，設置其默認值。預設默認值為`null`。

  + 範例：

    ``` java
    @RequestMapping(value = "/books/{id}", method = RequestMethod.PUT)
    @ResponseBody
    public String updateBook(@PathVariable("id") String id, @RequestHeader("User-Agent") String userAgent) {
        System.out.println("瀏覽器訊息：" + userAgent);
        System.out.println("updateBook working");
        System.out.println("books id is " + id);
    
        return "/success.jsp";
    }
    ```

    > 當 required 屬性值為 true 但是 請求頭不存在時，`MissingRequestHeaderException` 異常

#### 處理Session

+ `@SessionAttrubute`

  + 作用：獲取 `session` 域中存放的屬性值。

  + 使用位置：方法形參

  + 屬性：

    + value：指定 session 域中屬性的 key 值
    + required：session 屬性是否為必須存在，預設默認值為`true`。

  + 範例：

    ```java
    @RequestMapping("/test05")
    public String test05(@SessionAttribute("hello") String attr){
        System.out.println(attr);
        return "/success.jsp";
    }
    ```

#### 處理請求 Cookie

+ `@CookieValue`

  + 作用：獲取請求 cookie 值。

    ``` java
    // Servlet 獲取 cookie 操作
    Cookie[] cookies = request.getCookies();
    for(Cookie c : cookies){
        if(c.getName().equals("JSESSIONID")){
            String value = c.getValue();
        }
    }
    ```
  ```
  
  ```
  
+ 使用位置：方法形參
  
+ 屬性：
  
    + value：請求 cookie 的 key。
    + required：cookie 是否為必須，默認值為`true`。
  + defaultValue：當沒有該 cookie 時，給定其默認值。預設默認值為`null`。
  
+ 範例：
  
    ```java
    @RequestMapping(value = "/books/{id}", method = RequestMethod.PUT)
    @ResponseBody
    public String updateBook(
        @PathVariable("id") String id, 
        @RequestHeader("User-Agent") String userAgent,
        @CookieValue("JSESSIONID") String jid) {
        
        System.out.println("瀏覽器訊息：" + userAgent);
        System.out.println("SESSION值: " + jid);
        System.out.println("updateBook working");
        System.out.println("books id is " + id);
    
        return "/success.jsp";
    }
    ```

#### 封裝請求參數為POJO對象

請求參數較少時，使用 `@requestParam` 非常方便；當請求參數數量非常多時，使用 `@requestParam` 來一個個獲取請求參數，不只麻煩，還會讓方法參數過多。常見的處理方式就是將請求參數歸類並封裝為POJO物件。

spring-mvc 有自動封裝請求參數為相應的對象功能，**它會嘗試映射同名的請求參數值到Java對象中同名的屬性中**，而那些沒有映射到的屬性就維持其原值。

假設有以下表單要提交：

``` html
<form method="post" action="/books">
    書籍訊息：<br>
    書名：<input type="text" name="bookName"><br>
    作者：<input type="text" name="author"><br>
    價格：<input type="text" name="price"><br>
    庫存: <input type="text" name="stock"><br>
    <hr>
    地址訊息：<br>
    郵遞區號：<input type="text" name="address.postalCode">
    城市： <input type="text" name="address.city">
</form>
```

注意表單中地址訊息中郵遞區號與城市`input`標籤的`name`屬性，如果地址的訊息是在另一個 POJO 物件中，且作為表單對應物件內的屬性 (`address`)，此時可以**通過 `.` 來表示屬性的屬性**， spring-mvc 能識別並自動封裝。

```java
public class Address {
    private String postalCode;
    private String city;
    // 以下省略getter、setter、toString方法
}

public class Book {
    private String bookName;
    private String author;
    private Double price;
    private Integer stock;
    Address address;
    
    // 以下省略getter、setter、toString方法
}
```

```java
/**
 * Spring mvc 自動封裝請求參數為POJO對象
 */
@RequestMapping(value = "/books", method = RequestMethod.POST)
public String insertBook(Book book) {
    System.out.println("book: " + book);
    System.out.println("insertBook working");
    return "/success.jsp";
}
```

### 原生API支持

Servlet 中，都是直接透過 `HttpServletRequest` 和 `HttpServletResponse` 物件獲得的請求訊息、進行請求的響應操作。

spring-mvc 中提供對**部分**原生 API 的支持，只要將原生 API 對象定義為方法形參，spring-mvc 在呼叫控制器方法時會傳入。

spring-mvc 所支持的原生API：

+ HttpServletRequest
+ HttpServletResponse
+ HttpSession
+ java.security.Principal：https安全協議
+ Locale：國際化有關的區域訊息
+ InputStream：`ServletInputStream is = request.getInputStream();`
+ OutputStream：`ServletOutputStream os = response.getOutputStream();`
+ Reader：`BufferedReader reader = resquest.getReader();`
+ Writer：`PriterWriter writer = response.getWriter();`

```java
@RequestMapping("/test01")
public String test01(HttpServletRequest req, HttpSession session){
    req.setAttribute("username", "James");
    session.setAttribute("username", "Peter");
    return "/success.jsp";
}
```

### 亂碼問題解決

回顧在 Servlet 時所學到的解決亂碼的方式，亂碼可以分為兩大類：

+ 請求亂碼

  + `GET` 請求

    解決方式：透過 tomcat 的 `server.xml` 設定文件，在8080端口處設置 `URLENCODEING="UTF-8"`

    > 高版本 Tomcat 內置設定 URLENCODEING 為 UTF-8

  + `POST` 請求

    解決方式：`request.setCharacterEncoding("UTF-8")`

+ 響應亂碼

  解決方式：`response.setContentType("text/html;charset=UTF-8")` 或 `response.setCharacterEncoding("UTF-8")`

Servlet 中，會將處理編碼問題使用 Filter 統一做處理，spring-mvc 也提供編碼處理的 `CharacterEncodingFilter`。

`CharacterEncodingFilter` 重要屬性：

+ String encoding：編碼格式
+ boolean forceRequestEncoding：是否設置請求編碼格式，默認值為`false`。
+ boolean forceResponseEncoding：是否同時設置響應編碼格式，默認值為`false`。

```xml
<!-- 配置編碼處理Filter，需要注意配置在所有filter前面-->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<!-- 配置請求方式處理Filter -->
<filter>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>hiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 解決編碼的 Filter 必須配置到其他 Filter 最前面。

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

spring-mvc 響應數據的方式非常多，除了在原生API章節中直接操作原生API響應之外，下面將介紹另外幾種方式。

+ 頁面跳轉
  + 方法返回視圖名
  
    > 視圖名會與視圖解析器中的前後綴拼成一個完整的頁面路徑
+ 傳回數據
  
  + 控制器方法傳入Map、Model、Model對象

##### 頁面跳轉-方法返回視圖名

視圖名會與與視圖解析器 `ViewResolver` 的前後綴拼串成完整頁面路徑，spring-mvc 使用頁面路徑進行跳轉。亦可以指定跳轉的方式，在頁面路徑最前面加上 `foward:` 或 `redirect:` 字串進行轉發或重定向。

範例：控制器方法返回視圖名為 `index`。視圖名稱和配置前後綴拼接起來為 `/WEB-INF/index.jsp`。

```xml
<property name="prefix" value="/WEB-INF/jsp/" />
<property name="suffix value=".jsp" />
```

可以在 spring-mvc 配置視圖解析器 bean，預設的視圖解析器是 `org.springframework.web.servlet.view.InternalResourceViewResolver`，spring-mvc 各預設組件都可以從 `spring-webmvc` 依賴中的 `org.springframework.web.servlet` 包下的 <font color="ff0000">DispatcherServlet.properties</font> 文件找到

spring-mvc 默認跳轉方式為 `forward`。範例展示如何指定跳轉方式：

+ 轉發：`forward:/index.jsp`
+ 重定向：`redirect:/index.jsp`

#### 傳回數據

控制器方法中指定傳入`Map`、`Model`、`ModelMap` 參數，操作物件進行添加鍵值對或屬性方式，spring-mvc 會將添加的數據設置為 `request` 域的屬性。

```java
/**
 * 傳入Map方式
 */
@RequestMapping("/test01")
public String test01(Map<String,Object> resultMap){
    System.out.println("test01 is working");
    resultMap.put("username", "Jessy");
    return "/success.jsp";
}

/**
 * 傳入Model方式
 */
@RequestMapping("/test02")
public String test02(Model model){
    System.out.println("test02 is working");
    model.addAttribute("username", "Tommy");
    return "/success.jsp";
}

/**
 * 傳入 ModelMap 方式
 */
@RequestMapping("/test03")
public String test03(ModelMap modelMap){
    System.out.println("test03 is working");
    modelMap.addAttribute("username", "Brian");
    return "/success.jsp";
}
```

#### Map、Model、ModelMap的關係

查看三者的 class 可以發現 spring-mvc 都是傳入同一個 `BindingAwareModelMap` 對象。spring-mvc 只是將相同的物件以多態的方式賦值給不同的變數。

```java
/**
 * 同時傳入三個，其實都是傳入BindingAwareModelMap對象
 * 且3個都是使用同樣的對象
 */
@RequestMapping("/test04")
public String test04(Map<String, Object> map, Model model, ModelMap modelMap){
    //org.springframework.validation.support.BindingAwareModelMap
    System.out.println(map.getClass());
    //org.springframework.validation.support.BindingAwareModelMap
    System.out.println(model.getClass());
    //org.springframework.validation.support.BindingAwareModelMap
    System.out.println(modelMap.getClass());

    System.out.println(map == model); //true
    System.out.println(model == modelMap);//true
    return "/success.jsp";
}
```

三者之間的關係如下：(i)為interface、(c)為class，階層為繼承關係

+ Map(i)
  + LinkedHashMap(c)
    + ModelMap(c)
      + ExtendedModelMap(c) implement Model
        + BindingAwareModelMap(c)
+ Model(i)

#### 頁面跳轉及傳回數據 - 返回 ModelAndView 對象

`ModeAndView` 顧名思義，由Mode(模型)和View(視圖)組成。模型保存數據，視圖展示數據。

常用得方法如下：

+ setViewName(String name) 設置視圖名，與控制器方法直接回傳視圖名相同效果。
+ addObject(String attributeName, Object attributeValue)  設置`Model`中保存的數據為 `request` 域中屬性

##### 範例一：手動創建 ModelAndView 對象

```java
/**
 * 頁面跳轉 - 返回ModeAndView方式
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

##### 範例二：方法傳入ModelAndView 對象

```java
/**
 * 方法傳入ModelAndView 對象
 */
@RequestMapping("/update")
public ModelAndView doUpdate(ModelAndView modelAndView){
    // 設置Model數據
    modelAndView.addObject("username","Tommy");
    // 設置視圖名
    modelAndView.setViewName("/success.jsp");
    return modelAndView;
}
```

#### SESSION 域中帶入數據

spring-mvc 也提供放入 `session` 域中的方法，註解使用上沒有這麼的直覺。**<font color="ff0000">建議直接使用原生API方式</font>**。

+ `@SessionAttributes`

  + 作用：將欲存放在 `request` 域中的模型數據(BindingAwareModelMap、ModelAndView)也存放一份至 `session` 域中。

  + 使用位置：控制器類

  + 屬性：

    + value：指定`request`域中的key值。
    + type：限定屬性值的類型

  + 範例

    ```java
    @Controller
    @RequestMapping("/return")
    @SessionAttributes(value = "username")
    public class ReturnValueController {
        @RequestMapping("/test06")
        public String test06(Model model){
            model.addAttribute("username", "HelloWorld");
            return "/success.jsp";
        }
    }
    ```

    > @SessionAttribute 和 @SessionAttribute<font color="ffoooo">s</font> 是兩個不同的註解，請不要搞混

#### @ModelAttribute 

`@ModelAttribute` 註釋可以為模型添加數據。`@ModelAttribute` 通常是和 `@RequestMapping` 一起配合使用，`@ModelAttribute` 和 `@RequestMapping` 註釋位置的不同，其作用也有所差異。

在執行控制器類下的被 `@RequestMapping` 註解標註的方法前，spring-mvc 會依次執行被 `@ModelAttribute` 標註的方法。範例：當 login 請求到來時，會先呼叫 preProcess 方法，在呼叫 doLogin 方法。

```java
@RequestMapping("/login")
public String doLogin(){
    return "/success.jsp";
}

@ModelAttribute
public void preProcess(){
    System.out.println("Welcome ...")
}
```



`@ModelAttribute`註解標註的方法返回值會被存放在`Model`中，註解的 value 屬性設置方法返回值在 `Model` 的 key 值。沒有指定時以返回值類型名稱為 key (駝峰命名方式)。

範例：在執行 doLogin 方法前，執行 preProcess 方法，該方法讀取請求參數 username 和 password 值，並將其封裝為 User 物件，存放在 `Model `中，key 值為 `currentUser`。

```java
@RequestMapping("/login")
public String doLogin(){
    return "/success.jsp";
}

// 如果沒有指定 value，那該例子的 key 為 user
@ModelAttribute("currentUser")
public User preProcess(@RequestParam("username") username, @RequestParam("password") password){
    System.out.println("Welcome " + username);
    User user = new User(username, password);
    return user;
}
// 或是使用這種方式指定 key 值，效果與上面方法一致
@ModelAttribute
public void preProcess(@RequestParam("username") username, @RequestParam("password") password, Model model){
    System.out.println("Welcome " + username);
    User user = new User(username, password);
    model.addAttribute("current-user", user);
}
```

使用在方法形參上的 `@ModelAttribute` 註解的作用就是獲取 `Model` 中存放的數據。

1. 首先會決定到`Model`中取得數據的key值，如果`@ModelAttribute`有指定value值，key使用value，如果沒有指定value，則以參數的類型名稱作為 key(小寫開頭)。
2. 使用key值到`Model`(隱含模型)中獲取數據，如果有數據，則將該數據賦值給參數；如果沒有，則創建一個新的物件賦值給該參數。

```java
// 從Model中有 "currentUser"，所以重新創建User對象，而是使用 "currentUser" 進行請求參數的封裝
@RequestMapping("/login")
public String doLogin(@ModelAttrubute("currentUser") User user){
    System.out.printf("welcome %s", user.getUsername());
    return "/success.jsp";
}

// 如果沒有指定 value，那該例子的 key 為 user
@ModelAttribute("currentUser")
public User preProcess(@RequestParam("username") username, @RequestParam("password") password){
    User user = new User(username, password);
    return user;
}
```

如果 `Model` 中不存在指定 key 值的物件，那麼 spring-mvc 會使用原本的方式重新創建物件，並封裝。只是最後它還會將該物件放入到 `Model ` 中，key 值為 value 值。

```java
// Model中沒有"currentUser"，創建User並使用它來封裝請求參數，最後以 "currentUser"字串為key，將該物件保存在Model中
@RequestMapping("/login")
public String doLogin(@ModelAttrubute("currentUser") User user){
    System.out.printf("welcome %s", user.getUsername());
    return "/success.jsp";
}

//然後可以在JSP頁面取值
<h1>Hello ${currentUser.name}<h1>
```

`@RequestMapping` 和 `@ModelAttribute` 同時註釋同一個方法。

此時該方法的返回值不在表示視圖名，取而代之的是 `@RequestMapping` 的value 成為視圖名。方法的返回值則變成存放在 `Model` 中的數據，`@ModelAttribute` 的 value 值則為該數據的 key。

```java
// 返回的視圖名稱是 login
@RequestMapping("/login")
@ModelAttrubute("currenUser")
public User doLogin(@RequestParam("username") username, @RequestParam("password") password){
    User user = new User(username, password);
    return user;
}
```

`@RequestMapping` 所修飾的方法返回值上使用 `@ModelAttribute `修飾，此時方法返回值不再表示視圖名，取而代之的是 `@RequestMapping` 的value 成為視圖名。方法的返回值則變成存放在 `Model` 中的數據，`@ModelAttribute` 的 value 值則為該數據的 key。

```java
// 返回的視圖名稱是 login
@RequestMapping("/login")
public @ModelAttrubute("currenUser")User doLogin(@RequestParam("username") username, @RequestParam("password") password){
    User user = new User(username, password);
    return user;
}
```

總結：`@ModelAttribute` 適合為其它 `@RequestMapping` 修飾的方法在 `request` 域中設置共用的數據，這樣就不必在每個控制器方法中重複書寫在`Model`設置共用數據的操作。

### 源碼分析

#### doDispatcher()方法處理流程

1. getHandler()：根據當前請求地址找到能處理這個請求的目標處理器類(處理器)
2. getHandlerAdapter()：根據當前處理器類獲取到能執行這個處理器方法的適配器。
3. 使用上步驟獲取的適配器(AnnotationMethodHandlerAdapter)執行目標方法
4. 目標方法執行後會返回一個ModeAndView對象
5. 根據ModeAndView的信息轉發到具體的頁面，並可以在域中取出ModelAndView中的模型數據

#### spring-mvc 9大組件

DispatcherServlet 中有幾個引用類型的的屬性，spring-mvc 在工作的時候，關鍵位置都是由這些組建完成的。

**共通點：9大組件都是接口，制定規範。**

+ MultipartResolver：文件上傳解析器
+ LocaleResolver：區域訊息解析器和國際化有關
+ ThemeResolver：主題解析器，強大的主題效果更換
+ HandlerMapping：映射器
+ HandlerAdapter：適配器
+ HandlerExceptionResolver：異常解析器
+ RequestToViewNameTranslator：當方法沒有返回值時，將請求URL轉換為視圖名
+ FlashMapManager：spring-mvc中允許redirect攜帶數據的功能
+ ViewResolver：視圖解析器

9大組件初始化方法： initStrategies

組件的初始化：去IOC容器中找這個組件，如果沒有就使用默認配置。**有些組件是使用類型找的，有些則是使用id去找得。**

#### 如何確定目標方法每一個參數的值

思路：

1. 創建**隱含模型**(`BindingAwareModelMap`)，用於最後將裡面的數據時放到 `request` 域中

2. 找到所有`@ModelAttribute`註解標註的方法，遍歷解析

   1. 確定`@ModelAttribute`標註標註方法的參數值

      1. 取得目標方法每一個參數的類型

      2. 創建一個與目標方法參數相同長度的數組 `args`，用來保存每一個參數的值

      3. 遍歷 `arg` 數組

         1. 解析參數，獲得參數信息

         2. 取得目標方法參數上標註的註解

         3. <font color="ff0000">如果參數有註解</font>，解析註解的信息並針對不同註解進行賦值

            1. `@ModelAttribute`和`@SessionAttribute`，是兩個特例，它們只是將value屬性值賦值`attrName`，並沒有在這進行賦值。
            2. 有一些註解也只會解析，不會在這馬上進行賦值，和上方的`@ModeAttribute`和`@SessionAttribute`差不多。

         4. <font color="ff0000">如果參數沒有註解</font>

            1. `resolveCommonArgument`方法確認是否為普通參數，參數進行賦值。(普通參數就是指Servlet原生API)
            2. 判斷參數是否以解析，如果已解析就為參數進行賦值
            3. 判斷是否有默認值，如果有就為參數進行賦值
            4. 判斷是參數類型是否為`Model`或`Map`類型或旗下類型，如果是將**隱含模型**引用賦值給參數
            5. 判斷是否相容其它類型，是的話參數進行複值
            6. 判斷是否為簡單數據類型，如: Integer、String等，是的話賦值`paramName`變數為「""」。
            7. 都不符合上述判斷，賦值`attrName`為「""」。

            > 自定義類型，可以分為兩種：標註解(`@ModelAttribue`或`@SessionAttribute`)、沒標註解。
            >
            > 有註解：attrName為註解的value值。
            >
            > 沒註解：attrName為「""」。

         5. 對那些還沒進行複值的進行複值，分類為有註解的參數和梅註解的參數都有，這裡只針某幾種對重點介紹。

            1. `paramName != null`的簡單類型，進行request參數值與參數值的綁定(相同名稱)。
            2. `attrName != null`<font color="ff0000">(`@SessionAttribute`、`@ModuleAttribute`、沒註解的POJO都歸屬於這邊) </font>
               1. 解析ModelAttribute(只是方法名稱翻譯)，並不單單處理與`@ModelAttribute`相關，以業務邏輯來看的話，是解析 attrName
               2. 如果 attrName 為空，使用類型名稱作為key值(小寫)
               3. 判斷`Model`(隱含模型)中是否有與key值對應的數據，如果有參數綁定該數據
               4. else判斷 session scope 中是否有與 key值相對應的數據，如果有參數綁定該數據
               5. 如果在 `Model`和 `Session`中都沒有，使用反射創建一個新的自定義類物件。
            3. 使用數據綁定將請求參數中的值賦值給物件屬性。

   2. 取得`@ModelAttribute`的 value 值，如果沒有設定，則已方法返回值作為其值

   3. 執行`@ModelAttribute`註解標註的方法

   4. 隱含模型保存`@ModelAttribute`方法返回值，key值就是上步驟解析出的 value 值。

3. 找到目標方法，解析並執行(解析步驟與上面解析`@ModleAttribute`一樣)

