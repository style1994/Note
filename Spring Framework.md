## Spring Framework

### 配置文件

#### bean 標籤

##### 基本配置

用於配置對象交由 Spring 創建，默認情況下調用類中的**無參構造器**，如果沒有無參構造器則不能創建成功。

- 基本屬性：
  - id：Bean 實例在 Spring 容器的唯一標識。(不能重複)
  - name：Bean 實例在 Spring 容器的別名，別名可以多個(使用逗號分開)
  - class：Bean 的完整類名。

##### 範圍配置(scope)

scope：指定對象作用的範圍。request、session、globalsession 需要在外部環境下配置。

| 取值範圍       | 說明                                                                                                                                                                                                                                                                                                               |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **singleton**  | **默認值，單例。**                                                                                                                                                                                                                                                                                                 |
| **prototype**  | **多例的。**                                                                                                                                                                                                                                                                                                       |
| request        | WEB 項目中，每次 HTTP 請求，Spring 創建一個 Bean 對象，將該對象存入到 request 域中。<br />該對象在這個 HTTP 請求結束後被銷毀。                                                                                                                                                                                     |
| session        | WEB 項目中，每個 HTTP SESSION，Spring 創建一個 Bean 對象，將該對象存入到 session 域中。<br />該對象在這個 HTTP SESSION 結束後被銷毀。                                                                                                                                                                              |
| global session | WEB 項目中，僅僅在基於 portlet 的 web 應用中才有意義。<br />Portlet 規範定義了全域性 Session 的概念，它被所有構成某個 portlet web 應用的各種不同的 portlet 所共享。在 global session 作用域中定義的 bean 被限定於全域性 portlet Session 的生命週期範圍內。如果沒有 Portlet 環境，那麼 globalsession 相當於 session |

scope 和 prototype 區別：

|          | scope                              | prototype                                    |
| -------- | ---------------------------------- | -------------------------------------------- |
| 對象個數 | 1 個                               | 多個                                         |
| 對象創建 | 應用加載，創建容器時，對象就被創建 | 程序獲取對象時，創建對象                     |
| 對象運行 | 容器未被關閉，對象就存在           | 對象還有被引用(使用)，對象就存在             |
| 對象銷毀 | 應用關閉，銷毀容器時，對象被銷毀   | 對象沒有被引用一段時間後，被 Java 的 gc 回收 |

##### 生命週期方法配置

`init-method` 指定類中初始化方法名稱，當對象被創建後(配置完依賴後)時呼叫。

`destroy-method` 指定類中銷毀方法名稱，當對象被銷毀前呼叫。

```xml
<bean id="userDao" class="org.learning.dao.impl.UserDaoImpl" scope="singleton"
	init-method="init" destroy-method="destroy">
</bean>
```

##### bean 實例化的三種方式

1. 無參構造方法實例化(默認)

   ```xml
   <!-- 參構造器配置 -->
   <bean id="userDao" class="org.learning.dao.UserDao"/>
   ```

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

IOC (Inversion of Control) 控制反轉是一種思想，而 DI 是實現該思想的技術之一。

控制反轉，指的是將本來由程式本身自己創建所依賴對象的這種自主行為，轉變為被動從外部接受所依賴對象，自己不參與依賴對象的創建。有助於改善程式之間的耦合關係。

依賴注入 (Dependency Injection) 是 `Spring` 框架核心 `IOC(控制反轉)` 的具體實現。IOC 思想，是把對象創建權交由 Spring 框架。

代碼不可能沒有依賴，解偶只是降低它們的依賴關係，但不會完全消除。例如：`Service` 層仍會調用 `Dao` 層的方法來訪問數據庫。

這種業務層與持久層的依賴關係，在使用 `Spring` 之後，轉變由 `Spring` 來替我們維護。`Spring`框架會為業務層注入所需的持久層，而不是持久層自己去獲取。

##### Bean 的注入方式

Spring 提供依賴注入的方式為兩種

- 構造器注入：強調是一種**必要**依賴的關係，好處是可以將所依賴的對象設為 final。Spring 官方推薦大部分情況下請使用構造器注入依賴。
- setter 注入：強調依賴是**可選**的，應該僅用於對有默認值的依賴進行配置。否則，要在使用依賴前判斷其是否為 null。

###### 構造器注入

`bean` 標籤內定義 `constructor-arg` 子標籤，用配置 Spring 在創建 Bean 時所呼叫的無參構造器，所傳入的參數。當沒有指定`constructor-arg`子標籤，則呼叫無參構造方法創建 Bean 對象。

`constructor-arg`子標籤有以下屬性：

1. `name`：指定構造方法中的參數名稱。

2. `ref`：指定容器中其他 Bean 對象，將其作為構造方法的參數，值為 `bean id`。

   > 定義 Bean 對象的書寫順序並不等於創建的順序，Spring 框架會自動判斷哪些 Bean 要先創建，那些會晚創建。正常情況下，所依賴的 Bean 會先創建。

3. `value`：指定一個值，將該值作為構造方法的參數。(ref 和 value 擇一)

4. `type`：指定構造函數參數的類型

5. `index`：指定構造函數參數的索引。(0 開始)

假設參數間沒有繼承關係，且在沒有歧意的情況下，`constructor-arg` 標籤僅需指定 ref 屬性。並不需要指定構造參數的類型或索引。

```xml
<!-- 構造方法注入 -->
<bean id="userDao" class="org.learning.dao.UserDao"/>
<bean id="memberDao" class="org.learning.dao.MemberDao">
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
	<constructor-arg ref="userDao"/>
    <constructor-arg ref="memberDao"/>
</bean>
```

當使用簡單類型時，Spring 無法確定值的類型，因此在沒有幫助下無法案類型來自動匹配。 `constructor-arg` 標籤 type 屬性來確定 value 屬性是屬於哪種基本類型。例如：Spring 無法透過字串 1 判斷應該是 int 還是 long 又或者是 String。

```xml
<!-- 構造方法注入 -->
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
	<constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

當構造函數有兩個相同類型的的參數，Spring 無法判斷時，使用 `constructor-arg` 標籤 index 屬性來正確匹配注入的依賴。這還可以解決多個基本類型的歧意，

```xml
<!-- 構造方法注入 -->
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
	<constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

還可以使用構造函數的參數名稱來消除歧意。

```xml
<!-- 構造方法注入 -->
<bean id="userDao" class="org.learning.dao.UserDao"/>
<bean id="userService" class="org.learning.service.impl.UserServiceImpl">
	<constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

```java
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void save() {
        System.out.println("service call");
        userDao.save();
    }
}
```

要啟用該功能，必須在啟用調試標誌的情況下編譯代碼。以便 Spring 可以從構造函數中查找參數名稱。如果你不能或不想使用 debug 標誌編譯代碼，可以使用 @ConstructorProperties JDK 註解，顯示命名構造函數參數。如下範例

```java
package examples;

public class ExampleBean {

    // Fields omitted

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

###### Setter 方法注入

基於 setter 的依賴注入是通過調用無參構造器或無參靜態工廠方法創建 Bean 之後，在調用 Bean 的 setter 方法來完成。

你可以混合使用基於構造器和基於 setter 的 DI。**構造器注入用於強制性依賴**；**setter 注入用於可選依賴**。這是一個非常好的經驗法則。

> 在 setter 方法上使用 @Require 註解，可讓該屬性 setter 注入變為必須的依賴

Spring 團隊推薦使用構造函數注入。順便說明一下，大量的構造函數變量是一種不好的代碼味道，這意味著該類可能承擔了太多職責，應該對其進行重構以更好解決分離關注點。

Setter 注入應僅用於**可以在類中分配合理默認值的可選依賴項**。否則，必須在代碼使用依賴項的任何地方進行非空檢查。

`bean` 標籤內可以宣告`property`子標籤，`property`代表`bean`類的成員變量，`property`標籤有以下屬性：

1. `name`：指定執行`bean`哪個 set 方法，值為 set 方法名稱去掉前面 set 後剩下的文字，需寫成駝峰命名。**注意並不是`class`的屬性名稱**。
2. `ref`：容器中的`bean`對象，將該對象作為 set 方法的參數，值為`bean id`。
3. `value`：指定一個值，將該值作為 set 方法的參數。

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

set 方法注入還有另一種寫法：p 命名空間注入。它比起上面 set 方法注入更加方便，使用 p 命名空間必須先在配置文件中引入 p 命名空間。

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
<!-- 使用p命名空間方式注入依賴 -->
<bean id="userService" class="org.learning.service.impl.UserServiceImpl"
      p:userDao-ref="userDao"/>
```

> 在實際開發中還是推薦 set 方法注入，當需要注入的對象很多時，p 命名空間注入的書寫方式會過於冗長，閱讀不便。

##### 循環依賴

**如果主要使用構造器注入，則可能會遇到無法解決的循環依賴**。

例如：A 類通過構造函數注入 B 類，而 B 類通過構造函數注入 A 類。以上述情況配置 Bean，Spring IOC 容器會在運行時檢測到此循環依賴錯誤，並拋出 BeanCurrentlyInCreateionException。

一種可能的解決方案是編輯某些類的源代碼，將循環依賴注入改為基於 setter。換句話說，儘管不建議這樣做，但是你可以使用 setter 注入循環依賴關係。

A 類和 B 類之間的循環依賴關係迫使其中一個 Bean 在完全初始化之前被注入另一個 Bean(雞和蛋)。

通常你可以信任 Spring 做正確的事。它容器加載時檢測配置問題，例如對不存在的 Bean 的引用和循環依賴。在實際創建 Bean 時，Spring 設置屬性並盡可能晚的解決依賴關係。這意味著如果創建該對象或其依賴項之一時出現問題，則正確加載 Spring 容器以後會在你請求對象時生成異常。

這種淺在地延遲可見性是為什麼默認情況下，ApplicationContext 會創建單例 Bean，在容器啟動時，它們就已經被創建，你會在啟動容器時就發現配置問題，而不是之後。你仍然可以修改此默認行為，以便單例 Bean 延遲初始化，而不是預先實例化。

##### Bean 的依賴注入的數據類型

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
      >
      > Map 的鍵或值可以是以下任意屬性：
      >
      > bean | ref | idref | list | set | map | props | value | null

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

##### 複合屬姓名稱

設置 Bean 屬性時，可以使用複合屬性或嵌套屬性名稱，只要路徑中除最終屬性名稱以外的所有組件不是 null 即可。參考以下範例

```xml
<bean id="something" class="things.ThingOne">
    <property name="fred.bob.sammy" value="123" />
</bean>
```

something Bean 具有 fred 屬性，該屬性具有 bob 屬性，該屬性具有 sammy 屬性，最終設置 sammy 值為 123。為了使上面的配置起作用，在構造 something 之後，fred、bob 一定不能為 null。否則將拋出 NullPointerException

##### 懶初始化 Bean

默認情況下，作為初始化過程的一部分，ApplicationContext 實現會急於創建和配置所有 Singleton Bean。通常，這種預初始化是可取的，因為與數小時甚至數天後相比，會立刻發現配置中的錯誤。如果不希望使用此行為，則可以通過將 Bean 定義標記為延遲初始化來防止單例 Bean 的預初始化。

延遲初始化的 Bean 告訴 IOC 容器在首次請求時創建，而不是在容器啟動時。

```xml
<bean id="lazy" class="com.something.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.something.AnotherBean"/>
```

但是，當延遲初始化 Bean 是未延遲初始化的單例 Bean 的依賴項時，ApplicationContext 將在啟動時創建延遲初始化 Bean，因為它必須滿足單例 Bean 的依賴關係。

你還可以使用 `<bean/>` 元素上的 default-lazy-init 屬性在容器級別控制延遲初始化，參考以下範例

```xml
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```

##### 查找方法注入

在大多數應用場景中，容器大多數的 Bean 是單例。當單例 Bean 需要與另一個單例 Bean 協作或非單例 Bean 需要另一個非單例 Bean 協作時，通常可以通過將一個 Bean 定義為另一個 Bean 的屬性來處理依賴性。

當 Bean 的生命週期不同時會出現問題。假設單例 Bean A 需要使用非單例 Bean B，也許在 A 的每個方法調用上都使用。容器僅創建一次單例 Bean A，因此只有一次機會來設置依賴。但是無法在 A 每次需要 B 時，為 A 提供一個新的 B。

一個比較粗魯的方法是放棄某些控制反轉。你可以通過讓 Bean A 實現 `ApplicationContextAware` 接口，來讓 A 知道容器的存在，並在每次需要 B 時，調用容器取得新的 B 實例。參考以下範例：

```java
public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}

```

前面的內容是不理想的，因為業務代碼知道並耦合到 Spring 框架。下面將介紹第二種解決方式：查找方法注入，它是 Spring IoC 容器的一項高級功能，讓你可乾淨俐落的解決上述問題

查找方法注入是容器重寫容器所管理的 Bean 中方法，使該方法返回容器中另一個指定名稱的 Bean。

Spring 框架通過 CGLIB 庫生成字節碼來動態生成覆蓋該方法的子類來實現該查找方法注入功能。

CGLIB 是通過繼承方式來實現動態代理功能，所以需要確保以下事項

- 需要使用查找方法的類不可以為 final
- 要覆蓋的方法也不可以為 final

對具有 abstract 方法的類進行單元測試需要您自己對該類繼承並提供抽象方法的實現，組件掃描也需要具體的方法，這需要具體的類別。

另一個關鍵限制，查找方法不適用於工廠方法，尤其不適用配置類中的 @Bean 方法，因為在這種情況下，容器不負責創建實例，因此在無法創建運行時生成的子類。

對於前面代碼中的 CommandManager 類，Spring 容器將動態覆蓋 createCommand() 方法的實現。CommandManager 類中沒有任何 Spring 依賴項。

```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
    protected abstract Command createCommand();
}

```

在包含要注入查找方法的類中，要注入方法需要以下形式的簽名：

```text
<public|protected> [abstract] <return-type> theMethodName(no-arguments);
```

如果方法是 abstract，則動態生成的子類將實現該方法。否則，動態生成的子類將覆蓋原始類中定義的具體方法。查找方法注入參考以下配置

```xml
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>

<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>

```

每當需要 myCommand 的新實例時，標示為 commandManager 的 Bean 就會調用自己的 createCommand() 方法。如果確實需要 myCommand Bean 作為原型，則必須小心。如果它是 Singleton，則每次相同實例。

另外，在基於註解的配置方式中，你可以通過 @Lookup 註解聲明一個查找方法

```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}

```

更常用的是，你可以依賴餘查找方法聲明的返回值類型來解析，不直接指定目標 Bean 的名稱

```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        MyCommand command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup
    protected abstract MyCommand createCommand();
}

```

請注意，註解方式宣告查找方法應該有具體的實現，以使它們與 Spring 的組件掃描規則相容，在默認情況下抽象類將被忽略，該限制不適用於顯示註冊或顯示導入的類

#### Bean 作用域

創建 Bean 時，你不僅可以控制要插入 bean 的依賴，還可以控制所創建 Bean 的範圍。Spring 框架支持六個範圍，其中幾個只有在具有網路感知 ApplicationContext 時才有用。你也可以自訂範圍

| Scope       | 描述                                                                                                                                                |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| singleton   | 默認值。將每個 Spring IOC 容器中的 Bean 限定為單例。                                                                                                |
| prototype   | Spring IOC 容器中允許任意數量的實例                                                                                                                 |
| request     | Bean 存在於單個 HTTP 請求的生命周期中，也就是說對於每次 HTTP 請求，容器都會創建一個新的 Bean 實例。僅在可感知網路的 ApplicationContext 上下文中有效 |
| session     | Bean 存在於單個 HTTP Session 的生命週期中。僅在可感知網路的 ApplicationContext 上下文中有效                                                         |
| application | Bean 存在於 ServletContext 的生命週期中。僅在可感知網路的 ApplicationContext 上下中有效                                                             |
| websocket   | Bean 存在於 WebSocket 的生命週期。僅在可感知網路的 ApplicationContext 上下中有效                                                                    |

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

- `bean`： `Spring` 容器中的對象都被稱為 `bean` 對象
  - `id屬性`：`bean` 對象的唯一標識，不可重複。
  - `class屬性`：`bean` 對象的完整類名。
  - `scope屬性`：設定 `bean` 的作用範圍，常用的是`singleton`(默認)和`prototype`
  - `property標籤`：用於屬性注入
    - `name屬性`：屬性名稱
    - `value屬性`：指定注入的普通屬性值
    - `ref屬性`：指定注入 Spring 容器中的`bean id`值
    - `list標籤`
    - `set標籤`
    - `map標籤`
    - `array標籤`
    - `properties標籤`
  - `constructor-arg標籤` 指定創建`bean`對象時調用構造方法的參數
- `import標籤`：導入其它配置文件

### Spring 相關 API

#### ApplicationContext 實現類

1. ClassPathXmlApplicationContext

   從類的根路徑下加載配置文件推薦使用這種

2. FileSystemXmlApplicationContext

   從硬碟加載配置文件，配置文件可以在硬碟任意位置

3. AnnotationConfigApplicationContext

   當使用註解配置容器對象時，需要使用此類來創建 Spring 容器，它用來讀取註解。

#### getBean 方法

該方法有兩種常用多載：

- public Object getBean(String name) throws BeanException

  傳入配置文件定義的`bean id`

- public <T> T getBean(class<T> requireType) throws BeanException

  傳入所需 `bean` 的類型，當配置中出現兩個 `bean` 都符合，拋出錯誤。

> 傳入`bean id`取得`bean`的好處是可以擁有多個相同類型的`bean`，傳入類型取得`bean`的好處是不用手動轉型，但是配置中只能有一個相同類型的`bean`

### Spring 配置數據源

#### 數據源(連接池)的作用

數據庫連接池提高數據庫訪問性能。

連接池使用步驟：

1. 事先創建連接池，初始化部分連接。
2. 從連接池中獲取連接使用。
3. 使用完畢後將連接歸還給連接池。

常見的數據庫連接池有：DBCP、C3P0、Druid 等

#### 配置文件讀取外部 properties 文件

數據庫的連接訊息通常會定義在單獨的 properties 文件中，下面介紹如何在 Spring 配置文件中讀其他 properties 文件的訊息。

1. 首先，需要引入 context 命名空間和約束路徑

   1. 命名空間：xmlns:context="http://www.springframework.org/schema/context"
   2. 約束路徑：
      1. http://www.springframework.org/schema/context
      2. http://www.springframework.org/schema/context/spring-context.xsd

2. 通過`context:property-placeholder`標籤引入外部 properties 文件

3. 使用 SPEL 取得 properties 文件值(${key}格式)。

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

`Spring` 是輕代碼種配置的框架，配置是非常繁重的，嚴重影響開發效率。所以註解開發是一種趨勢，註解替代 xml 配置文件，可以簡化配置、提高開發效率。

**`Spring` 框架註解區分為兩種，原始註解與新註解。原始註解出現的比較早，新註解出現的比較晚。**

#### Spring 原始註解

`Spring` 原始註解主要是替代`bean`標籤配置

| 註解           | 說明                                                                                                                               |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| @Component     | 使用在類上用於實例化 Bean                                                                                                          |
| @Controller    | 使用在 Controller 層類上用於實例化 Bean                                                                                            |
| @Service       | 使用在 Service 層類上用於實例化 Bean                                                                                               |
| @Repository    | 使用在 Dao 層上用於實例化 Bean                                                                                                     |
| @Autowired     | 根據類型進行依賴注入。**可以在構造器、方法、參數、成員變量上使用。**                                                               |
| @Qualifier     | 必須結合@Autowired 一起使用，用於根據`bean id`進行依賴注入                                                                         |
| @Resource      | 等於@Autowired + @Qualifier，根據 bean id 進行注入。改善@Qualifier 還須搭配@Autowired 一起使用問題。**可以在成員變量、方法上使用** |
| @Value         | 使用在成員變量上，注入普通屬性                                                                                                     |
| @Scope         | 標註 Bean 的作用範圍                                                                                                               |
| @PostConstruct | 使用在方法上，指定 Bean 的初始化方法，構造方法執行完後調用                                                                         |
| @PreDestroy    | 使用在方法上，指定 Bean 的銷毀方法，在 Bean 被銷毀前調用                                                                           |

> @Component、@Controller、@Service、@Repository 這四個註解功能都是告訴 Spring 該類為 bean。使用後三者能詳細區分出是分層架構中哪一層的 bean，幫助閱讀程式碼。如果無法歸類請使用@Component。

注意，使用註解進行開發時，需要在配置文件中配置組件掃描，作用是指定哪個包即其子包下的 Bean 需要進行掃描以便識別使用註解配置的 Class、Field、Method。

```xml
<!-- 註解類的組件掃描 -->
<context:component-scan base-package="目標package下所有類"></context:component-scan>
```

> @Value 注入可以搭配 SPEL 的方式，讀取在配置文件中導入的 properties 檔數據

##### 不推薦使用@Autowired 進行 Field 注入的原因

在使用 IDEA 開發時，在 Field 上使用 Spring 的依賴注入註解 `@Autowired` 後會出現如下警告

> Field injection is not recommended(成員變量注入是不推薦的)

###### @Autowired vs @Resource

實際上，它們的基本功能都是通過註解實現**依賴注入**，只不過 `@Autowired` 是 Spring 定義的，而 `@Resource` 是 JSR-250 定義的。功能大致相同，但是還有一些細節不同。

- 依賴識別方式：`@Autowired` 默認是 byType 可以搭配 `@Qualifier` 指定 Name，`@Resource` 默認 byName 如果找不到則 byType
- 適用對象：`@Autowired`可以對構造器、方法、參數、成員變量使用。`@Resource`只能對方法、成員變量使用。
- 提供方：`@Autowired` 是 Spring 提供。`@Resource` 是 JSR-250 提供的。

###### 各種依賴注入方式的優缺點

Spring 官方文檔，建議了如下的使用場景

- 構造器注入：強依賴性(即必須使用此依賴)，不變性(各依賴不會經常變動)
- Setter 注入：可選(沒有此依賴也可以工作)，可變(依賴會經常變動)
- Field 注入：大多數情況盡量少使用，一定要使用的話，`@Resource`相對`@Autowired`對 IOC 容器偶合度更低。

###### Field 注入的缺點

- 不能像構造器注入那樣注入不可變對象(指定強依賴)。
- 依賴對外部不可見，外界可以看到構造器和 setter，但是無法看到私有成員變量。
- 你的類和 DI 容器強耦合在一起
- 單元測試時，你無法直接初始化該類，必須依賴 DI 容器。因為 private 成員，沒提供訪問方法。
- @Autowired 太過方便容易讓你放棄對依賴的思考

##### 範例

未使用註解開發的 Spring xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userDao" class="org.learning.dao.impl.UserDaoImpl" scope="singleton" />

    <bean id="userService" class="org.learning.service.impl.UserServiceImpl" 					scope="singleton">
        <property name="userDao" ref="userDao" />
    </bean>
</beans>
```

使用註解開發

```java
// <bean id="userDao" class="org.learning.dao.impl.UserDaoImpl" scope="singleton" />
@Repository("userDao")
@Scope("singleton")
public class UserDaoImpl implements UserDao {

    @Override
    public void save() {
        System.out.println("save running ...");
    }
}
```

```java
@Service("userService")
@Scope("singleton")
public class UserServiceImpl implements UserService {

    // <property name="userDao" ref="userDao" />-->
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;

    // 使用@Autowired進行Field注入，不需要寫setter方法。當然@Autowired也能寫在setter和constructor		上
    /*
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
    */

    @Override
    public void doSave() {
        userDao.save();
    }

    // 對應bean標籤init-method屬性
    @PostConstruct
    private void init(){
        System.out.println("service do init");
    }

    // 對應bean標籤destroy-method屬性
    @PreDestroy
    private void destroy(){
        System.out.println("service do destroy");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="org.learning"></context:component-scan>
</beans>
```

#### Spring 新註解

使用原始註解並不能完全替代調 xml 上的配置

- 非自定義的 bean `<bean>`
- 加載 properties 文件的配置 `<context:property-placeholder>`
- 組件掃描配置 `<context:component-scan>`
- 引入其他配置文件 `<import>`

這時就由新註解來替代這些 xml 配置

| 註解            | 說明                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------ |
| @Configuration  | 指定當前類是一個 Spring 配置類，當創建容器時會從該類上加載註解。<br />將該類想像為一個 Spring 配置文件即可。 |
| @ComponentScan  | 用於指定 Spring 在初始化容器時要掃描的包，作用和 `<context:component-scan>` 一樣                             |
| @Bean           | 用於方法上，將方法返回值儲存到 Spring 容器中。                                                               |
| @PropertySource | 用於加載 properties 文件的配置                                                                               |
| @Import         | 導入其它 Spring 配置類                                                                                       |

現在使用新入解替代以下 xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <context:property-placeholder location="user.properties" />
    <bean id="user" class="org.learning.entity.User">
        <property name="name" value="${name}" />
        <property name="age" value="${age}" />
    </bean>
    <context:component-scan base-package="org.learning"></context:component-scan>
</beans>
```

首先需要創建一個主配置類，用於代表`applicationContext.xml`文件。

```java
// 標示該類為一個配置類
@Configuration
// <context:component-scan base-package="org.learning"></context:component-scan>
@ComponentScan("org.learning")
// 導入另一個配置類
@Import(UserConfiguration.class)
public class SpringConfiguration {
}
```

為了示範導入其它配置，這裡將加入非自定義 bean 的配置，放到另一個配置類`UserCongfiguration`

```java
// 標示該類為配置類
@Configuration
// 載入外部properties文件
@PropertySource("user.properties")
public class UserConfiguration {

    @Value("${name}")
    private String name;
    @Value("${age}")
    private String age;

    // 演示非自定義類如何使用註解方式，載入到Spring容器
    @Bean("user")
    public User getUser(){
        User user = new User();
        user.setName(name);
        user.setAge(age);
        return user;
    }
}
```

最後在創建容器時，需要使用`AnnotationConfigApplicationContext`類替代`ClassPathXmlApplicationContext`類，改為讀取配置類而不是 xml 配置文件方式，來創建 Spring 容器。

```java
AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
```

至此已完成使用 Spring 新註解替代 xml 配置。

### Spring 整合 junit

要測試容器內的 Bean 物件，每個方法的第一行都要創建容器，獲取相應 Bean。每個方法都需要寫類似的代碼有點繁瑣。

```java
ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
app.getBean("testBean");
```

- Spring 提供了 SpringJunit 負責創建容器，但是需要將配置文件名稱告訴它。

- 將需要測試的 Bean 直接在測試類進行注入。

想要使用 SpringJunit 有以下步驟

1. 在 maven 導入 Spring 集成 Junit 依賴(導入 junit 和 spring-test)

   ```xml
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
       <scope>test</scope>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-test</artifactId>
       <version>5.2.1.RELEASE</version>
   </dependency>
   ```

2. 使用 `@RunWith` 註解替換原來的運行期

3. 使用 `@ContextConfiguration` 指定配置文件或配置類，value 指定 xml 文件，classes 指定配置類

4. 使用 `@Autowired` 注入需要測試的對象

5. 創建測試方法進行測試

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {

    @Autowired
    UserService userService;

    @Test
    public void test1(){
        System.out.println(userService);
    }
}
```

### Spring AOP

AOP 為 Aspect Oriented Programming 的縮寫，意思為**面向切面編程**，是通過預編譯方試和運行**動態代理**實現程序功能的統一維護的一種技術。

**動態代理：不修改程式碼的情況下，對目標方法做相應的增強。實現程序之間的鬆耦合。**

AOP 是 OOP 的延續，是軟件開發中的一個熱點，也是 Spring 框架的一個重要內容，是函數編程的一種衍生範型。利用 AOP 可以對業務邏輯的各部分進行隔離，從而使得業務邏輯各部分之間的耦合度降低，提高程序的可用性，同時提高開發的效率。

#### AOP 的作用與優勢

- 作用：在程序運行期間，不修改源碼的情況下對此方法進行功能的增強。
- 優勢：減少代碼重複性，提高開發效率，並且便於維護。

#### AOP 的底層實現

實際上，Spring AOP 的底層是通過 Spring 提供的動態代理技術實現的。在運行期間，Spring 通過動態代理技術，動態的生成代理對象，代理對象方法執行時進行增強功能的介入，再去調用目標對象的方法，從而完成功能的增強。

常用的動態代理技術：

- JDK 代理：基於接口的動態代理技術
- cglib：基於類的動態代理技術

#### JDK 的動態代理

JDK 是基於接口現實動態代理技術，所以首先必須有**接口**，以及**目標對象(接口實現類)**。

> JDK 的動態代理不須引入其他第三方類庫。JDK 以內置相關功能。

JDK 動態代理參與角色：

1. 目標對象
2. 目標對象的接口(可能有多個)
3. 增強的功能，可能是另一個對象的方法。

範例：`TargetInterface` 為目標對象的接口、`Target`為目標對象、`Enhance` 為增強方法的對象

```java
public interface TargetInterface {
    void save();
}

public class Target implements TargetInterface{
    @Override
    public void save() {
        System.out.println("save is running ...");
    }
}

public class Enhance {
    // 前置增強
    public static void before(){
        System.out.println("before is running");
    }

    // 後置增強
    public static void after(){
        System.out.println("after is running");
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        Target target = new Target();
        // 生成代理對象使用Proxy.newProxyInstance，該方法接收三個參數
        //1. 目標對象的類加載器
        //2. 目標對象的接口數組
        //3. 調用處理類，調用代理對象的任何方法，其實都是執行invoke方法。


        TargetInterface proxyInstance = (TargetInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), new InvocationHandler() {
            //   invoke三個參數分別是 代理對象、目標對象方法、目標對象方法參數。
            //   invoke方法返回值為，執行完目標對象方法後的返回值。
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // 前置增強
                Enhance.before();
                // 目標對象方法
                Object invoke = method.invoke(target, args);
                // 後置增強
                Enhance.after();
                return invoke;
            }
        });

        // 調用代理對象的內的方法(接口定義方法)，Proxy.newProxyInstance返回的代理對象需強轉為目標接口類型。
        proxyInstance.save();
    }
}
```

#### cglib 的動態代理

cglib 為第三方類庫，Spring 近期的版本已經將其集成到`spring-core`下，在早期要使用 Spring 還需手動引入 cglib 依賴。

cglib 是基於父類的動態代理技術，參與的角色有：

1. 目標父類對象
2. 增強的功能

範例：`Target`為目標對象、`Enhance` 為增強方法的對象

```java
public class Target{
    public void save() {
        System.out.println("save is running ...");
    }
}

public class Enhance {
    // 前置增強
    public static void before(){
        System.out.println("before is running");
    }

    // 後置增強
    public static void after(){
        System.out.println("after is running");
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        final Target target = new Target();

        // cglib的動態代理
        // 1. 創建增強器
        Enhancer enhancer = new Enhancer();
        // 2. 設置父類(目標對象)
        enhancer.setSuperclass(Target.class);
        // 3. 設置回調方法
        enhancer.setCallback(new MethodInterceptor() {
            // 方法前三個參數與JDK代理invoke一致，第4個為方法的代理對象
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                // 前置增強
                Enhance.before();
                // 目標對象方法
                Object invoke = method.invoke(target, args);
                // 後置增強
                Enhance.after();
                return invoke;
            }
        });
        // 4. 創建代理對象(注意轉型)
        Target proxy = (Target) enhancer.create();


        proxy.save();
    }
}
```

#### Spring 的 AOP 簡介

##### AOP 概念

Spring 的 AOP 底層實現就是對上面動態代理代碼進行了封裝，封裝後我們只需要關注部分進行代碼編寫，並通過配置的方式完成指定目標方法的增強。

在正式講解 AOP 之前，我們必須理解 AOP 相關術語，常用術語如下：

- Target(目標對象)：代理的目標對象
- Proxy(代理對象)：一個類被 AOP 增強後，就產生一個代理對象
- Joinpoint(連接點)：所謂連接點是指那些被攔截到的點。在 Spring 中這些點指的是方法，因為 Spring 只支持方法類型的連接點。**可以被增強的方法就稱為「連接點」**。
- PointCut(切入點/切點)：所謂切入點就是對符合條件的 Joinpoint 進行攔截的定義。可以理解為**被增強的方法稱為「切入點」**。
- Advice(通知/增強)：攔截到 Joinpoint 之後要做的事情就是通知。就是指前置增強與後製增強的那些方法。
- Aspect(切面)：是切入點與通知的結合。就是 JDK 或 cglib 動態代理中的回調方法。
- Weaving(織入)：是指運行期間把增強應用到目標對象來創建代理對象的過程。Spring 採用動態代理織入，而 AspectJ 採用編譯時期織入與類裝載期織入。

##### AOP 開發明確事項

1. 需要編寫的內容

   - 編寫核心業務代碼(目標類的目標方法)
   - 編寫切面類，切面類中定義通知。
   - 配置文件中，配置織入關係，即哪些通知與哪些連接點進行結合。

2. AOP 技術實現的內容

   Spring 框架監控切入點方法的執行，一旦監控到切入點方法被運行，使用代理機制，動態創建目標對象的代理對象，根據通知類別，在代理對象對象的相應位置，將通知相應的功能織入，完成完整代碼邏輯運行。

3. 在 Spring 中，框架會根據目標類是否實現了接口來決定是使用 JDK 或 cglib

##### 基於 XML 的 AOP 開發

1. 導入 AOP 相關依賴

   Spring 有兩套 AOP 配置，Spring 原生與融合另一個更優秀的 AOP 框架 AspectJ。Speing 也推薦使用基於 AspectJ 所提供的 AOP 配置。

   ```xml
   <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjweaver</artifactId>
       <version>1.9.6</version>
   </dependency>
   ```

2. 創建目標接口和目標類(內有切點)

3. 創建切面類(內有增強方法)

4. 將目標類和切點類的對象創建權交給 Spring 管理(bean)

   Spring 大多數功能都是建立在 Spring 容器上，所以目標類與切面類都需交由 Spring 管理

5. 在 xml 配置文件織入關係(aop:config)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                               http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
   </beans>
   ```

   > xml 配置文件需先宣告 aop 命名空間

6. 測試代碼

###### 切點表達式的寫法

切點(Pointcut)表達式用來定義斷言，用於匹配、判斷哪些連接點(Joinpoint)是否要織入通知(Advice)。Spring AOP 的切點表達式主要借鏡於 AspectJ，然而並未全部支援，可使用的代號有以下幾個：

- `execution`：最主要的表示式，用於匹配方法執行的連接點。

- `within`：用於匹配目標物件必須是指定的類型(子類繼承的方法沒有 override 也算)的執行方法，可用來取代某些 `execution` 模式。

  > 詳情可以看 @target 和 @within 註解的比較，只是 within 是目標對象的類型，@within 是目標對象上的註解

- `this`：用於匹配 AOP 代理物件必須是指定的類型的執行方法。注意是 AOP 代理對象的類型匹配，這樣就包括引入接口類型匹類。常用於 Advice 中要取得代理物件之時。

- `target`：用於匹配目標物件必須是指定的類型的執行方法。注意是目標對象的類型匹配，這樣就不包括引入接口類型匹配。常用於 Advice 中要取得目標物件之時。

- `args`：用於匹配當前執行的方法的傳入的參數為指定的類型的執行方法，常用於 Advice 中要取得方法參數時。

- `@within`：用於匹配目標物件的執行方法，其目標物件持有指定註解。常用於 Advice 中要取得標註之時。

  > 與 @within、@target 的區別下面有詳細介紹

- `@target`：用於匹配目標物件的執行方法，其目標物件持有指定的註解。常用於 Advice 中要取得標註之時。

- `@args`：用於匹配當前執行方法持有指定註解的方法，常用於 Advice 中要取得標註之時。

- `@annotation`：方法上必須有指定的標註，常用於 Advice 中要取得標註之時。

- bean：Spring AOP 擴展的，用於匹配特定名稱的 Bean 對象的執行方法

表達式語法

```
execution([修飾符] 返回值類型 [包名.類名.]方法名(參數))
```

> `*`表示任意符號，`..`表示 0 或多個符號。

- 修飾符可以省略

- 包名、類名可以省略

- 返回值類型、包名、類型、方法名可以使用星號`*`代表任意

- 包名與類名之間一個點`.`代表當前包下的類，兩個點`..`表示當前包及其子包下的類

- 方法名可以使用\*來模糊匹配，例如：set\* 匹配以 set 開頭的方法

- 參數類表可以使用兩個點`..`表示任意個數、任意類型的參數

- 參數列表 (\*, String) 代表匹配方法須有兩個參數，第一個任意類型、第二個為 String 類型

- 參數列表 (.., String) 代表匹配方法至少有一個參數，當只有一個參數時，該參數類型必須為 String，當有多個參數時，最後參數的類型必須為 String

  > 注意：參數列表內除了常見的類型外，都必須以全類名指定

- 當通知中需要取得目標對象執行方法的參數時，可以通過在通知方法上宣告欲取得參數，並在參數列表中指定通知方法上的參數名稱，且還要在 @After、@Before 等註解提供的 argNames 屬性上，提供參數列表上相同的參數名稱(多個使用逗點分開)

  ```java
  @After(value = "execution(String com.godzilla.test..*.get*(abc, def))", argNames = "abc,def")
      public void execute5(String abc, String def){
      }
  ```

  上面的例子可以通過 args 表達式進行改寫，但是編譯器還是會建議你填上 argNames 屬性

  ```java
  @After("execution(String com.godzilla.test..*.get*(..)) && args(abc, def)")
  public void execute6(String abc, String def){}
  ```

類型匹配語法

很多地方都會按造類型的匹配，AspectJ 提供了以下類型匹配同配符

- \*：匹配任何數量字符。可用於返回值、包名(一層)、類名、方法名

- ..：匹配任何數量字符的重複，在類與包之間匹配任意數量的子包，在方法參數匹配任何數量的參數。(任意：0~N 個)

- +：匹配指定類型及其子類型。僅能做為後墜放在類型後邊

  | 表達式            | 說明                                                                                                            |
  | ----------------- | --------------------------------------------------------------------------------------------------------------- |
  | java.lang.String  | 匹配 String 類型                                                                                                |
  | java.\*.String    | 匹配 java 包下任何一級子包下的 String 類型。例如可以匹配 java.lang.String ，但不能匹配 java.lang.ss.String      |
  | java.lang.\*ing   | 匹配 java.lang 包下以 ing 結尾的類型                                                                            |
  | java.lang.Number+ | 匹配 java.lang 包下的 Number 類型及其子類型。例如匹類 java.lang.Number、java.lang.Integer、java.math.BigInteger |

範例

```
// 指定org.learning.aop包下的Target類中的無參method方法，且限定需沒有返回值
execution(public void org.learning.aop.Target.method())

// 指定org.learning.aop包下的Target類中所有任意參數的方法，且限定需沒有返回值
execution(void org.learning.aop.Target.*(..))

// 指定org.learning.aop包下的任意類中所有任意參數的方法，且沒有限定返回值
execution(* org.learning.aop.*.*(..))

// 指定org.learning.aop包及其子包下所有類中所有任意參數的方法，且沒有限定返回值
execution(* org.learning.aop..*.*(..))
// 任意包下的任意類的任意參數的方法，且沒有限定返回值
execution(* *..*.*(..))
```

`within` 限定必須是指定的型態，可以使用 `*` 或 `..`，某些 `execution` 模式，可以使用 `within` 取代，例如：

- `within(cc.openhome.model.AccountDAO)` 相當於 `execution(* cc.openhome.model.AccountDAO.*(..))`
- `within(cc.openhome.model.*)` 相當於 `execution(* cc.openhome.model.*.*(..))`
- `within(cc.openhome.model..*)` 相當於 `execution(* cc.openhome.model.service..*.*(..))`

Pointcut 表示式可以使用 `&&`、`||` 與 `!` 來作關係運算

```java
@Around("execution(* cc.openhome.model.AccountDAO.*(..)) && this(nullable)")
public Object around(ProceedingJoinPoint proceedingJoinPoint, Nullable nullable) throws Throwable

```

這表示，必須是 `cc.openhome.model.AccountDAO` 型態上的方法，而且代理物件必須是 `Nullable`，因為使用了 `&&`，而且 `this(nullable)` 中的 `nullable` 表示，從 `around` 方法上與 `nullable` 同名的參數得知型態。

當然，如果只是想限定代理物件必須是 `cc.openhome.aspect.Nullable`，只要寫 `this(cc.openhome.aspect.Nullable)` 就可以了。

其他的代號與 `this` 類似，也都可以用來在 Advice 中取得對應的物件；`args` 若有多個引數要定義，可以使用 `args(name, email)` 這類的形式，或者也可以使用 `args(name,..)` 表示至少有一個參數。

###### 通知的類型

通知配置語法

```xml
<aop:通知類型 method="切面類中方法名" pointcut="切點表達式" />
```

| 名稱         | 標籤                    | 說明                                                                 |
| ------------ | ----------------------- | -------------------------------------------------------------------- |
| 前置通知     | `<aop:before>`          | 用於配置前置通知。<br />指定的增強方法在切入點方法之前執行。         |
| 後置通知     | `<aop:after-returning>` | 用於配置後置通知。<br />指定的增強方法在切入點方法之後執行。         |
| 環繞通知     | `<aop:around>`          | 用於配置環繞通知。<br />指定的增強方法在切入點方法之前和之後都執行。 |
| 異常拋出通知 | `<aop:throwing>`        | 用於配置異常拋出通知。<br />指定切入點方法在出現異常時執行           |
| 最終通知     | `<aop:after>`           | 用於配置最終通知。<br />無論切入點的方法執行後是否有異常都會執行。   |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 目標對象bean -->
    <bean id="target" class="aop.Target" />

    <!-- 切面類bean -->
    <bean id="myAspect" class="aop.MyAspect" />

    <!-- spring aop 配置 -->
    <aop:config>
        <!-- 定義切面 -->
        <aop:aspect ref="myAspect">
            <!-- 定義織入關係 -->
            <!-- 前置增強 -->
            <aop:before method="before" pointcut="execution(public void aop.Target.save())"></aop:before>
            <!-- 後置增強 -->
            <aop:after-returning method="afterReturning" pointcut="execution(public void aop.Target.save())"></aop:after-returning>
            <!-- 環繞增強 -->
            <aop:around method="around" pointcut="execution(public void aop.Target.save())"></aop:around>
            <!-- 異常拋出增強 -->
            <aop:after-throwing method="afterThrowing" pointcut="execution(public void aop.Target.save())"></aop:after-throwing>
            <!-- 最終增強 -->
            <aop:after method="after" pointcut="execution(public void aop.Target.save())"></aop:after>

        </aop:aspect>
    </aop:config>
</beans>
```

###### 切點表達式的抽取

上面的範例 xml 配置，可以發現切點表達式`pointcut="execution(public void aop.Target.save())`頻繁出現，這時我們就可以將該切點表達式抽取出來，並為切點命名，之後使用`pointcut-ref`屬性替代`pintcut`屬性。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 目標對象bean -->
    <bean id="target" class="aop.Target" />

    <!-- 切面類bean -->
    <bean id="myAspect" class="aop.MyAspect" />

    <!-- spring aop 配置 -->
    <aop:config>
        <!-- 定義切點 -->
        <aop:pointcut id="myPointcut" expression="execution(public void aop.Target.save())"/>
        <!-- 定義切面 -->
        <aop:aspect ref="myAspect">
            <!-- 定義織入關係 -->
            <!-- 前置增強 -->
            <aop:before method="before" pointcut-ref="myPointcut"></aop:before>
            <!-- 後置增強 -->
            <aop:after-returning method="afterReturning" pointcut-ref="myPointcut"></aop:after-returning>
            <!-- 環繞增強 -->
            <aop:around method="around" pointcut-ref="myPointcut"></aop:around>
            <!-- 異常拋出增強 -->
            <aop:after-throwing method="afterThrowing" pointcut-ref="myPointcut"></aop:after-throwing>
            <!-- 最終增強 -->
            <aop:after method="after" pointcut-ref="myPointcut"></aop:after>

        </aop:aspect>
    </aop:config>
</beans>
```

##### 基於註解的 AOP 開發

開發步驟：

1. 創建目標接口和目標類(內部有切點)
2. 創建切面類(內部有通知/增強方法)
3. 將目標類和切面類對象創建權交給 Spring 管理
4. 在切面類中使用註解配置織入關係
5. 在 Spring 配置類中開啟組件掃描和 AOP 的自動代理
6. 測試

###### AOP 註解

通知註解的語法`@通知註解("切點表達式")`

| 註解                      | 說明                                                                                           |
| ------------------------- | ---------------------------------------------------------------------------------------------- |
| `@EnableAspectJAutoProxy` | 啟動 AspectJ 動態代理，使用於 Spring 配置類上                                                  |
| `@Aspect`                 | 標注該類為切面類                                                                               |
| `@Pointcut`               | 提取切點表達式，註解使用在方法上。<br />之後可以在各增強方法上以「方法名()」引用該切點表達式。 |
| `@Before`                 | 用於配置**前置通知**，指定的增強方法在切入點方法之前執行                                       |
| `@After-returning`        | 用於配置**後置通知**，指定的增強方法在切入點方法之後執行                                       |
| `@Around`                 | 用於配置**環繞通知**，指定的增強方法在切入點方法前後執行                                       |
| `@AfterThrowing`          | 用於配置**異常通知**，指定的增強方法在切入點方法出現異常後執行                                 |
| `@After`                  | 用於配置**最終通知**，指定的增強方法在切入點後執行(無論有無異常)。                             |

###### 範例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置組件掃描 -->
    <context:component-scan base-package="anno" />

    <!-- aop 自動代理 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

```java
@Component("myAspect")
// 標注該類為切面類
@Aspect
public class MyAspect {

    // 前置增強
    // <aop:before method="before" pointcut="execution(public void aop.Target.save())" />
    @Before("execution(public void anno.Target.save())")
    public void before(){
        System.out.println("前置增強");
    }

    // 後置增強
    // <aop:AfterReturning method="before" pointcut="execution(public void aop.Target.save())" />
    @AfterReturning("execution(public void anno.Target.save())")
    public void afterReturning(){
        System.out.println("後置增強");
    }

    /**
     * 環繞增強
     * @param joinPoint 切入點方法
     * @return 切入點返回值
     * @throws Throwable
     */
    // <aop:Around method="before" pointcut="execution(public void aop.Target.save())" />
    @Around("execution(public void anno.Target.save())")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("環繞-前");
        // 執行切入點方法
        Object o = joinPoint.proceed();
        System.out.println("環繞-後");

        return o;
    }

    // 異常拋出增強
    // <aop:AfterThrowing method="before" pointcut="execution(public void aop.Target.save())" />
    @AfterThrowing("execution(public void anno.Target.save())")
    public void afterThrowing(){
        System.out.println("異常拋出增強");
    }

    // 最終增強
    // <aop:After method="before" pointcut="execution(public void aop.Target.save())" />
    @After("execution(public void anno.Target.save())")
    public void after(){
        System.out.println("最終增強");
    }
}
```

###### 切點表達式抽取

範例中的`MyAspect`切面類，重複使用到同一個切點表達式。此時可以通過`@Pointcut`註解提取該表達式。之後就可以在各增強方法上使用「切面類名.方法名()」或「方法名()」的方式來引用該切面表達式。

```java
@Component("myAspect")
// 標注該類為切面類
@Aspect
public class MyAspect {
	// 提取切面表達式
    @Pointcut("execution(public void anno.Target.save())")
    public void myPointcut(){};

    // 前置增強
    // <aop:before method="before" pointcut="execution(public void aop.Target.save())" />
    @Before("myPointcut()")
    public void before(){
        System.out.println("前置增強");
    }

    // 後置增強
    // <aop:AfterReturning method="before" pointcut="execution(public void aop.Target.save())" />
    @AfterReturning("myPointcut()")
    public void afterReturning(){
        System.out.println("後置增強");
    }

    /**
     * 環繞增強
     * @param joinPoint 切入點方法
     * @return 切入點返回值
     * @throws Throwable
     */
    // <aop:Around method="before" pointcut="execution(public void aop.Target.save())" />
    @Around("myPointcut()")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("環繞-前");
        // 執行切入點方法
        Object o = joinPoint.proceed();
        System.out.println("環繞-後");

        return o;
    }

    // 異常拋出增強
    // <aop:AfterThrowing method="before" pointcut="execution(public void aop.Target.save())" />
    @AfterThrowing("myPointcut()")
    public void afterThrowing(){
        System.out.println("異常拋出增強");
    }

    // 最終增強
    // <aop:After method="before" pointcut="execution(public void aop.Target.save())" />
    @After("myPointcut()")
    public void after(){
        System.out.println("最終增強");
    }
}
```

#### @within、@target 表達式的區別

@within 和 @target 切點表達式，接受一個 annotation 作為表達式的值，都是用來判斷目標對象的類上是否有標註該註解，兩者有以下差異

- @within：會匹配到標示指定註解的類中的方法和子類中那些沒有被 override 的父類方法
- @target：會匹配到標示指定註解欸中的方法。**不涉及其他類**。

#### @annotation

@annotation 切點表達式，接受一個 annotition 作為表達式的值，用來匹配那些有標註該註解的方法

#### @args

@args 切點表達式，接受一個 annotation 作為表達式的值，用來匹配那些方法參數上有該註解的方法。

### Spring JdbcTemplate

它是 `Spring` 框架中提供的一個對象，是對原始繁瑣的 Jdbc API 對象的簡單封裝。`Spring` 框架提供了許多**操作模板類**，都是對一些其他技術的封裝。例如：操作關係型數據庫的 `JdbcTemplate`、`HibernateTemplate`，操作 nosql 數據庫的 `RedisTemplate`，操作消息隊列的 `JmsTemplate` 等等。

#### JdbcTemplate 開發步驟

1. 導入`spring-jdbc` 和 `spring-tx` 依賴。`spring-tx`是事務相關 API。
2. 創建數據表和表對應實體
3. 創建 `JdbcTemplate` 對象
4. 執行數據庫操作

```java
/**
     * 測試JdbcTemplate開發步驟
     */
@Test
public void test1() throws Exception {
    //1. 創建數據源(這裡使用c3p0數據庫連接池)
    ComboPooledDataSource dataSource = new ComboPooledDataSource();
    dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
    dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/spring_demo");
    dataSource.setUser("root");
    dataSource.setPassword("1qaz2wsx");

    //2. 創建jdbcTemplate對象
    JdbcTemplate jdbcTemplate = new JdbcTemplate();

    //3. 設置數據源
    jdbcTemplate.setDataSource(dataSource);

    //4. 操作數據庫
    int row = jdbcTemplate.update("insert into account values (?,?)", "James", 5000);
    System.out.println(row);
}
```

#### Spring 產生 JdbcTemplate 對象

可以將`JdbcTemplate`與`DataSource`的創建權交給 Spring，在 Spring 容器內部將數據源 `DataSource`注入到 `JdbcTemplate` 模板對象中。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_demo"/>
        <property name="user" value="root"/>
        <property name="password" value="1qaz2wsx"/>
    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

上方的資料庫連接訊息也可以抽取為`.properties`文件，在 Spring xml 配置文件引入該文件，通過`SPEL`讀取對應數據。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 引入外部properties文件 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
    <!-- SPEL取值 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

#### JdbcTemplate 常規操作

- 更新操作：增、刪、改

  針對數據庫的增加、刪除、修改都是調用`JdbcTemplate`物件的`update`方法。

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration("classpath:applicationContext.xml")
  public class JdbcTemplateCRUDTest {

      @Autowired
      private JdbcTemplate jdbcTemplate;

      /**
       * 儲存
       */
      @Test
      public void saveTest() {
          String sql = "insert into account values(?,?)";
          int row = jdbcTemplate.update(sql, "Peter", "2999");
          System.out.println(row);
      }

      /**
       * 更新
       */
      @Test
      public void updateTest() {
          String sql = "update account set money = ? where name = ?";
          int row = jdbcTemplate.update(sql, 20000, "Peter");
          System.out.println(row);
      }

      /**
       * 刪除
       */
      @Test
      public void deleteTest(){
          String sql = "delete from account where name = ?";
          int row = jdbcTemplate.update(sql, "Peter");
          System.out.println(row);
      }
  }
  ```

- 查詢操作

  針對數據庫的查詢是調用`JdbcTemplate`物件的`query`方法。比較常用的方法是：

  - `List<T> query(String sql, RowMapper<T> rowMapper, Object ... args)`

    該方法會將每列封裝為對應物件，並返回存放這些物件的 List。**適合查詢多筆資料時使用**。

    `rowMapper` 為列轉換器，可以選擇自己實現該接口。Spring 也有提供相關實現類供開發者使用，如果對象與欄位名吻合可以使用`BeanPropertyRowMapper`，該轉換器會將每列轉換為對應的物件，**使用時須指定泛型與在構造器指定物件 class。**

    ```java
    /**
     * 查詢全部資料
     */
    @Test
    public void queryListTest(){
        List<Account> list = jdbcTemplate.query("select * from account", new BeanPropertyRowMapper<Account>(Account.class));
        list.forEach(System.out::println);
    }
    ```

  - `T queryForObject(String sql, RowMapper<T> mapper, Object ... args)`

    該方法回將第一列封裝為對應的物件，並返回該物件。**適合查詢單一筆資料時使用**。

    ```java
    /**
    * 查詢單筆資料
    */
    @Test
    public void queryOneTest(){
       String sql = "select * from account where name= ?";
       Account account = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(Account.class),"Peter");
       System.out.println(account);
    }
    ```

  - `T queryForObject(String sql, Class<T> requireType, Object args)`

    該方法將查詢返回的值強轉為指定的類型。**適合查詢返回值為簡單數據類型**。

    ```java
    /**
    * 查詢結果為簡單數據類型
    */
    @Test
    public void querySampleTypeTest(){
        String sql = "select count(*) from account";
        Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
        System.out.println(count);
    }
    ```

### Spring 事務控制

#### 編程式事務控制

編程式是指使用 Java API 方式進行事務控制。學會手動使用 API 控制事務，才能更好的使用 xml 配置和註解方式的事務控制。

##### 事務控制相關對象

- `PlatformTransactionManager`

  該接口是 Spring 的事務管理器，裡面提供了常用的事務操作方法。

  | 方法                                                                 | 說明               |
  | -------------------------------------------------------------------- | ------------------ |
  | `TransactionStatus getTransaction(TransactionDefination defination)` | 獲取事務的狀態訊息 |
  | `void commit(TransactionStatus status)`                              | 提交事務           |
  | `void rollback(TransactionStatus status)`                            | 回滾事務           |

  > `PlatformTransactionManager` 是接口，不同的 Dao 層技術有不同的實現類。
  >
  > 例如：Dao 層技術是 Jdbc 和 mybatis 時，實現類為 `org.springframework.jdbc.datasource.DataSourceTransactionManager`。
  >
  > Dao 層技術是 hibernate 時，實現類為`org.springframework.orm.hibernate5.HibernateTransactionManager`

- `TransactionDefinition`

  事務的定義訊息對象

  | 方法                         | 說明                                                                                            |
  | ---------------------------- | ----------------------------------------------------------------------------------------------- |
  | `int getIsolationLevel()`    | 獲得事務的隔離級別                                                                              |
  | `int getPropagationBehavior` | 獲得事務的傳播行為                                                                              |
  | `int getTime()`              | 獲得超時時間<br />默認值是-1，沒有時間限制。如果有，以秒為單位設置。                            |
  | `boolean isReadOnly()`       | 是否只讀。<br />建議查詢時設置為以讀。                                                          |
  | `getTransactionManager()`    | 獲得該交易所指定使用的`transactionManager`<br />當 Spring 容器內超過兩個交易管理器時需指定      |
  | `getRollbackFor`             | 獲得當遇到那些錯誤時會執行 rollback 操作，默認是`RunTimeException`。<br />建議設置為`Exception` |

- `TransactionStatus`

  接口提供的是事務具體的運行狀態，方法介紹如下：

  | 方法                         | 說明                 |
  | ---------------------------- | -------------------- |
  | `boolean hasSavePoint()`     | 事務是否有設置保存點 |
  | `boolean iSCompleted()`      | 事務是否已完成       |
  | `boolean isNewTransaction()` | 事務是否是新事務     |
  | `boolean isRollbackOnly`     | 事務是否回滾         |

##### 事務的隔離級別

設置隔離級別，可以解決事務併發產生的問題。如：髒讀、不可重複讀、幻讀。

1. `ISOLATION_DEFAULT` 數據庫默認
2. `ISOLATION_READ_UNCOMMIT` 讀未提交，髒讀、不可重複讀、幻讀皆無法解決
3. `ISOLATION_READ_COMMIT` 讀已提交，可以解決髒讀，其他無法解決。
4. `ISOLATION_REPEATABLE_READ` 可重複讀，可以解決髒讀、不可重複讀，其他無法解決
5. `ISOLATION_SERIALIZABLE` 序列化，全部都可解決。但是性能最差。

##### 事務的傳播行為

指的是當 A 業務方法中調用 B 業務方法，且當 A、B 業務都有事務控制時，它們會出現重複、統一的問題。事務傳播行為就是為了解決該問題。下面列出可以設定的傳播行為：

- **REQUIRED：如果 A 當前沒有事務，B 就新建一個事務，如果 A 已經存在事務中，B 加入這個事務中。一般的選擇(默認值)。**
- **SUPPORTS：如果 A 當前沒有事務，B 就以非事務方式運行。如果 A 已經存在事務，B 加入這個事務**。
- MANDATORY：B 加入 A 當前的事務，如果 A 當前沒有事務，就拋出異常。
- REQUEST_NEW：如果 A 當前以存在事務，B 新建一個事務，把 A 事務掛起。
- NOT_SUPPORT：B 以非事務方式運行，如果 A 當前存在事務，就把 A 事務掛起。
- NEVER：B 以非事務方式運行，如果 A 當前存在事務，拋出異常。
- NESTED：如果 A 當前存在事務，則 B 在嵌套事務內執行。如果 A 當前沒有事務，則執行 REQUIRED 類似的操作。

#### 基於 XML 的聲明式事務控制

Spring 的聲明式事務控制，顧名思義就是採用聲明的方式來處理事務。這裡所說的聲明，就是指在配置文件中聲明。Spring 通過聲明訊息，來主動幫你處理事務，而不需由你主動使用 API 來控制事務。

聲明式的優點：

- 通過聲明的方式，業務邏輯就可以與事務控制邏輯切分開來。在開發時業務邏輯不會意識到正在事務管理之中，事實上也應該如此。因為事務控制是屬於系統層面的服務，而不是業務邏輯的一部分。

- 如果想改變事務管理的方式，只需更改配置文件
- 在不需要事務管理時，只要在配置文件上修改一下，即可移除事務管理服務，無須改變代碼重新編譯。

> Spring 事務控制的底層就是通過 AOP 實現的。

##### 基於 Aspectj AOP 配置事務操作步驟

1. 導入`aspectjweaver`、`spring-jdbc` 、 `spring-tx` 依賴。`spring-tx`是事務相關 API。
2. 創建數據表和表對應實體
3. XML 配置文件需引入事務的命名空間 `tx`
4. XML 配置數據源`DataSource`與`JdbcTemplate`
5. 根據所使用 DAO 層技術配置對應的`PlatformTransactionManager`的實現類
6. 配置事務的通知。使用 `<tx:advice>` 標籤。子標籤 `<tx-attribute>` 內部還可以針對各個不同的方法配置若干個`<tx-method>`
7. 使用 AOP 配置事務的織入。事務使用標籤 `<aop-advisor>`來配置織入。該標籤與`<aop:aspect>`功能相同，只是大多用來配置事務。

##### 操作範例

AOP 配置事務範例。數據庫連接池使用 c3p0，數據庫使用 mysql。

1. 確認是否導入相關依賴。`spring-context`、`aspectjweaver`、`spring-jdbc`、`spring-tx`、`c3p0`、`mysql-connector-java`

2. 定義數據表與對應實體

   ```sql
   drop table account;
   create table account(
   	name varchar(100),
       money double
   );
   insert into account values('James',5000);
   insert into account values('Peter',5000);
   ```

   ```java
   public class Account {
       String name;
       double money;
   	// 以下省略getter、setter、toString方法
   }
   ```

3. 編寫`Dao`、`Service`、`Controller`

   ```java
   public interface AccountDao {

       void out(String name, double money);

       void in(String name, double money);
   }

   public class AccountDaoImpl implements AccountDao {

       private JdbcTemplate jdbcTemplate;

       public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
           this.jdbcTemplate = jdbcTemplate;
       }

       /**
        * 提款
        * @param name 帳戶
        * @param money 提款金額
        */
       @Override
       public void out(String name, double money) {
           jdbcTemplate.update("update account set money = money - ? where name = ?", money, name);
       }

       /**
        * 存款
        * @param name 帳戶
        * @param money 存款金額
        */
       @Override
       public void in(String name, double money) {
           jdbcTemplate.update("update account set money = money + ? where name = ?", money, name);

       }
   }
   ```

   ```java
   public interface AccountService {
       void transfer(String origin, String target, double money);
   }

   public class AccountServiceImpl implements AccountService {

       private AccountDao accountDao;

       public void setAccountDao(AccountDao accountDao){
           this.accountDao = accountDao;
       }

       @Override
       public void transfer(String origin, String target, double money) {
           accountDao.out(origin, money);
           int i = 1/0; // 自殺，看是否有回滾
           accountDao.in(target, money);
       }
   }
   ```

   ```java
   public class App {
       public static void main(String[] args) {
           ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
           AccountService service = app.getBean(AccountService.class);
           service.transfer("James", "Peter", 500);
       }
   }
   ```

4. 編寫 XML 配置

   配置事務管理器的配置必須根據 Dao 層的技術使用不同的`PlatformTranctiinManager`實現類。`JdbcTemplate`、`Mybatis` 使用的是 `DataSourceTransactionManager`。

   `<tx:advice>`用於配置事務的通知/增強，至少配一個`<tx:method>`，`name`屬性可以使用萬用字元`*`代表`0~N`個字元。例如：update\*、delete\*等等，**否則將沒有方法受到事務管理**。

   有點類似第二次配置的概念。切點表達式斷言了`service`切面類中哪些連接點為切點。而`<tx:method>`是針對各切點方法的`事務定義`。這樣的好處是可以根據切點方法內邏輯做不同的`事務定義`(隔離級別、傳播行為等)。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/tx
                              http://www.springframework.org/schema/tx/spring-tx.xsd
                              http://www.springframework.org/schema/aop
                              https://www.springframework.org/schema/aop/spring-aop.xsd">
       <!-- 配置c3p0數據源 -->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
           <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_demo"/>
           <property name="user" value="root"/>
           <property name="password" value="1qaz2wsx"/>
       </bean>
       <!-- 配置jdbcTemplate -->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <property name="dataSource" ref="dataSource"/>
       </bean>
       <!-- 配置dao -->
       <bean id="accountDao" class="dao.impl.AccountDaoImpl">
           <property name="jdbcTemplate" ref="jdbcTemplate"/>
       </bean>
       <!-- 配置service -->
       <bean id="accountService" class="service.impl.AccountServiceImpl">
           <property name="accountDao" ref="accountDao"/>
       </bean>

       <!-- 配置事務管理器(jdbcTemplate和mybatis適用) -->
       <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource"/>
       </bean>

       <!-- 配置事務通知 -->
       <tx:advice id="tx" transaction-manager="txManager">
           <tx:attributes>
               <!-- 各別設定各切點方法的事務配置 -->
               <!-- name="*" 匹配所有方法 -->
               <tx:method name="*" isolation="DEFAULT" propagation="REQUIRED" read-only="false" timeout="-1"/>
           </tx:attributes>
       </tx:advice>

        <!-- aop配置 -->
       <aop:config>
           <!-- 織入事務通知/增強 -->
           <aop:advisor advice-ref="tx" pointcut="execution(* service.impl.*.*(..))"/>
       </aop:config>
   </beans>
   ```

#### 基於註解的事務控制

##### 事務相關註解

| 註解                           | 說明                                                                                                                                                                                                                          |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `@Transactional`               | 可以應用在類或方法上，使用在類上表示該類中的所有方法都套用該配置，<br />如果類與方法上都有，則採就近原則。<br />該註解可用屬性進行事務定義                                                                                    |
| `@EnableTransactionManagement` | 用於 Spring 配置類上，表示啟用交易管理。**使用時 Spring 容器內需要有`TransactionManager`的實現類物件。** <br />當 Spring 容器內存在兩個交易管理器，需要`@Transaction`註解使用`transactionManager`屬性指定使用哪個交易管理器。 |

##### 註解配置事務相關 XML 標籤

| 標籤                     | 說明                                                           |
| ------------------------ | -------------------------------------------------------------- |
| `<tx:annotation-driven>` | 開啟事務的註解驅動。如果缺少此配置，`@Transactional`註解無效。 |

##### 範例

```java
@Repository("AccountDao")
public class AccountDaoImpl implements AccountDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;


    /**
     * 提款
     * @param name 帳戶
     * @param money 提款金額
     */
    @Override
    public void out(String name, double money) {
        jdbcTemplate.update("update account set money = money - ? where name = ?", money, name);
    }

    /**
     * 存款
     * @param name 帳戶
     * @param money 存款金額
     */
    @Override
    public void in(String name, double money) {
        jdbcTemplate.update("update account set money = money + ? where name = ?", money, name);

    }
}
```

```java
@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;

    @Override
    public void transfer(String origin, String target, double money) {
        accountDao.out(origin, money);
        int i = 1/0;
        accountDao.in(target, money);
    }
}
```

```java
@Service("accountService")
// 配置事務管理
@Transactional(isolation = Isolation.DEFAULT, timeout = -1, propagation = Propagation.REQUIRED, readOnly = false)
public class AccountServiceImpl implements AccountService {

    @Autowired
    private AccountDao accountDao;

    @Override
    public void transfer(String origin, String target, double money) {
        accountDao.out(origin, money);
        int i = 1/0;
        accountDao.in(target, money);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:contex="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/tx
                           http://www.springframework.org/schema/tx/spring-tx.xsd
                           http://www.springframework.org/schema/context
                           https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置c3p0數據源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_demo"/>
        <property name="user" value="root"/>
        <property name="password" value="1qaz2wsx"/>
    </bean>
    <!-- 配置jdbcTemplate -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置事務管理器(jdbcTemplate和mybatis適用) -->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置組件掃描(必要) -->
    <contex:component-scan base-package="org.learning"/>
    <!-- 配置註解驅動(必要) -->
    <tx:annotation-driven transaction-manager="txManager" />
</beans>
```

將大部分的 XML 配置都改用註解方式，如果想要完全替代掉 XML，需要建立 Spring 配置類進行以下配置。

當中將不是我們自定義的類通過`@Bean`加入至 Spring 容器中。比較特別的是`@EnableTransactionManager`啟動交易管理，容器內需要有一個以上的平台管理器(`PlatformTransactionManager`實現類)。

```java
//標註該類為Spring配置類
@Configuration
//組件掃描
@ComponentScan("org.learning")
// 啟用交易管理
@EnableTransactionManagement
public class SpringConfiguration {

    @Bean("dataSource")
    public ComboPooledDataSource getDataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/spring_demo");
        dataSource.setUser("root");
        dataSource.setPassword("1qaz2wsx");
        return dataSource;
    }

    @Bean("jdbcTemplate")
    public JdbcTemplate getJdbcTemplate(@Autowired ComboPooledDataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);

        return jdbcTemplate;
    }

    @Bean("txManager")
    public TransactionManager getTransactionManager(@Autowired ComboPooledDataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```
