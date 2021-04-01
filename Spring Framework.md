## Spring Framework

### 配置文件

#### bean 標籤

##### 基本配置

用於配置對象交由 Spring 創建，默認情況下調用類中的**無參構造器**，如果沒有無參構造器則不能創建成功。

+ 基本屬性：
  + id：Bean 實例在 Spring 容器的唯一標識。(不能重複)
  + class：Bean 的完整類名。

##### 範圍配置(scope)

scope：指定對象作用的範圍。request、session、globalsession 需要在外部環境下配置。

| 取值範圍       | 說明                                                         |
| -------------- | ------------------------------------------------------------ |
| **singleton**  | **默認值，單例。**                                           |
| **prototype**  | **多例的。**                                                 |
| request        | WEB項目中，每次 HTTP 請求，Spring 創建一個 Bean 對象，將該對象存入到 request 域中。<br />該對象在這個 HTTP 請求結束後被銷毀。 |
| session        | WEB項目中，每個 HTTP SESSION，Spring 創建一個 Bean 對象，將該對象存入到 session 域中。<br />該對象在這個HTTP SESSION 結束後被銷毀。 |
| global session | WEB項目中，僅僅在基於 portlet 的 web 應用中才有意義。<br />Portlet 規範定義了全域性 Session 的概念，它被所有構成某個 portlet web 應用的各種不同的portlet 所共享。在 global session 作用域中定義的 bean 被限定於全域性 portlet Session的生命週期範圍內。如果沒有 Portlet 環境，那麼 globalsession 相當於 session |

scope 和 prototype 區別：

|          | scope                              | prototype                                |
| -------- | ---------------------------------- | ---------------------------------------- |
| 對象個數 | 1個                                | 多個                                     |
| 對象創建 | 應用加載，創建容器時，對象就被創建 | 程序獲取對象時，創建對象                 |
| 對象運行 | 只要容器還存在，對象就存在         | 對象還有被引用(使用)，對象就存在         |
| 對象銷毀 | 應用關閉，銷毀容器時，對象被銷毀   | 對象沒有被引用一段時間後，被Java的gc回收 |

##### 生命週期方法配置

`init-method` 指定類中初始化方法名稱，當對象被創建時呼叫。

`destroy-method` 指定類中銷毀方法名稱，當對象被銷毀時呼叫。

```xml
<bean id="userDao" class="org.learning.dao.impl.UserDaoImpl" scope="singleton" 
	init-method="init" destroy-method="destroy">
</bean>
```

##### bean 實例化的三種方式

1. 無參構造方法實例化(默認)

2. 工廠靜態方法實例化

   `bean` 標籤的 `class` 屬性需指向靜態工廠，且增加 `factory-method` 指定使用靜態工廠中的靜態方法。

   ```xml
   <!-- 工廠靜態方法配置 -->
   <bean id="userDao" class="org.learning.factory.UserDaoFactory" 
   	factory-method="getInstance">
   </bean>
   ```

3. 工廠實例方法實例化

   需要先配置工廠類的`bean`，然後在`factory-bean`屬性指定該工廠`bean`，`factory-method`指定工廠`bean`的方法。

   ```xml
   <!-- 工廠實例方法配置 -->
   <bean id="factory" class="org.learning.factory.UserDaoDynamicFactory"></bean>
   <bean id="userDao" factory-bean="factory" factory-method="getInstance"></bean>
   ```

   

