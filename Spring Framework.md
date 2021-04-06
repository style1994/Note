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

#### 依賴注入

依賴注入 ( Dependency Injection ) 是`Spring`框架核心`IOC(控制反轉)`的具體實現。編寫程序時，通過`IOC`，把對象創建交給`Spring`，但是代碼不可能沒有依賴，`IOC`解偶只是降低它們的依賴關係，但不會完全消除。例如：`Service`層仍會調用`Dao`層的方法。

這種業務層與持久層的依賴關係，在使用`Spring`之後，就讓`Spring`來替我們維護。`Spring`框架會將持久層對象傳入至業務層，而不是我們自己去獲取。

##### Bean 的注入方式

###### 構造方法

`bean` 標籤內可以宣告 `constructor-arg`子標籤，用於指定在呼叫構造方法時，傳入的參數。當沒有指定`constructor-arg`子標籤，默認呼叫無參構造方法創建`bean`對象。`constructor-arg`子標籤有以下屬性：

1. `name`：指定構造方法的參數名
2. `ref`：指定容器中的`bean`對象，將該對象作為構造方法的參數，值為`bean id`。
3. `value`：指定一個值，將該值作為構造方法的參數。

```xml
<!-- 構造方法注入 -->
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
<constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

```java
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public UserServiceImpl() {
    }

    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        System.out.println("service call");
        userDao.save();
    }
}
```

###### Set方法

`bean` 標籤內可以宣告`property`子標籤，`property`代表`bean`類的成員變量，`property`標籤有以下屬性：

1. `name`：指定執行`bean`哪個set方法，值為set方法名稱去掉前面set後剩下的文字，需寫成駝峰命名。**注意並不是`class`的屬性名稱**。
2. `ref`：容器中的`bean`對象，將該對象作為set方法的參數，值為`bean id`。
3. `value`：指定一個值，將該值作為set方法的參數。

> `ref`和`value`兩者只能選一個使用

範例：

```xml
<bean id="userDao" class="org.learning.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```

```java
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        System.out.println("service call");
        userDao.save();
    }
}
```

set方法注入還有另一種寫法：p命名空間注入。它比起上面set方法注入更加方便，使用p命名空間必須先在配置文件中引入p命名空間。

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
<!-- 使用p命名空間方式注入依賴 -->
<bean id="userService" class="org.learning.service.impl.UserServiceImpl" 
      p:userDao-ref="userDao"/>
```

> 在實際開發中還是推薦set方法注入，當需要注入的對象很多時，p命名空間注入的書寫方式會過於冗長，閱讀不便。

##### Bean的依賴注入的數據類型

除了引用`bean`對象注入之外，普通數據類型、集合等都可以在容器中進行注入。

注入數據的三種類型：

1. 普通數據類型 ( `value` )

   通過 `value` 屬性指定普通數據類型值。

2. 引用數據類型 ( `ref` )

   通過 `ref` 引入 Spring 容器中其他`bean`對象，將各種不同的`bean`互相組合。

3. 集合數據類型

   1. List

      `list` 標籤為 `property` 下的子標籤，通過 `list` 標籤為`bean`中數據類型為 `list ` 的成員變量賦值。

   2. Map

      `map`標籤為`property`下的子標籤，通過 `map` 標籤為`bean`中數據類型為 `map ` 的成員變量賦值。

      `map` 下的每個元素都以 `entry` 標籤表示，`key`和`key-ref`用來表示鍵，`value`和`value-ref`用來表示值，帶有 `ref` 的標籤屬性用來引用 Spring 容器中的 `bean`。

   3. Properties

      `props`標籤為`property`下的子標籤，通過 `props` 標籤為`bean`中數據類型為 `properties ` 的成員變量賦值。

      `props`下可以定義多個`prop`標籤，`key`屬性表示鍵，`prop`標籤體中的文字表示值。

   4. Array

      `array`標籤為`property`下的子標籤，通過 `array` 標籤為`bean`中數據類型為數組的成員變量賦值。

   5. Set

      `set`標籤為`property`下的子標籤，通過 `set` 標籤為`bean`中數據類型為 `set ` 的成員變量賦值。

   > 集合中元素如果想包含普通數據類型使用 `value `標籤表示，如果想包含容器中`bean`對象使用 `ref`，還可以內嵌其他標籤，因為集合中可以包含各種類型的數據。

要示範三種類型的不同注入方式，首先準備一個`bean`包含不同需注入數據類型。

```java
public class User {
    private String name;
    private int age;
    private Double salary;
    private List<String> family;
    private Map<String, Integer> grade;
    private Properties props;
    private Set<String> subject;
    private String[] remark;
    private List<Hobby> hobbies;
    // 以下省略 get set 方法
}

public class Hobby {
    private String describe;
    // 以下省略 get set 方法
}
```

```xml
<bean id="user" class="org.learning.bean.User">
    <!-- 普通數據類型 -->
    <property name="name" value="James"></property>
    <property name="age" value="26"></property>
    <property name="salary" value="99999"></property>
    <!-- list -->
    <property name="family">
        <list>
            <value>Amy</value>
            <value>Peter</value>

        </list>
    </property>
    <!-- map -->
    <property name="grade">
        <map>
            <entry key="math" value="100" ></entry>
            <entry key="chinese" value="100"></entry>
            <entry key="english" value="50"></entry>
        </map>
    </property>
    <!-- properties -->
    <property name="props">
        <props>
            <prop key="height">180cm</prop>
        </props>
    </property>

    <!-- array -->
    <property name="remark">
        <array>
            <value>test1</value>
            <value>test2</value>
            <value>test3</value>
        </array>
    </property>

    <!-- set -->
    <property name="subject">
        <set>
            <value>set value1</value>
            <value>set value2</value>
            <value>set value3</value>
        </set>
    </property>

    <!-- 集合中包含其他bean對象 -->
    <property name="hobbies">
        <list>
            <!-- 直接在集合內部定義bean -->
            <bean class="org.learning.bean.Hobby">
                <property name="describe" value="swim"></property>
            </bean>
            <bean class="org.learning.bean.Hobby">
                <property name="describe" value="reading"></property>
            </bean>
            // 引用外部bean
            <ref bean="hobby1"></ref>
        </list>
    </property>
</bean>

<!-- 外部的bean -->
<bean id="hobby1" class="org.learning.bean.Hobby">
    <property name="describe" value="play ball"></property>
</bean>
```

#### 引入其他配置文件

實際開發中，Spring 的配置內容非常多，這就導致 Spring 配置很繁雜且體積很大，因此，Spring 提供引入其他配置文件的功能。這樣可以將不同模塊的配置文件分開，在集成到一個統整配置文件中。

`import` 標籤可以加載其他配置文件，`resource`標籤屬性指定配置文件名。

> 如果引入的其他配置文件與自己本身發生衝突，會按照先後順序覆蓋

```xml
<!-- 引入其他配置文件 -->
<import resource="applicationContext-product.xml"></import>
<import resource="applicationContext-user.xml"></import>
```

#### Spring 重點配置

+ `bean`： `Spring` 容器中的對象都被稱為 `bean` 對象
  + `id屬性`：`bean` 對象的唯一標識，不可重複。
  + `class屬性`：`bean` 對象的完整類名。
  + `scope屬性`：設定 `bean` 的作用範圍，常用的是`singleton`(默認)和`prototype`
  + `property標籤`：用於屬性注入
    + `name屬性`：屬性名稱
    + `value屬性`：指定注入的普通屬性值
    + `ref屬性`：指定注入Spring容器中的`bean id`值
    + `list標籤`
    + `set標籤`
    + `map標籤`
    + `array標籤`
    + `properties標籤`
  + `constructor-arg標籤` 指定創建`bean`對象時調用構造方法的參數
+ `import標籤`：導入其它配置文件

### Spring 相關 API

#### ApplicationContext 實現類

1. ClassPathXmlApplicationContext

   從類的根路徑下加載配置文件推薦使用這種

2. FileSystemXmlApplicationContext

   從硬碟加載配置文件，配置文件可以在硬碟任意位置

3. AnnotationConfigApplicationContext

   當使用註解配置容器對象時，需要使用此類來創建Spring容器，它用來讀取註解。

#### getBean 方法

該方法有兩種常用多載：

+ public Object getBean(String name) throws BeanException

  傳入配置文件定義的`bean id`

+ public <T> T getBean(class<T> requireType) throws BeanException

  傳入所需 `bean` 的類型，當配置中出現兩個 `bean` 都符合，拋出錯誤。

> 傳入`bean id`取得`bean`的好處是可以擁有多個相同類型的`bean`，傳入類型取得`bean`的好處是不用手動轉型，但是配置中只能有一個相同類型的`bean`

### Spring 配置數據源

#### 數據源(連接池)的作用

數據庫連接池提高數據庫訪問性能。

連接池使用步驟：

1. 事先創建連接池，初始化部分連接。
2. 從連接池中獲取連接使用。
3. 使用完畢後將連接歸還給連接池。

常見的數據庫連接池有：DBCP、C3P0、Druid等

#### 配置文件讀取外部properties文件

數據庫的連接訊息通常會定義在單獨的properties文件中，下面介紹如何在Spring配置文件中讀其他properties文件的訊息。

1. 首先，需要引入context命名空間和約束路徑
   1. 命名空間：xmlns:context="http://www.springframework.org/schema/context"
   2. 約束路徑：
      1. http://www.springframework.org/schema/context
      2. http://www.springframework.org/schema/context/spring-context.xsd 

2. 通過`context:property-placeholder`標籤引入外部 properties 文件

3. 使用SPEL取得properties文件值(${key}格式)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
        <!-- 加載外部 properties 文件 -->
    <context:property-placeholder location="jdbc.properties" />

    <!-- 使用SPEL取得properties文件值(${key}格式) -->
    <bean id="jdbcUtils" class="org.learning.util.JdbcUtils">
        <property name="user" value="${user}" />
        <property name="password" value="${password}" />
        <property name="driver" value="${driver}" />
        <property name="url" value="${url}" />
    </bean>
</beans>
```

### Spring 註解開發

`Spring` 是輕代碼種配置的框架，配置是非常繁重的，嚴重影響開發效率。所以註解開發是一種趨勢，註解替代xml配置文件，可以簡化配置、提高開發效率。

**`Spring` 框架註解區分為兩種，原始註解與新註解。原始註解出現的比較早，新註解出現的比較晚。**

#### Spring 原始註解

`Spring` 原始註解主要是替代`bean`標籤配置

| 註解           | 說明                                                     |
| -------------- | -------------------------------------------------------- |
| @Component     | 使用在類上用於實例化Bean                                 |
| @Controller    | 使用在Controller層類上用於實例化Bean                     |
| @Service       | 使用在Service層類上用於實例化Bean                        |
| @Repository    | 使用在Dao層上用於實例化Bean                              |
| @Autowired     | 使用在成員變量上，根據類型進行依賴注入                   |
| @Qualifier     | 結合@Autowired一起使用，用於根據名稱進行依賴注入         |
| @Resource      | 等於@Autowired + @Qualifier，根據名稱進行注入            |
| @Value         | 使用在成員變量上，注入普通屬性                           |
| @Scope         | 標註Bean的作用範圍                                       |
| @PostConstruct | 使用在方法上，指定Bean的初始化方法，構造方法執行完後調用 |
| @PreDestroy    | 使用在方法上，指定Bean的銷毀方法，在Bean被銷毀前調用     |

