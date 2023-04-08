# Spring6

## 准备工作

### 新建父工程

首先我们创建一个父工程，名为`Spring6`

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled.png)

这里的`JDK版本`要选择17，`maven版本`要达到3.6及以上，创建项目之后进入`IDEA`主界面，删除父工程`Spring6`的src包。紧接着创建`module工程`，名为`spring-first`。

### 配置Maven

然后我们要配置`maven`，我们需要引入`spring`相关的`maven`坐标，如下

```xml
<dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>6.0.2</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.junit.jupiter/junit-jupiter-api -->
            <!--junit-->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.3.1</version>
        </dependency>
    </dependencies>
```

完成配置后点击右侧`maven`刷新按钮，等待下方进度条加载完毕且`pom.xml`不再报错，即为配置成功。

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%201.png)

### 写入测试方法

在main主体类下java包中创建一个`package`名为`com.atguigu.spring6`，在该`package`下新建一个`User类`，内容如下

```java
package com.atguigu.spring6;

public class User {
    public void add(){
        System.out.println("add......");
    }

    public static void main(String[] args) {
        User user = new User();
        user.add();
    }
}
```

### 写入XML文件

然后我们在`resources`下面新建一个`xml文件`，具体操作见下图

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%202.png)

我们将这个`xml`命名为`bean`，配置内容见下方代码块

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--完成user对象创建
bean标签
id属性:唯一标识
class属性:要创建对象所在类的全路径(包名+类名)
-->
<bean id="user" class="com.atguigu.spring6.User"></bean>
</beans>
```

在`xml`文件中，我们添加一个`bean标签`用于完成user对象的创建。

- `id`属性值唯一，这一点我们已经在`Javaweb`中熟知，`id`我们一般取名为所要完成类的对象的名字的小写
- `class`属性我们要求配置为创建对象所在类的全路径(包名+类名)，一般来说都会有智能提示。

### 写入测试类

配置完`xml`后，我们在`com.atguigu.spring6`下新建一个`TestUser`类，用于测试`User`类，内容如下

- 第3行加载了`Spring`的配置文件"bean.xml"，并创建了一个`ApplicationContext`对象`context`。

```java
 ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
```

- 第5行通过`context`的`getBean`方法获取了名为"user"的`bean`对象，并将其强制转换为`User`类型。

```java
User user = (User) context.getBean("user");
```

- 第6行打印了`user`对象的字符串表示形式（`toString`方法），用于检查对象是否被成功创建。

```java
System.out.println(user);
```

- 第8行调用了`user`对象的`add()`方法进行测试。

```java
user.add();
```

### 代码一览

```
package com.atguigu.spring6;

import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUser {
    @Test
    public void testUserObject(){
//        加载spring配置文件,对象创建
        ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
//        获取创建的对象
        User user = (User) context.getBean("user");
        System.out.println(user);
//        使用对象调用方法进行测试
        user.add();
    }
}
```

### 添加无参构造器

我们在`User`类中添加无参构造器，验证在测试`User`类的时候是否调用无参构造器

```java
public User(){
        System.out.println("无参数构造执行");
    }
```

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%203.png)

### 创建`testUserObject1`方法

接着我们在`TestUser`类中创建`testUserObject1`方法，获取类Class的对象

```java
Class clazz = Class.forName("com.atguigu.spring6.User");
```

### 抛出异常

`forName`本身会有一个异常，抛出即可。然后我们调用`User`对象去打印输出该对象的值

```java
User user = (User)clazz.getDeclaredConstructor().newInstance();
        System.out.println(user);
```

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%204.png)

### 整合日志

接下来我们对`log4j`日志进行整合，首先我们引入`maven`坐标

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.19.0</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.19.0</version>
</dependency>
```

### 配置`log4j2.xml`文件

然后我们在`resources`路径下新建一个`log4j2.xml`文件，配置内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <loggers>
        <!--
            level指定日志级别，从低到高的优先级：
                TRACE < DEBUG < INFO < WARN < ERROR < FATAL
                trace：追踪，是最低的日志级别，相当于追踪程序的执行
                debug：调试，一般在开发中，都将其设置为最低的日志级别
                info：信息，输出重要的信息，使用较多
                warn：警告，输出警告的信息
                error：错误，输出错误信息
                fatal：严重错误
        -->
        <root level="DEBUG">
            <appender-ref ref="spring6log"/>
            <appender-ref ref="RollingFile"/>
            <appender-ref ref="log"/>
        </root>
    </loggers>

    <appenders>
        <!--输出日志信息到控制台-->
        <console name="spring6log" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss SSS} [%t] %-3level %logger{1024} - %msg%n"/>
        </console>

        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，适合临时测试用-->
        <File name="log" fileName="d:/spring6_log/test.log" append="false">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
        </File>

        <!-- 这个会打印出所有的信息，
            每次大小超过size，
            则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，
            作为存档-->
        <RollingFile name="RollingFile" fileName="d:/spring6_log/app.log"
                     filePattern="log/$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz">
            <PatternLayout pattern="%d{yyyy-MM-dd 'at' HH:mm:ss z} %-5level %class{36} %L %M - %msg%xEx%n"/>
            <SizeBasedTriggeringPolicy size="50MB"/>
            <!-- DefaultRolloverStrategy属性如不设置，
            则默认为最多同一文件夹下7个文件，这里设置了20 -->
            <DefaultRolloverStrategy max="20"/>
        </RollingFile>
    </appenders>
</configuration>
```

### 执行测试方法

然后我们回到`TestUser`类中，执行`testUserObject`方法，观察控制台输出结果，见下图

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%205.png)

### 手动写入日志

接下来我们要实现的功能就是手动写日志。我们先在`TestUser`中创建`Logger对象`

```java
private Logger logger = LoggerFactory.getLogger(TestUser.class);
```

📌这里的`package`千万不要导错了，在`TestUser`中正确的包名见下图

```java
import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
```

在`testUserObject`方法里面我们手动写入日志信息

```java
logger.info("###执行调用成功###");
```

观察控制台输出结果

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%206.png)

至此，`log4j2`日志导入正式完成。

## IOC系统学习

接下来我们要学习一种新容器：IoC容器，即控制反转，我们在`spring`里面通过`IoC容器`管理`java`对象的实例化和初始化，我们将由IoC容器管理的`java对象`称为`Spring Bean`，这一点在和我们学`javase`中使用`new`来创建对象没有任何区别。（只是为了区分，在`spring`里面叫`IoC`罢了😉）

### 什么是依赖注入？

接下来我们要讲解一下什么是**依赖注入**，常见的依赖注入包括如下两种方式

- `set`注入
- **构造**注入

### 实现初步注入

我们在`com.atguigu.spring6`包下新建一个`Person`类，在User类中注入`Person`的属性，名为`person`。

```java
public class User {
    private String name ;
    private Person person ;
...
}
```

这里是初步实现注入的思想，后续我们会在`spring`源码部分进行详细讲解

### 迁移Maven坐标

为了对`xml`以及`maven`进行统一管理，我们将`spring-first`工程里面的`maven`坐标统一迁移到父工程`Spring6`中去

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%207.png)

📌最后不要忘记刷新`maven`

### 新建子工程

紧接着我们在`Spring6`中创建一个子工程，名为`spring6-ioc-xml`，继承父工程，`JDK`版本选择**17**，依旧是`maven`版本。

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%208.png)

初步配置见下图，不再赘述（`bean.xml`未进行配置）

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%209.png)

#### 复制XML文件

接着我们将`log4j2.xml`从`spring-first`工程中复制到`spring6-ioc-xml`下，并放入相对应的位置（如果不知道在哪里的请回看`spring-first`的创建过程☹）

我们在`com.atguigu.spring6.iocxml`包下新建一个`class`文件名为`TestUser`作为测试类进行测试。

#### 配置XML文件

然后我们对`bean.xml`进行配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--user对象创建-->
    <bean id="user" class="com.atguigu.spring6.iocxml.User"></bean>
</beans>
```

首先我们通过`id`来获取`bean`，`TestUser`类配置内容如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2010.png)

运行结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2011.png)

#### 根据类型获取bean

```java
User user2 = context.getBean(User.class);
System.out.println("根据类型获取bean:" + user2);
```

输出的结果与第一种方式无异。

#### 根据id和类型获取bean

```java
User user3 = context.getBean("user", User.class);
System.out.println("根据id和类型获取bean:" + user3);
```

输出的结果与第一、二种方式无异。

### 注意事项

📌当我们根据类型获取`bean`的时候，要求我们的`IOC`容器中指定类型的`bean`只能有一个

当我们在`bean.xml`中写入如下配置

```java
<!--user对象创建-->
    <bean id="user" class="com.atguigu.spring6.iocxml.User"></bean>
    <bean id="user1" class="com.atguigu.spring6.iocxml.User"></bean>
```

我们在`TestUser`类中启动相关测试方法的时候，会发现报如下错误

```java
Exception in thread "main" org.springframework.beans.factory.
NoUniqueBeanDefinitionException:
 No qualifying bean of type 'com.atguigu.spring6.iocxml.User' available:
 expected single matching bean but found 2: user,user1
```

这句话很明显的告诉我们：没有为`com.atguigu.spring6.iocxml.User`匹配到一个合适的对象，`Spring6`期望我们提供一个单实例的对象，但是我们却提供了两个，一个叫`user`，一个叫`user1`，因此报错。

### 通过接口实现类的获取

接下来我们通过接口的方式去实现类获取的过程，首先我们在`com.atguigu.spring6.iocxml`包下新建一个`package`名为`bean`，分别建立`UserDao`接口以及`UserDaoImpl`实现类，内容如下

```java
package com.atguigu.spring6.iocxml.bean;
public interface UserDao {
    public void run();
}
```

```java
package com.atguigu.spring6.iocxml.bean;
public class UserDaoImpl implements UserDao{
    @Override
    public void run() {
        System.out.println("run.......");
    }
}
```

#### 配置XML文件

接着我们去配置一下`bean.xml`文件，我们同样是通过`id`和`class`属性去获取我们所要得到的实现类，以验证`IOC`反转控制的思想

```xml
<!--一个接口实现类获取过程-->
    <bean id="UserDaoImpl" class="com.atguigu.spring6.iocxml.bean.UserDaoImpl"></bean>
```

#### 写入测试类

最后我们在`package`包下新建一个测试类名为`TestUserDao`，写入下方代码

```java
package com.atguigu.spring6.iocxml.bean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUserDao {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
//        根据类型获取接口对应bean
        UserDao userDao = context.getBean(UserDao.class);
        System.out.println(userDao);
        userDao.run();
    }
}
```

最终我们打印输出的结果在控制台有所显示：

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2012.png)

接下来我们实现一个接口对应多个实现类的业务场景。我们在`bean`下新建一个类叫`PersonDaoImpl`，并将它继承于`UserDao`接口。 

```java
package com.atguigu.spring6.iocxml.bean;

public class PersonDaoImpl implements UserDao{
    @Override
    public void run() {
        System.out.println("person run.....");
    }
}
```

#### 编写Bean标签

然后我们去`bean.xml`中进行配置接口所对应的`bean`标签

```xml
<!--一个接口实现多个类的过程-->
    <bean id="PersonDaoImpl" class="com.atguigu.spring6.iocxml.bean.PersonDaoImpl"></bean>
```

最后我们再对`TestUserDao`进行测试，报错信息如下

```java
Exception in thread "main" org.springframework.beans.factory.
NoUniqueBeanDefinitionException: 
No qualifying bean of type 'com.atguigu.spring6.iocxml.bean.UserDao' available: 
expected single matching bean but found 2: UserDaoImpl,PersonDaoImpl
```

很明显，报错原因是因为`bean`不唯一。

#### 根据set方法注入

接下来我们通过`set`方法注入。首先我们在`com.atguigu.spring6.iocxml`下新建一个`package`名为`di`，新建一个类名为`Book`，内容如下

```java
package com.atguigu.spring6.iocxml.di;

public class Book {
    private String bname ;
    private String author ;

    public String getBname() {
        return bname;
    }

    public void setBname(String bname) {
        this.bname = bname;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public static void main(String[] args) {
//        set方法注入
        Book book = new Book();
        book.setBname("java");
        book.setAuthor("尚硅谷");
    }
}
```

根据上方代码块我们可以得知，我们通过`setter`的途径去对`Book`对象注入相关属性，这是我们最常见的`set`注入，接下来我们另一种`set`注入方法。

首先先对`Book`类添加相关构造器

```java
public Book() {
    }

public Book(String bname, String author) {
        this.bname = bname;
        this.author = author;
    }
```

通过`New`对象的方式进行注入

```java
//        通过构造器注入
        Book book1 = new Book("c++","尚硅谷");
```

接下来介绍`Spring6`中特有的`set`注入方法。我们在`resources`下新建一个`xml`名为`bean-di`，具体配置内容见下方代码块

```xml
<!--1.set方法注入-->
    <bean id="book" class="com.atguigu.spring6.iocxml.di.Book">
        <property name="bname" value="前端开发"></property>
        <property name="author" value="尚硅谷"></property>
    </bean>
```

在这个`xml`中，我们引入一个新的标签名为`property`，其中`name`属性的值与你在`Book`中写入的变量有关，`value`的值即为上文`set`方法注入的属性拥有同等效力。

然后我们在`Book`类中写入`toString()`方法，用于打印输出相关信息。接着我们在`di`中写入测试类，相关测试信息与之前无异，读者仿照前文自行写入测试类即可，测试结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2013.png)

### 构造器注入

接下来我们尝试一下`构造器注入`

首先我们去`xml`中进行配置

```xml
<!--2.构造器注入-->
    <bean id="bookCon" class="com.atguigu.spring6.iocxml.di.Book">
        <constructor-arg name="bname" value="java开发"></constructor-arg>
        <constructor-arg name="author" value="尚硅谷"></constructor-arg>
    </bean>
```

在上方代码块中，我们引入了一个新标签名为`constructor-arg`，该`bean`标签有两个属性，与`property`一致。然后我们在Book的含参构造器中写入如下输出语句

```java
System.out.println("有参数构造执行了.....");
```

#### 写入测试类

接着我们在`TestBook`中写入测试类，原理与上文一致，读者自行编写测试类即可，测试结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2014.png)

📌我们在`xml`中构造`bean`标签中`constructor-arg`的时候，它还有一个属性叫作`index`，读者可自行摸索并写出测试方法，测试结果与上图一致。

接下来我们要对一个特殊值：null进行处理。首先我们在`Book`中新建变量`others`使其私有化，属性为`String`，为它生成`set`方法以及在`Book`内重新生成`toString()`。接着我们在`xml`文件中手写`property`，内容见下

```xml
<property name="others"><null/></property>
```

这一方面很好的处理`value`值为`null`的情况。

有时候我们会在`xml`文件中写入特殊符号，比如`<`和`>`，此时我们就需要特殊转义符号来进行处理。

```xml
<property name="others" value="&lt;"></property>
```

### 转义字符

最后一种情况，有时候我们想在`xml`文件中注入纯文本，此时我们需要用到`CDATA节`，具体使用方法见下

```xml
<property name="others">
            <value><![CDATA[a < b]]></value>
        </property>
```

要想为对象类型属性赋值，我们首先得做一下准备工作。首先我们要在`com.atguigu.spring6.iocxml`包下新建一个`package`名为`ditest`，分别新建类名为`Dept`和`Emp`两个文件，分别写入如下内容

```java
package com.atguigu.spring6.iocxml.ditest;

public class Dept {
    private String dname;
    public void info(){
        System.out.println("部门名称:" + dname);
    }
}
```

```java
package com.atguigu.spring6.iocxml.ditest;

public class Emp {
//    员工属于某个部门
    private Dept dept;
    private String ename;
    private Integer age;
    public void work(){
        System.out.println("emp is working....." + age);
    }
}
```

#### 注入属性

接下来我们分别在两个类中注入`get`和`set`方法，并且在`Emp`中将`work`方法改写为

```java
public void work(){
        System.out.println("emp is working....." + age);
        dept.info();
    }
```

接着我们新建一个`xml`名为`bean-ditest`，在`xml`中，我们先对`Dept`进行普通的属性注入，`id`值均为类名小写，`property`值见下

```xml
<property name="dname" value="安保部"></property>
```

和上述操作一样，我们同样为`Emp`注入相关属性

```xml
<property name="ename" value="lucy"></property>
<property name="age" value="18"></property>
```

接下来我们要注入对象类型的属性，写法如下

```xml
<property name="dept" ref="dept"></property>
```

`ref`代表引入的意思，表示引入我们的对象，这个对象与`Dept`进行属性注入的时候的`id`值拥有同等效力。我们在`ditest`下新建一个测试类名为`TestEmp`，读者对`Emp`自行编写测试方法并且调用`work`方法，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2015.png)

接下来我们使用内部`bean`的方式进行注入，首先编写我们的`xml`文件，内容如下

```xml
<!--第二种方式:内部bean做注入-->
    <bean id="dept2" class="com.atguigu.spring6.iocxml.ditest.Dept">
        <property name="dname" value="财务部"></property>
    </bean>
    <bean id="emp2" class="com.atguigu.spring6.iocxml.ditest.Emp">
        <!--普通属性注入-->
        <property name="ename" value="mary"></property>
        <property name="age" value="20"></property>
<!--使用内部bean的方式进行注入-->
        <property name="dept">
            <bean id="dept2" class="com.atguigu.spring6.iocxml.ditest.Dept">
                <property name="dname" value="财务部"></property>
            </bean>
        </property>
    </bean>
```

在`xml`中我们可以看到，我们使用内部`bean`进行了注入，具体操作是在`property`中注入`bean`，这也是为什么叫内部`bean`的原因，换句话说，现在我们是在`emp2`中注入内部`bean`，相比以前单独的两个模块，能更直观、方便的看到注入关系。最后读者对`Emp`自行编写测试方法并且调用`work`方法，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2016.png)

### 级联赋值

接下来我们要介绍第三种方法：级联赋值。首先我们对xml文件进行修改，内容见下

```xml
<!--第三种方式:级联赋值-->
    <bean id="dept3" class="com.atguigu.spring6.iocxml.ditest.Dept">
        <property name="dname" value="技术研发部"></property>
    </bean>
    <bean id="emp3" class="com.atguigu.spring6.iocxml.ditest.Emp">
        <property name="ename" value="tom"></property>
        <property name="age" value="30"></property>
        <property name="dept" ref="dept3"></property>
        <property name="dept.dname" value="测试部"></property>
    </bean>
```

我们重点可以放在最后一行代码上，我们通过类调属性的方式对`dname`的值进行修改，根据就近原则，控制台打印输出的结果也应该是跟测试部有关，然后读者对`Emp`自行编写测试方法并且调用`work`方法，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2017.png)

#### 注入属性

接着我们尝试着去注入数组类型的相关属性，首先我们新建一个`xml`名为`bean-diarray`，相关配置见下

```xml
<!--注入数组类型的属性-->
    <bean id="dept" class="com.atguigu.spring6.iocxml.ditest.Dept">
        <property name="dname" value="技术部"></property>
    </bean>
    <bean id="emp" class="com.atguigu.spring6.iocxml.ditest.Emp">
<!--普通属性-->
        <property name="ename" value="lucy"></property>
        <property name="age" value="20"></property>
<!--注入对象类型的属性-->
        <property name="dept" ref="dept"></property>
<!--注入数组类型的属性-->
        <property name="loves">
            <array>
                <value>Java</value>
                <value>Vue</value>
                <value>MySQL</value>
            </array>
        </property>
    </bean>
```

我们重点关注`<array>`相关标签的代码，因为我们考虑到人的爱好不止一个，所以`Spring6`为我们提供了一个内置的`array`标签，在标签内我们可以写入多个值。接着我们在`Emp`中对`work`方法进行改写，内容如下

```java
public void work(){
        System.out.println("emp is working....." + age);
        dept.info();
        System.out.println(Arrays.toString(loves));
    }
```

最后读者对`Emp`自行编写测试方法并且调用`work`方法，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2018.png)

### list集合注入

下面我们介绍一下`list`集合的注入，首先我们在`Dept`中写入一个集合，并且对`info`进行改写，内容如下

```java
private List<Emp> empList;

public void info(){
        System.out.println("部门名称:" + dname);
        for (Emp emp:empList){
            System.out.println(emp.getEname());
        }
    }
```

#### XML文件配置

接着我们新建`xml`名为`bean-dilist`，内容如下

```xml
<bean id="empone" class="com.atguigu.spring6.iocxml.ditest.Emp">
        <property name="ename" value="lucy"></property>
        <property name="age" value="19"></property>
    </bean>
    <bean id="emptwo" class="com.atguigu.spring6.iocxml.ditest.Emp">
        <property name="ename" value="mary"></property>
        <property name="age" value="30"></property>
    </bean>
    <bean id="dept" class="com.atguigu.spring6.iocxml.ditest.Dept">
    <property name="dname" value="技术部"></property>
    <property name="empList">
        <list>
            <ref bean="empone"></ref>
            <ref bean="emptwo"></ref>
        </list>
    </property>
</bean>
```

我们重点关注`<list>`，在`list`集合中，我们引入相关`ref`，表示对属性的注入。最后读者对`Dept`自行编写测试方法并且调用`info`方法，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2019.png)

### Map集合注入

现在我们试着进行`map`集合的注入，步骤如下。首先我们在`com.atguigu.spring6.iocxml`下新建一个`package`名为`dimap`，在`dimap`中新建两个文件，分别为`Student`和`Teacher`，分别创建如下属性

```java
public class Student {
    private Map<String,Teacher> teacherMap;
    private String sid ;
    private String sname ;
......
}
```

```java
public class Teacher {
    private String teacherId;
    private String teacherName;
......
}
```

我们分别对这两个类注入`set`、`get`、`toString`方法，接下来我们编写`xml`文件，名为`bean-dimap`，在`xml`文件中，我们主要有以下三个步骤

- 创建两个对象
- 注入普通类型的属性
- 在学生的`bean`中注入`map`集合类型的属性

前两个读者自行理解并对`xml`进行相关操作，我们重点讲解第三个如何实现。我们对`teacherMap`这个`map`进行如下操作

```xml
<property name="teacherMap">
            <map>
                <entry>
                    <key>
                        <value>10010</value>
                    </key>
                    <ref bean="teacher"></ref>
                </entry>
            </map>
        </property>
```

在上方代码块中，我们通过引入`entry`标签对`map`进行操作，同样是通过`value`以及`ref`属性对`map`注入相关值，由于我们的`map`是写入`Teacher`类中，所以我们`ref`引入的值必须是`teacher`。

接着我们在`Student`类中对`run`方法进行改写，内容如下

```java
public void run(){
        System.out.println("学生的编号:"+sid+"学生的名称:"+sname);
        System.out.println(teacherMap);
    }
```

读者可自行在`xml`中写入两个`map`注入属性，并在`dimap`中新建测试类`TestStu`，读者自行完成即可。运行结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2020.png)

#### 引用集合类型的Bean

接下来我们尝试着引用集合类型的`bean`，首先我们在`dimap`中新建类为`Lesson`，在类中我们写入一个属性名为`lessonName`，`String`类型且私有化，并且构造出它的`get`、`set`、`toString`方法。

接下来我们新建一个`xml`名为`bean-diref`，我们要完成的步骤如下

- 创建三个对象
- 注入普通类型属性
- 使用`util:`类型 定义
- 在学生`bean`里面引入`util:`类型定义bean,完成`list map`类型属性注入。然后我们修改头文件，修改内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/util
       http://www.springframework.org/schema/beans/spring-util.xsd
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
```

#### 注入属性

接下来我们就可以引入`util`标签进行引用集合类型的注入。首先我们将`id`为`student`的`property`属性补充完整，然后引入`util`标签，内容见下

```xml
<util:list id="lessonList">
<ref bean="lessonone"></ref>
<ref bean="lessontwo"></ref>
    </util:list>
    <util:map id="teacherMap">
        <entry>
            <key>
                <value>10010</value>
            </key>
            <ref bean="teacherone"></ref>
        </entry>
        <entry>
            <key>
                <value>10086</value>
            </key>
            <ref bean="teachertwo"></ref>
        </entry>
    </util:map>
```

按照上述步骤，读者可自行编写测试类，不出意外会报错（故意留的😋😋），我们需要在`xml`修改相关配置，改动地方见下图

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2021.png)

运行成功截图见下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2022.png)

### p标签命名注入

接下来我们介绍`p标签命名注入`，首先我们在`beans`头标签中写入

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

然后我们写入如下标签

```xml
<!--p命名空间注入-->
    <bean id="studentp" class="com.atguigu.spring6.iocxml.dimap.Student"
    p:sid="100" p:sname="mary" p:lessonList-ref="lessonList" p:teacherMap-ref="teacherMap">
    </bean>
```

#### 引入Maven依赖

我们通过`引入外部属性文件`的方式对数据进行注入处理，首先我们在`pom.xml`中引入相关依赖

```xml
<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.30</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.31</version>
    </dependency>
```

接着我们在`resources`下新建一个`properties`名为`jdbc`，写入内容如下

```xml
jdbc.user=root
jdbc.password=20020930pch
jdbc.url=jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
jdbc.driver=com.mysql.cj.jdbc.Driver
```

📌数据库中的`user`和`password`都需要根据读者数据库的用户名和密码来决定，本文只是作者的数据库账户名和密码

📌数据库使用的是8.0及以上的版本

#### 配置XML

我们新建一个`xml`文件名为`bean-jdbc`，并且使用`context`命名空间引入，内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
```

接下来我们在`com.atguigu.spring6.iocxml`中新建一个`package`名为`jdbc`，在里面新建一个测试类名为`TestJdbc`，内容如下

```java
package com.atguigu.spring6.iocxml.jdbc;

import com.alibaba.druid.pool.DruidDataSource;
import org.junit.jupiter.api.Test;

public class TestJdbc {
    @Test
    public void demo1(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUrl("jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("20020930pch");
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
    }
}
```

#### 引入文件属性

然后我们完成数据库相关信息的注入，使用`$`引用`properties`文件中的相关属性

```xml
<!--完成数据库信息注入-->
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
<property name="url" value="${jdbc.url}"></property>
<property name="username" value="${jdbc.user}"></property>
<property name="password" value="${jdbc.password}"></property>
<property name="driverClassName" value="${jdbc.driver}"></property>
    </bean>
```

#### 测试

最后我们写入一个测试类，方法如下

```java
@Test
    public void demo2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean-jdbc.xml");
        DruidDataSource dataSource = context.getBean(DruidDataSource.class);
        System.out.println(dataSource.getUrl());
    }
```

### Bean的作用域

接下来我们测试`bean`的作用域。我们先在`com.atguigu.spring6.iocxml`中新建一个`package`名为`scope`，在包下我们新建`Orders`测试类，然后在`resources`中新建`xml`进行配置，配置内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--通过scope属性配置单实例/多实例-->
<bean id="orders" class="com.atguigu.spring6.iocxml.scope.Orders" scope="singleton">

</bean>
</beans>
```

在`xml`文件中我们可以看到在`bean`标签内多了一个属性叫作`scope`，读者在这里可以把它理解为作用域标签，`Spring6`在默认情况下是单实例，如果想设置多实例，在`scope`里面修改即可。最后读者自行编写测试类进行测试，并调用`sout`方法输出`orders`。

### Bean的生命周期

接下来我们介绍一下`bean`的生命周期。首先我们在`com.atguigu.spring6.iocxml`中新建一个`package`名为`life`，在`life`中新建一个`User`类，内容如下

```java
package com.atguigu.spring6.iocxml.life;

public class User {
    private String name ;
//    无参数的构造
    public User(){
        System.out.println("1 bean对象创建,调用无参构造");
    }
//  初始化的方法
    public void initMethod(){
        System.out.println("4 bean对象初始化 调用指定的初始化的方法");
    }
//  销毁的方法
    public void destoryMethod(){
        System.out.println("7 bean对象销毁 调用指定的销毁的方法");
    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        System.out.println("2 给bean对象设置属性值");
        this.name = name;
    }
}
```

### 配置XML

接着我们去`xml`文件中进行配置，新建一个`xml`名为`bean-life`，在文件中我们写入如下内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.atguigu.spring6.iocxml.life.User"
          scope="singleton" init-method="initMethod" destroy-method="destoryMethod">
        <property name="name" value="lucy"></property></bean>
</beans>
```

最终我们在`life`下新建一个测试类名为`TestUser`，用来测试`bean`的生命周期，内容见下图

```java
package com.atguigu.spring6.iocxml.life;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUser {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean-life.xml");
        User user = context.getBean("user", User.class);
        System.out.println("6 bean对象创建完成了,可以使用了");
        System.out.println(user);
//        context.close();    // 销毁
    }
}
```

测试完毕之后我们写入如下方法进行测试，目的是为了调用`context.close();`

```java
package com.atguigu.spring6.iocxml.life;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUser {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean-life.xml");
        User user = context.getBean("user", User.class);
        System.out.println("6 bean对象创建完成了,可以使用了");
        System.out.println(user);
        context.close();    // 销毁
    }
}
```

如此下来，`bean`的基本生命周期就算走了一遍，读者可自行阅读代码加深印象。

细心的读者可以发现，生命周期的结果显示并不完整，接下来我们把完整的生命周期展示一遍。首先我们在`life`下新建一个类名为`MyBeanPost`，我们在这个类中接入`BeanPostProcessor`接口，在接口中我们能看到两个方法，我们将这两个方法在`MyBeanPost`中进行重写，声明为`public`，并且分别加上`bean`生命周期中的3和5，最终进行测试，结果如下

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2023.png)

接下来我们基于`XML`管理`Bean-FactoryBean`，首先我们新建一个`package`名为`factorybean`，然后我们分别创建两个对象，名为`FactoryBean`和`User`（创建`User`是因为有泛型），我们在`FactoryBean`中写入如下内容

```java
package com.atguigu.spring6.iocxml.factorybean;

import org.springframework.beans.factory.FactoryBean;

public class MyFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        return new User();
    }

    @Override
    public Class<?> getObjectType() {
        return User.class;
    }
}
```

接着我们新建一个`XML`名为`bean-factorybean`，我们在里面注入`id`为`user`的属性，并编写测试类进行测试。

📌在编写测试类的时候，读者需要注意使用强转，因为我们的方法在`FactoryBean`是通过`User`创建的。

接下来我们尝试基于`XML`管理`Bean`实现自动装配。首先我们在`com.atguigu.spring6.iocxml`下新建一个`package`名为`auto`，在其包下新建三个`package`分别名为`controller`、`dao`、`service`。然后我们在`dao`层下新建一个`interface`名为`UserDao`，配置内容如下

```java
package com.atguigu.spring6.iocxml.auto.dao;

public interface UserDao {
    public void addUserDao();
}
```

接着我们创建一个实现类名为`UserDaoImpl`并且继承`UserDao`，实现方法的重写，内容如下

```java
@Override
    public void addUserDao() {
        System.out.println("userDao方法执行了...");
    }
```

以此类推，请读者自行在`service`层下写入相关信息。接着在`UserController`中写入如下信息

```java
package com.atguigu.spring6.iocxml.auto.controller;

import com.atguigu.spring6.iocxml.auto.service.UserService;
import com.atguigu.spring6.iocxml.auto.service.UserServiceImpl;

public class UserController {
    private UserService userService;

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    public void addUser(){
        System.out.println("controller方法执行了...");
//        调用service里面的方法
        userService.addUserService();
    }
}
```

完成上述步骤之后，我们在`resources`中写入`XML`名为`bean-auto`，读者自行对三个`id`属性进行相关配置，其中在`userController`和`userService`中写入如下标签以实现根据类型实现自动装配

```xml
autowire="byType"
```

最终读者自行编写与`userController`相关的测试类并进行测试。

以上方法是根据类型实现自动装配，在`Spring6`中，我们还可以根据名称实现自动装配，即将上述标签替换成

```xml
autowire="byName"
```

运行结果与上述是一致的，区别在于：当我们使用`byName`注入属性的时候，我们id的名字必须与测试类中`getBean`中的名字一致，否则报错显示`userDao`为`null`，而类型注入可以忽略这一点。

### 基于注解进行Bean管理

接下来我们基于注解进行`Bean`管理。首先我们新建一个`module`名为是`spring6-ioc-annotation`，这个`module`继承于父工程，因此我们不需要引入依赖。接下来我们在resources中写入`bean.xml`开启组件扫描

```xml
<!--启动组件扫描-->
    <context:component-scan base-package="com.coder"></context:component-scan>
```

开启组件扫描之后我们将`log4j2.xml`复制到本`module`中方便后期调试。

接下来我们了解两个较为特殊的情况

- 指定要排除的组件

```xml
<context:component-scan base-package="com.coder.spring6">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--<context:exclude-filter type="assignable" expression="com.atguigu.spring6.controller.UserController"/>-->
</context:component-scan>
```

`context:exclude-filter`即为指定排除规则，`type`即为设置排除或包含的依据

- `type="annotation"`根据注解排除，`expression`中设置要排除的注解的全类名
- `type="assignable"`根据类型排除，`expression`中设置要排除的类型的全类名
- **仅扫描指定组件**

```xml
<context:component-scan base-package="com.coder" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	<!--<context:include-filter type="assignable" expression="com.atguigu.spring6.controller.UserController"/>-->
</context:component-scan>
```

`context:include-filter`标签：指定在原有扫描规则的基础上追加的规则。`use-default-filters`属性：取值`false`表示关闭默认扫描规则，此时必须设置`use-default-filters="false"`，因为默认规则即扫描指定包下所有类

然后我们需要使用注解去定义`Bean`，具体操作见下

### 写入测试

我们首先新建一个`package`名为`com.coder.spring6.bean`，在包下新建一个类名为`User`，我们在`User`中写入如下内容

```java
package com.coder.spring6.bean;

import org.springframework.stereotype.Component;

@Component(value = "user")
public class User {

}
```

`@Component(value = "user")`这一步操作相当于`<bean id = "user" class = "com.coder.spring6.bean.User">`，我们用一行注解就可以轻松解决`XML`配置的问题。里面的value的值不写也没有关系，如果不写，`Spring6`默认就是类名的全小写。

下面这是官方提供的文档，我们可以通过这四种注解来解决`XML`配置问题

| 注解        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| @Component  | 该注解用于描述 Spring 中的 Bean，它是一个泛化的概念，仅仅表示容器中的一个组件（Bean），并且可以作用在应用的任何层次，例如 Service 层、Dao 层等。  使用时只需将该注解标注在相应类上即可。 |
| @Repository | 该注解用于将数据访问层（Dao 层）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
| @Service    | 该注解通常作用在业务层（Service 层），用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
| @Controller | 该注解通常作用在控制层（如SpringMVC 的 Controller），用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |

## 实验一：通过`@Autowired`注入

`@Autowired`是根据类型实现自动装配，读者根据下图自行配置相关`package`以及实现类

![Untitled](https://typora-1312608844.cos.ap-nanjing.myqcloud.com/typora/Untitled%2024.png)

我们分别在`service`和`dao`两层目录下的接口写入如下内容

```java
public void add();
```

并且分别在它们的实现类里面进行重写，并输出对应的语句。

📌记得在`service`和`dao`分别添加所对应的注解，相关注解可由上文得到。

最终我们写入测试类并且通过反射调用`UserController`的类，本测试类先通过调用`service`，然后由`service`去调用`dao`层最终完成输出结果

### 根据`set`注入

我们在`UserController`中可以使用`set`进行注入，步骤如下：我们先利用`IDEA`生成`userService`的`setter`，然后在`set`方法上写入注解`@Autowired`，这样就是一个`set`方法的注入。同理，读者可在

`service`中写入同样的操作，最终我们可以在`TestUser`中进行测试，结果与上文一致。

### 构造方法注入

接下来我们可以通过构建构造器来对属性进行注入，步骤如下：我们先利用`IDEA`生成`userService`的`constructor`，然后在`constructor`方法上写入注解`@Autowired`，这样就是一个`constructor`方法的注入。同理，读者可在`service`中写入同样的操作，最终我们可以在`TestUser`中进行测试，结果与上文一致。

### 形参注入

我们在构造方法注入的基础上，去掉方法头部的`@Autowired`，在构造器里面注入`@Autowired`，示例如下

```java
public UserController(@Autowired UserService userService) {
        this.userService = userService;
    }
```

读者可对另一个自行配置，最终我们可以在`TestUser`中进行测试，结果与上文一致。

### 只有一个有参数构造，无注解

🤣🤣只写参数构造器，不加任何注解。同理，读者可在`service`中写入同样的操作，最终我们可以在`TestUser`中进行测试，结果与上文一致。

接下来我们在`dao`中写入一个类名为`UserRedisDaoImpl`，我们在这个方法里面实现`UserDao`的接口，重写`run()`方法，这时候我们进行测试的时候，会报一个如下的错误

```java
Exception in thread "main" java.lang.NullPointerException: 
Cannot invoke "com.coder.spring6.autowired.dao.UserDao.add()" 
because "this.userDao" is null
```

### `@Autowired`注解和`@Qualifier`注解联合

#### 解决办法：引入新注解，根据名称来注入

我们在`UserServiceImpl`里面的变量上加入如下注解`@Qualifier`，在`value`中传入`userRedisDaoImpl`这个实现类，

```java
@Autowired
@Qualifier(value = "userRedisDaoImpl")
```

然后我们可以在`TestUser`中进行测试，结果与上文一致。

### `@Resource`注入

`@Resource`注解也可以完成属性注入。那它和`@Autowired`注解有什么区别？

- `@Autowired`注解是`Spring`框架自己的，`@Resource`注解需要我们自己去引入`maven`坐标。
- `@Resource`注解默认根据名称装配`byName`，未指定`name`时，使用属性名作为`name`。通过`name`找不到的话会自动启动通过类型`byType`装配。
- `@Autowired`注解默认根据类型装配`byType`，如果想根据名称装配，需要配合`@Qualifier`注解一起用
- `@Resource`注解用在属性上、`setter`方法上
- `@Autowired`注解用在属性上、`setter`方法上、构造方法上、构造方法参数上。

接下来我们引入`@Resource`的坐标实现`@Resource`注入

```xml
<dependency>
            <groupId>jakarta.annotation</groupId>
            <artifactId>jakarta.annotation-api</artifactId>
            <version>2.1.1</version>
        </dependency>
```

接着我们在`com.coder.spring6`下新建一个`package`名为`resource`，我们将`autowried`下的内容`cv`到`resource`下，并且在`UserController`中写入如下注解

```java
//    根据名称进行注入
    @Resource(name = "myUserService")
    private UserService userService;
```

在`UserServiceImpl`中我们也能得到相应实现

```java
@Service(value = "myUserService")
```

### 不指定名称，根据属性名称指定准入

我们可以在`UserServiceImpl`写入如下注解

```java
//    不指定名称,根据属性名称指定准入
    @Resource
private UserDao userDao;
```

这个时候我们启动测试类进行测试，会发现`Spring6`报如下错误

```java
Caused by: org.springframework.context.annotation.ConflictingBeanDefinitionException:
 Annotation-specified bean name 'userController' for bean class
 [com.coder.spring6.resource.controller.UserController] conflicts with existing, 
non-compatible bean definition of same name and class 
[com.coder.spring6.autowired.controller.UserController]
```

这句话的意思就是，`Spring6`中扫描的还是我们之前`autowried`中的实现类，而并非是`resource`中的实现类，为了解决这个问题，我们需要对注解做一些改动

```java
@Controller("myUserController")
@Repository("myUserDao")
@Repository("myUserRedisDao")
@Service("myUserService")
```

这四个注解分别对应四个实现类，请读者根据前置知识自行分配并再次启动测试类，测试结果与上文一致。由于本篇难度过大，作者提供源码供读者参考。

### 源码一览

```java
package com.coder.spring6.resource.controller;

import com.coder.spring6.resource.service.UserService;
import jakarta.annotation.Resource;
import org.springframework.stereotype.Controller;

import java.time.temporal.ValueRange;

@Controller("myUserController")
public class UserController {
//    根据名称进行注入
    @Resource(name = "myUserService")
    private UserService userService;

    public void add(){
        System.out.println("controller.......");
        userService.add();
    }

}
```

```java
package com.coder.spring6.resource.dao;

public interface UserDao {

    public void add();

}
```

```java
package com.coder.spring6.resource.dao;

import jakarta.annotation.Resource;
import org.springframework.stereotype.Repository;

@Repository("myUserDao")
public class UserDaoImpl implements UserDao {

    @Override
    public void add() {
        System.out.println("dao......");
    }
}
```

```java
package com.coder.spring6.resource.dao;

import org.springframework.stereotype.Repository;

@Repository("myUserRedisDao")
public class UserRedisDaoImpl implements UserDao {

    @Override
    public void add() {
        System.out.println("dao redis.....");
    }
}
```

```java
package com.coder.spring6.resource.service;

import com.coder.spring6.resource.dao.UserDao;
import jakarta.annotation.Resource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service(value = "myUserService")
public class UserServiceImpl implements UserService {
//    不指定名称,根据属性名称指定准入
    @Resource
private UserDao myUserDao;
    @Override
    public void add() {
        System.out.println("service.....");
        myUserDao.add();
    }
}
```

接下来我们还可以通过类型注入，即无须声明属性即可进行注入

```java
@Resource
    private UserService userService;
```

### `Spring`全注解开发

全注解开发就是不再使用`spring`配置文件了，写一个配置类来代替配置文件。

我们在`com.coder.spring6`中写入测试类名为`TestUserControllerAnno`，在测试类中我们要有所改动，内容如下

```java
package com.coder.spring6.resource;

import com.coder.spring6.config.SpringConfig;
import com.coder.spring6.resource.controller.UserController;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUserControllerAnno {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        UserController controller = context.getBean(UserController.class);
        controller.add();
    }
}
```

我们新建一个`module`名为`spring6-reflect`，继承于`spring6`父工程，然后新建一个`package`名为`com.coder.reflect`，我们在包下新建一个类名为`Car`，并写入如下属性

```java
private String name ;
private int age ;
private String color ;
```

然后我们生成它的`getter`和`setter`以及两个构造器，并写入如下私有方法

```java
private void run(){
        System.out.println("私有方法-run......");
    }
```

接下来我们在同样的包下新建一个测试类名为`TestUser`，在开始测试之前，我们先回顾一下`Java`中的反射机制。我们要实现`Car`对象的实例化，首先得获取到这个类名的`class`。在`Java`中我们提供三种方式来获取类名的`class`

- 类名.class

```java
Class<Car> clazz1 = Car.class;
```

- 对象.getClass()

```java
Class clazz2 = new Car().getClass();
```

- Class.forName("全路径")

```java
Class clazz3 = Class.forName("com.coder.reflect.Car");
```

接下来我们进行对象的实例化

```java
Object o = (Car)clazz3.getDeclaredConstructor().newInstance();
        System.out.println(o);
```

我们尝试着去获取构造方法，首先我们获取所有的构造，可以通过如下方法实现

```java
public void test02() throws Exception {
        Class clazz = Car.class;
    Constructor[] constructors = clazz.getConstructors();
    for (Constructor c : constructors){
        System.out.println("方法名称:"+c.getName()+"   参数个数:"+c.getParameterCount());
     }
```

在这个测试方法中，我们通过`getConstructors`去获取所有`public`的构造方法，并且通过`for`循环打印输出方法名称以及所获取到的参数的个数

当我们的构造方法为`private`的时候，上述的方法是行不通的，这个时候我们需要重新编写测试方法进行测试，内容如下

```java
public void test02() throws Exception {
        Class clazz = Car.class;
    Constructor[] constructors = clazz.getDeclaredConstructors();
    for (Constructor c : constructors){
        System.out.println("方法名称:"+c.getName()+"   参数个数:"+c.getParameterCount());
      }
```

在这个测试方法中，我们通过`getDeclaredConstructors`去获取所有`private`的构造方法，并且通过`for`循环打印输出方法名称以及所获取到的参数的个数。

#### 创建对象

接下来我们指定有参数的构造去创建对象，方法如下

```java
Constructor c1 = clazz.getConstructor(String.class, int.class, String.class);
    Car car1 = (Car)c1.newInstance("夏利", 10, "白色");
    System.out.println(car1);
```

通过这样的方法我们完成了参数的实例化，最终完成了对象的创建。

当我们的构造是一个`private`的时候，我们可以通过如下方法完成参数的实例化

```java
Constructor c2 = clazz.getDeclaredConstructor(String.class, int.class, String.class);
    c2.setAccessible(true);  // 设置允许访问,访问私有的构造
    Car car2 = (Car)c2.newInstance("长安", 3, "粉色");
    System.out.println(car2);
```

#### 获取属性

接下来我们尝试着去获取属性。首先我们得拿到这个对象的`Class`

```java
Class  clazz = Car.class;
Car car = (Car)clazz.getDeclaredConstructor().newInstance();
```

然后我们获取所有的`public`属性

```java
Field[] fields = clazz.getFields();
```

接下来我们获取所有属性(包含私有属性)

```java
Field[] fields1 = clazz.getDeclaredFields();
        for (Field field : fields1){
            if (field.getName().equals("name")){
//          允许进行访问
                field.setAccessible(true);
                field.set(car,"奔驰");
            }
```

最终打印输出结果

```java
System.out.println(field.getName());
System.out.println(car);
```

接下来我们通过获取方法来实现反射操作。我们尝试着去获取属性。首先我们得拿到这个对象的`Class`

```java
Car car = new Car("马自达",5,"紫色");
Class clazz = car.getClass();
```

接下来我们先介绍`public`方法

```java
Method[] methods = clazz.getMethods();
```

这行代码使用 `Java` 的反射机制获取了一个类（或对象）的所有公共方法，并将它们存储在一个 **`Method`** 数组中

```java
for (Method m1 :methods){
            if (m1.getName().equals("toString")){
                String invoke = (String)m1.invoke(car);
                System.out.println("toString执行了"+invoke);
            }
        }
```

这行代码是使用 `for-each` 循环遍历先前获取类中所有公共方法的 `Method`数组 `methods`，对于每个 `Method`对象 `m1`执行以下操作

1. 检查该 `Method`对象的名称是否等于 `toString`，如果不等于则跳过后续操作。
2. 通过 `invoke()`方法调用该 `Method`对象表示的方法，并传入 `car`对象作为执行该方法的目标对象。
3. 将返回值强制转换为 `String`类型，并存储在 `invoke`变量中。
4. 打印包含 `toString执行了` 开头及 `invoke`中文本的语句到控制台上。

接下来我们实现`private`构造方法

```java
Car car = new Car("马自达", 5, "紫色");
Class clazz = car.getClass();
```

代码中首先创建了一个`Car`对象，然后通过反射获取该对象的`Class`对象

```java
Method[] methodsAll = clazz.getDeclaredMethods();
        for (Method m : methodsAll) {
            if (m.getName().equals("run")) {
```

接着遍历该`Class`对象中的所有方法，找到名称为`run`的方法

```java
m.setAccessible(true);
```

由于是私有方法，我们通过`setAccessible`可将其设置为可访问状态

```java
m.invoke(car);
```

最后使用**`invoke()`**方法来实现动态调用**`run()`**方法。使用`invoke()`方法时，需要传入要调用的对象和方法所需要的参数。因为**`run()`**方法没有参数，因此可以直接调用。**`invoke()`**方法会调用对象的方法，并返回方法执行结果。

代码一览

```java
@Test
    public void test05() throws Exception {
        Car car = new Car("马自达", 5, "紫色");
        Class clazz = car.getClass();
        Method[] methodsAll = clazz.getDeclaredMethods();
        for (Method m : methodsAll) {
            if (m.getName().equals("run")) {
                m.setAccessible(true);
                m.invoke(car);
            }
        }
    }
```

## 实现`Spring`中的`IOC`

首先我们在`coder`下新建几个`package`分别名为`anno`、`bean`、`dao`、`service`，首先我们在`anno`下新建两个接口分别名为`Bean`和`di`。在`Bean`中我们可以通过注解的方式创建对象，注解如下

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
```

- `@Target(ElementType.TYPE)` 表示该注解只能用于修饰类、接口（包括注解类型）或 `enum`声明。
- `@Retention(RetentionPolicy.RUNTIME)`表示该注解在运行时保留，可以通过反射读取。

接下来我们在`bean`下新建一个接口名为`ApplicationContext`，然后写入如下方法

```java
Object getBean(Class clazz);
```

接着我们再写入一个实现类名为`AnnotationApplicationContext`并且继承该接口，实现方法的重写

然后我们在`dao`下新建一个`package`名为`impl`，在`impl`下新建一个实现类名为`UserServiceImpl`，并通过注解去注入属性，内容如下

```java
@Di
private UserDao userDao;
```

### 路径转义

完成上述步骤之后，我们尝试着对包路径进行转义，换句话说，我们采用`/`而并非`.`

```java
String PackagePath = basePackage.replaceAll("\\.", "\\\\");
```

首先，将参数 **`basePackage`**中的点号**`.`**替换为反斜线**`\`**的形式，得到一个文件路径。这是因为`Java`类在文件系统中通常以文件的形式存储，而包名实际上就是文件路径的一部分。此时，变量**`PackagePath`**中的值就是转换后的文件路径。

```java
Enumeration<URL> urls = Thread.currentThread().getContextClassLoader().getResources(PackagePath);
```

然后，通过`Java`类加载器获取该包路径下的所有资源文件的`URL`，并将它们存储在一个枚举器中。枚举器是`Java`中的一种迭代器，它可以便利一个枚举集合中的元素。

```java
while (urls.hasMoreElements()){
                URL url = urls.nextElement();
                String file = URLDecoder.decode(url.getFile(), "utf-8");
                System.out.println(file);
            }
```

接下来我们通过枚举器逐个迭代所有资源文件的`URL`，并对每个`URL`进行处理。在`URL`对象中获取该资源文件的文件路径，并将其解码为字符串。同时，由于`URL`对象中的文件路径中可能包含某些特殊字符，例如空格或非`ASCII`字符，因此需要使用`URLDecoder`对其进行解码。

```java
System.out.println(file);
```

最后，将处理好的文件路径输出到控制台。

### 手写`IOC`-实现`Bean`创建

我们先在`ApplicationContext`中创建一个方法便于后续使用

```java
Object getBean(Class clazz);
```

然后在`UserService`中创建一个方法便于后续使用

```java
void add();
```

读者分别对这两个方法在所对应的实现类里面进行重写即可，内容如下

```java
@Override
    public void add() {
        System.out.println("service.......");

    }

@Override
    public Object getBean(Class clazz) {
        return beanFactory.get(clazz);
    }
```

```java
private void loadBean(File file) throws Exception {
        if (file.isDirectory()) {
```

这行代码使用了`Java`的`if`条件语句，并检查了给定的`file`对象是否表示一个目录（即判断给定的`file`是否为目录）。

```java
File[] childrenFiles = file.listFiles();
```

如果`file`对象是一个目录，这行代码将获取该目录下的所有子文件，并将这些子文件存储在类型为`File`的数组`childrenFiles`中。

```java
if (childrenFiles == null || childrenFiles.length == 0) {
    return;
  }
```

这行代码使用`Java`的条件语句，检查`childrenFiles`数组是否为空或是否包含零个元素。如果是这样，那么这个目录中没有任何子文件，因此应立即退出方法，即跳出当前方法并返回到其调用方法中。

```java
for (File child : childrenFiles) {
                if (child.isDirectory()) {
                    loadBean(child);
                }
```

第一行代码使用`Java`的`for-each`循环，遍历子文件数组中的每个子文件。在每次循环中，将当前子文件存储在名为`child`的`File`对象中。第二行代码检查名为`child`的`File`对象是否是一个目录。如果是一个目录，将递归调用`loadBean`方法，以便读取该目录下所有的子文件。这个递归调用是程序查找和加载所有`bean`的关键步骤。

```java
else {
String pathWithClass = child.getAbsolutePath().substring(rootPath.length() - 1);
```

这行代码开始处理子文件不是目录的情况。具体来说，它处理每一个文件并根据它是否包含一个`@class`运行时注释来确定它是否应该被加载到`beanFactory`中。我们获取当前子文件的绝对路径（包括文件名和文件路径），然后从该路径中删除`rootPath`的值，以便只留下当前文件的相对路径（即距离根路径的距离）。这一步骤确保所有的文件导入路径是相对路径，从而简化了文件路径的处理。

```java
if (pathWithClass.contains(".class")) {
```

这个`if`代码块检查子文件是否具有`.class`扩展名，以确保它是一个`Java`类文件。如果不是类文件，那么不会将其加载到`beanFactory`中。

```java
String allName = pathWithClass.replaceAll("\\", ".").replace( ".class", "");
```

这行代码将相对路径中的反斜杠替换为点，并删除`.class`后缀。这个处理步骤使得可以从文件路径中直接构建类名。

```java
Class<?> clazz = Class.forName(allName);
```

这行代码使用`Java`的`Class`类来动态加载名为`allName`的类，并返回它的`Class`对象。

```java
if (!clazz.isInterface()) {
```

这个`if`代码块检查当前加载的`Class`对象是否是接口类型。如果是接口类型，则无法将它实例化，因此不能将它加载到`beanFactory`中。

```java
Bean annotation = clazz.getAnnotation(Bean.class);
```

这行代码检查当前`Class`对象是否具有`@bean`运行时注释。如果是这样，则说明该类应该被实例化为`bean`，并加载到`beanFactory`。

```java
if (annotation != null) {
```

在前面的`if`代码块中，如果当前`Class`对象具有`@bean`运行时注释，则进一步在这个`if`代码块中处理该注释。

```java
Object instance = clazz.getConstructor().newInstance();
```

这行代码使用反射创建一个当前`Class`对象的实例。这个实例将作为`bean`在应用程序中使用。

```java
if (clazz.getInterfaces().length > 0) {
```

这个`if`代码块检查当前加载的`Class`对象是否实现了一个接口。如果实现了接口，则将这个`bean`加载到`beanFactory`中的接口类型，否则加载对应的`Class`类型。

```java
beanFactory.put(clazz.getInterfaces()[0], instance);
```

这个代码行将`bean`加载到`beanFactory`中的接口类型。

```java
} else {
```

如果当前`Class`对象没有实现接口，则将这个`bean`加载到`beanFactory`中与之对应的`Class`类型中。

```java
beanFactory.put(clazz, instance);
```

这个代码行将加载到`beanFactory`中。

### 代码一览

```java
package com.coder.bean;

import com.coder.anno.Bean;
import org.springframework.beans.factory.BeanFactory;

import java.io.File;
import java.io.IOException;
import java.lang.annotation.Annotation;
import java.net.URL;
import java.net.URLDecoder;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.Map;

public class AnnotationApplicationContext implements ApplicationContext {
    private Map<Class, Object> beanFactory = new HashMap<>();
    private static String rootPath;

    @Override
    public Object getBean(Class clazz) {
        return beanFactory.get(clazz);
    }

    public AnnotationApplicationContext(String basePackage) throws Exception {
        try {
            String PackagePath = basePackage.replaceAll("\\.", "\\\\");
            Enumeration<URL> urls = Thread.currentThread().getContextClassLoader().getResources(PackagePath);
            while (urls.hasMoreElements()) {
                URL url = urls.nextElement();
                String filePath = URLDecoder.decode(url.getFile(), "utf-8");
                rootPath = filePath.substring(0, filePath.length() - PackagePath.length());
                loadBean(new File(filePath));
                System.out.println(filePath);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private void loadBean(File file) throws Exception {
        if (file.isDirectory()) {
            File[] childrenFiles = file.listFiles();
            if (childrenFiles == null || childrenFiles.length == 0) {
                return;
            }
            for (File child : childrenFiles) {
                if (child.isDirectory()) {
                    loadBean(child);
                } else {
                    String pathWithClass = child.getAbsolutePath().substring(rootPath.length() - 1);
                    if (pathWithClass.contains(".class")) {
                        String allName = pathWithClass.replaceAll("\\\\", ".").replace(".class", "");
                        Class<?> clazz = Class.forName(allName);
                        if (!clazz.isInterface()) {
                            Bean annotation = clazz.getAnnotation(Bean.class);
                            if (annotation != null) {
                                Object instance = clazz.getConstructor().newInstance();
                                if (clazz.getInterfaces().length > 0) {
                                    beanFactory.put(clazz.getInterfaces()[0], instance);
                                } else {
                                    beanFactory.put(clazz, instance);
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
```