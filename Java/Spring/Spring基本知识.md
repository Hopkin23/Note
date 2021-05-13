# Spring基础知识

## Spring基础五大部分

1. xml配置对象
2. DI依赖注入
3. propority配置数据源
4. 注解开发
5. spring配置Junit测试

## 1 Spring知识概念

### 概念

Spring是分层的Java SE/EE应用full-stack轻量级开源框架，以**IoC**(Inverse Of Control 反转控制)和**AOP**(Aspect Oriented Programming 面向切面编程)为内核。

EJB是Spring前身

### Spring项目配置

* 新建文件夹
* Maven module创建aop
* project structure配置
  * project 环境和路径
  * Facets 添加web并修改路径在main下
* 同上建IoC module

### Spring优势

1. 方便解耦，简化开发
    * 提供IoC容器创建对象进行管理，业务层不再由于创建DAO层对象而产生硬编码造成的过度耦合。

2. AOP编程的支持
    * 面向切面编程思想实现

3. 声明式事务的支持
    * jdbc是编程式事务控制,业务层进行connecttion set connecttions.roback
    * 业务层需要很多方法都需要事务控制，都需要写上面的代码， 声明式事务通过配置的方式一次性配置一片方法自动进行事务控制

4. 方便程序的控制
    * Spring集成Junit，让我们的测试更加方便。**非容器依赖的编程方式**进行几乎所有的测试工作。

5. 方便集成各种优秀的框架
    * Spring自身解决方案：web层-Spring-MVC;DAO层-JEVC模板，spring-data
    * 支持第三方优秀框架
        * web层：Struts、Struts2
        * DAO层：Hibernate、MyBaits
        * Hessian、Quartz

6. 降低JavaEE API 的使用难度
    * Spring对JavaEE API(JDBC,JavaMail,远程调用等)进行了薄薄的封装层，使这些API的使用难度大大降低。
    * 对于重复繁琐的代码要进行封装，Spring替我们进行了相应的封装称为**模板**。

7. Java源码是经典学习范例
    * 对Java设计模式灵活运用

### Spring的体系结构（了解）

![Spring的体系结构](.\img\spring_system.png)

## 2 spring快速入门

### 2.1 Spring程序开发步骤

1. 导入 Spring 开发的基本包坐标
2. 编写 Dao 接口和实现类
3. 创建 Spring 核心配置文件xml
4. 在 Spring 配置文件中配置 UserDaoImpl
5. 使用 Spring 的 API 获得 Bean 实例

### 2.2 导入Spring开发的基本包坐标spring-context

```xml
<properties>
    <spring.version>5.0.5.RELEASE</spring.version>
</properties>
<!--导入spring的context坐标，context依赖core、beans、expression-->
<dependencies> 
    <dependency>  
        <groupId>org.springframework</groupId> 
        <artifactId>spring-context</artifactId> 
        <version>${spring.version}</version>
    </dependency>
</dependencies>

```

### 2.3 编写Dao接口和实现类

```java
public interface UserDao {  
    public void save();
}

public class UserDaoImpl implements UserDao {  
        @Override  
        public void save() {
            System.out.println("UserDao save method running....");
    }
}
```

### 2.4 创建Spring核心配置文件

在类路径下（resources）创建applicationContext.xml配置文件

```java
<?xml version="1.0" encoding="UTF-8" ?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

### 2.5 在Spring配置文件中配置UserDaoImpl

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans 
    xmlns="http://www.springframework.org/schema/beans"              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

   <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
</beans>

```

### 2.6 使用Spring的API获得Bean实例ClassPathXmlApplicationContext

```java
@Test
public void test1(){
    ApplicationContext applicationContext = new  
        ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");             
        userDao.save();
 }
```

## 3. Spring配置文件

### 3.0 Spring配置文件基本内容

1. 基本配置id class
2. 范围配置scope
3. 生命周期配置
4. Bean实例化方式：无参构造，工厂静态，工厂实例方法
5. 依赖注入DI

### 3.1 Bean标签基本配置id class

用于配置对象交由Spring 来创建。
**默认**情况下它调用的是类中的**无参构造函数**，如果没有无参构造函数则不能创建成功。

基本属性：
**id**：Bean实例在Spring容器中的唯一标识
**class**：Bean的**全限定名称**

### 3.2 Bean标签范围配置scope

scope:指对象的作用范围，取值如下：

| 取值范围         | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| singleton        | 默认值，单例的                                               |
| prototype        | 多例的                                                       |
| request          | WEB   项目中，Spring   创建一个   Bean   的对象，将对象存入到   request   域中 |
| session          | WEB   项目中，Spring   创建一个   Bean   的对象，将对象存入到   session   域中 |
| global   session | WEB   项目中，应用在   Portlet   环境，如果没有   Portlet   环境那么globalSession   相当于   session |

1）当scope的取值为singleton时

​    Bean的实例化个数：1个
​    Bean的实例化时机：当**Spring核心文件被加载时**，实例化配置的Bean实例
​    Bean的生命周期：
        对象创建：当应用加载，创建容器时，对象就被创建了
        对象运行：只要容器在，对象一直活着
        对象销毁：当应用卸载，销毁容器时，对象就被销毁了

2）当scope的取值为prototype时

​    Bean的实例化个数：多个
​    Bean的实例化时机：当**调用getBean()方法时**实例化Bean
    Bean的生命周期：
        对象创建：当使用对象时，创建新的对象实例
        对象运行：只要对象在使用中，就一直活着
        对象销毁：当对象长时间不用时，被 Java 的垃圾回收器回收了

### 3.3 Bean生命周期配置

init-method：指定类中的初始化方法名称
destroy-method：指定类中销毁方法名称

### 3.4 Bean实例化三种方式

1） 使用**无参构造方法**实例化
​      它会根据**默认**无参构造方法来创建类对象，如果bean中没有默认无参构造函数，将会创建失败

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
```

2） **工厂静态**方法实例化

​      工厂的静态方法返回Bean实例

```java
public class StaticFactoryBean {
    public static UserDao createUserDao(){    
    return new UserDaoImpl();
    }
}
```

```xml
<bean id="userDao" class="com.itheima.factory.StaticFactoryBean" 
      factory-method="createUserDao" />
```

3） **工厂实例**方法实例化

​      工厂的非静态方法返回Bean实例

```java
public class DynamicFactoryBean {  
    public UserDao createUserDao(){        
        return new UserDaoImpl(); 
    }
}
```

```xml
<bean id="factoryBean" class="com.itheima.factory.DynamicFactoryBean"/>
<bean id="userDao" factory-bean="factoryBean" factory-method="createUserDao"/>
```

### 3.5 Bean的依赖注入入门

①创建 UserService，UserService 内部在调用 UserDao的save() 方法

```java
public class UserServiceImpl implements UserService {
    @Override
    public void save() {
        ApplicationContext applicationContext = new 
        ClassPathXmlApplicationContext("applicationContext.xml");   
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.save();
    }
 }
```

②将 UserServiceImpl 的创建权交给 Spring

```xml
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl"/>
```

③从 Spring 容器中获得 UserService 进行操作

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
UserService userService = (UserService) applicationContext.getBean("userService");
userService.save();
```

### 3.6 Bean的依赖注入概念

依赖注入（Dependency Injection）：它是 Spring 框架核心 IOC 的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了 Spring，但是代码中不可能出现没有依赖的情况。

IOC 解耦只是降低他们的依赖关系，但不会消除。例如：业务层仍会调用持久层的方法。

那这种业务层和持久层的依赖关系，在使用 Spring 之后，就让 Spring 来维护了。

简单的说，就是坐等框架把持久层对象传入业务层，而不用我们自己去获取

### 3.7 Bean的依赖注入方式

①构造方法

​      创建有参构造

```java
public class UserServiceImpl implements UserService {
@Override
public void save() {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");       UserDao userDao = (UserDao) applicationContext.getBean("userDao");    
    userDao.save();
    }
 }
```

​      配置Spring容器调用有参构造时进行注入

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl">      
    <constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

②set方法

​      在UserServiceImpl中添加setUserDao方法

```java
public class UserServiceImpl implements UserService {
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;  
        } 
    @Override    
    public void save() {      
         userDao.save();
    }
}
```

​      配置Spring容器调用set方法进行注入

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"/>
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"/>
</bean>
```

set方法:**P命名空间注入**

​      P命名空间注入本质也是set方法注入，但比起上述的set方法注入更加方便，主要体现在配置文件中，如下：

​      首先，需要引入P命名空间：

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

其次，需要修改注入方式

```xml
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl" 
    p:userDao-ref="userDao"/>
```

### 3.8 Bean的依赖注入的数据类型

上面的操作，都是注入的引用Bean，处了对象的引用可以注入，普通数据类型，集合等都可以在容器中进行注入。

注入数据的三种数据类型
**普通数据类型
引用数据类型
集合数据类型**

其中引用数据类型，此处就不再赘述了，之前的操作都是对UserDao对象的引用进行注入的，下面将以set方法注入为例，演示普通数据类型和集合数据类型的注入。

**Bean的依赖注入的数据类型:**

（1）普通数据类型的注入

```java
public class UserDaoImpl implements UserDao {
private String company;
    private int age;
    public void setCompany(String company) {
        this.company = company;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public void save() {
        System.out.println(company+"==="+age);
        System.out.println("UserDao save method running....");   
    }
}

```

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
    <property name="company" value="传智播客"></property>
    <property name="age" value="15"></property>
</bean>
```

（2）集合数据类型（List`<String>`）的注入

```java
public class UserDaoImpl implements UserDao {
    private List<String> strList;
    public void setStrList(List<String> strList) {
        this.strList = strList;
    }
    public void save() {
        System.out.println(strList);
        System.out.println("UserDao save method running....");
    }
}
```

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
    <property name="strList">
        <list>
            <value>aaa</value>
            <value>bbb</value>
            <value>ccc</value>
        </list>
    </property>
</bean>
```

（3）**集合数据类型（List`<User>`）的注入**

```java
public class UserDaoImpl implements UserDao {
    private List<User> userList;
    public void setUserList(List<User> userList) {
    this.userList = userList;  
 }
public void save() {
    System.out.println(userList);
    System.out.println("UserDao save method running....");
    }
}
```

```xml
<bean id="u1" class="com.itheima.domain.User"/>
<bean id="u2" class="com.itheima.domain.User"/>
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
    <property name="userList">
        <list>
            <bean class="com.itheima.domain.User"/>
            <bean class="com.itheima.domain.User"/>
            <ref bean="u1"/>
            <ref bean="u2"/>       
        </list>
    </property>
</bean>
```

（4）集合数据类型（ Map<String,User> ）的注入

```java
public class UserDaoImpl implements UserDao {
    private Map<String,User> userMap;
    public void setUserMap(Map<String, User> userMap) {
    this.userMap = userMap;
    }    
public void save() {      
    System.out.println(userMap);
    System.out.println("UserDao save method running....");
    }
}
```

```xml
<bean id="u1" class="com.itheima.domain.User"/>
<bean id="u2" class="com.itheima.domain.User"/>
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
    <property name="userMap">
        <map>            
            <entry key="user1" value-ref="u1"/>
            <entry key="user2" value-ref="u2"/>
        </map>
    </property>
</bean>
```

（5）集合数据类型（Properties）的注入

```java
public class UserDaoImpl implements UserDao {
    private Properties properties;
    public void setProperties(Properties properties) {
        this.properties = properties;
    }
    public void save() {
        System.out.println(properties);
        System.out.println("UserDao save method running....");
    }
}
```

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
    <property name="properties">
        <props>
            <prop key="p1">aaa</prop>
            <prop key="p2">bbb</prop> 
            <prop key="p3">ccc</prop>
        </props>
    </property>
</bean>
```

### 3.9 引入其他配置文件（分模块开发）

实际开发中，Spring的配置内容非常多，这就导致Spring配置很繁杂且体积很大，所以，可以将部分配置拆解到其他配置文件中，而在Spring主配置文件通过import标签进行加载

```xml
<import resource="applicationContext-xxx.xml"/>
```

## 4. spring相关API

### 4.1 ApplicationContext的继承体系

applicationContext：接口类型，代表应用上下文，可以通过其实例获得 Spring 容器中的 Bean 对象

### 4.2 ApplicationContext的实现类

1）**ClassPathXmlApplicationContext**

​      它是从类的根路径下加载配置文件 推荐使用这种

2）FileSystemXmlApplicationContext

​      它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。

3）**AnnotationConfigApplicationContext**

​      当使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解。

### 4.3 getBean()方法使用

```java
public Object getBean(String name) throws BeansException {  
    assertBeanFactoryActive();   
    return getBeanFactory().getBean(name);
}
public <T> T getBean(Class<T> requiredType) throws BeansException {   
    assertBeanFactoryActive();
    return getBeanFactory().getBean(requiredType);
}
```

其中，当参数的数据类型是**字符串**时，表示根据Bean的id从容器中获得Bean实例，**返回是Object，需要强转**。

当参数的数据类型是**Class类型**时，表示根据类型从容器中匹配Bean实例，当容器中相同类型的Bean有多个时，则此方法会报错

**getBean()方法使用:**

```java
ApplicationContext applicationContext = new 
            ClassPathXmlApplicationContext("applicationContext.xml");
  UserService userService1 = (UserService) applicationContext.getBean("userService");
  UserService userService2 = applicationContext.getBean(UserService.class);
```

## 5.Spring配置数据源

### 5.1 数据源（连接池）的作用

数据源(连接池)是提高程序性能如出现的
事先实例化数据源，初始化部分连接资源
使用连接资源时从数据源中获取
使用完毕后将连接资源归还给数据源
常见的数据源(连接池)：**DBCP、C3P0、BoneCP、Druid**等

**开发步骤:**

①导入数据源的坐标和数据库驱动坐标
②创建数据源对象
③设置数据源的基本连接数据
④使用数据源获取连接资源和归还连接资源

### 5.2 数据源的手动创建

①导入c3p0和druid的坐标

```xml
<!-- C3P0连接池 -->
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
<!-- Druid连接池 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

①导入mysql数据库驱动坐标

```xml
<!-- mysql驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.39</version>
</dependency>
```

②创建C3P0连接池

```java
@Test
public void testC3P0() throws Exception {
    //创建数据源
    ComboPooledDataSource dataSource = new ComboPooledDataSource();
    //设置数据库连接参数
    dataSource.setDriverClass("com.mysql.jdbc.Driver");       dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
    dataSource.setUser("root");
    dataSource.setPassword("root");
    //获得连接对象
    Connection connection = dataSource.getConnection();
    System.out.println(connection);
}

```

②创建Druid连接池

```java
@Test
public void testDruid() throws Exception {
    //创建数据源
    DruidDataSource dataSource = new DruidDataSource();
    //设置数据库连接参数
    dataSource.setDriverClassName("com.mysql.jdbc.Driver"); 
    dataSource.setUrl("jdbc:mysql://localhost:3306/test");   
    dataSource.setUsername("root");
    dataSource.setPassword("root");
    //获得连接对象
    Connection connection = dataSource.getConnection();    
    System.out.println(connection);
}
```

③提取jdbc.properties配置文件

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
```

④读取jdbc.properties配置文件创建连接池

```java
@Test
public void testC3P0ByProperties() throws Exception {
    //加载类路径下的jdbc.properties
    ResourceBundle rb = ResourceBundle.getBundle("jdbc");
    ComboPooledDataSource dataSource = new ComboPooledDataSource(); 
    dataSource.setDriverClass(rb.getString("jdbc.driver"));   
    dataSource.setJdbcUrl(rb.getString("jdbc.url")); 
    dataSource.setUser(rb.getString("jdbc.username")); 
    dataSource.setPassword(rb.getString("jdbc.password"));
    Connection connection = dataSource.getConnection();   
    System.out.println(connection);
}
```

### 5.3 Spring配置数据源

可以将DataSource的创建权交由Spring容器去完成

DataSource有无参构造方法，而Spring默认就是通过无参构造方法实例化对象的

DataSource要想使用需要通过set方法设置数据库连接信息，而Spring可以通过set方法进行字符串注入

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
    <property name="user" value="root"/>
    <property name="password" value="root"/>
</bean>
```

测试从容器当中获取数据源

```java
ApplicationContext applicationContext = new 
           ClassPathXmlApplicationContext("applicationContext.xml");
               DataSource dataSource = (DataSource) 
applicationContext.getBean("dataSource");
Connection connection = dataSource.getConnection();
System.out.println(connection);
```

### 5.4 抽取jdbc配置文件

applicationContext.xml加载jdbc.properties配置文件获得连接信息。

首先，需要引入context命名空间和约束路径：

命名空间：xmlns:context="http://www.springframework.org/schema/context"

约束路径：
`http://www.springframework.org/schema/context>
​http://www.springframework.org/schema/context/spring-context.xsd`

```xml
<context:property-placeholder location="classpath:jdbc.properties"/>
//SPL获取
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

### 5.5 知识要点

Spring容器加载properties文件

```xml
<context:property-placeholder location="xx.properties"/>
<property name="" value="${key}"/>
```

## 6. Spring注解开发

### 6.1 Spring原始注解

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以注解开发是一种趋势，注解代替xml配置文件可以简化配置，提高开发效率。

Spring原始注解主要是替代`<Bean>`的配置

| 注解           | 说明                                           |
| -------------- | ---------------------------------------------- |
| @Component     | 使用在类上用于实例化Bean                       |
| @Controller    | 使用在web层类上用于实例化Bean                  |
| @Service       | 使用在service层类上用于实例化Bean              |
| @Repository    | 使用在dao层类上用于实例化Bean                  |
| @Autowired     | 使用在字段上用于根据类型依赖注入               |
| @Qualifier     | 结合@Autowired一起使用用于根据名称进行依赖注入 |
| @Resource       | 相当于@Autowired+@Qualifier，按照名称进行注入  |
| @Value         | 注入普通属性                                   |
| @Scope         | 标注Bean的作用范围                             |
| @PostConstruct | 使用在方法上标注该方法是Bean的初始化方法       |
| @PreDestroy    | 使用在方法上标注该方法是Bean的销毁方法         |

注意：

使用注解进行开发时，需要在applicationContext.xml中**配置组件扫描**，作用是指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类、字段和方法。

```java
<!--注解的组件扫描-->
<context:component-scan base-package="com.itheima"></context:component-scan>
```

使用@Compont或@Repository标识UserDaoImpl需要Spring进行实例化。

```java
//@Component("userDao")
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    @Override
    public void save() {
        System.out.println("save running... ...");
    }
}
```

使用@Compont或@Service标识UserServiceImpl需要Spring进行实例化

使用@Autowired或者@Autowired+@Qulifier或者@Resource进行userDao的注入
只使用@Autowired而不使用@Qulifier指定ID,那么会按照类型匹配查找
@Resource==@Autowired+@Qulifier

```java
//@Component("userService")
@Service("userService")
public class UserServiceImpl implements UserService {
    /*@Autowired
    @Qualifier("userDao")*/
    @Resource(name="userDao")
    private UserDao userDao;
    @Override
    public void save() {       
        userDao.save();
    }
}
```

使用@Value进行字符串的注入

```java
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    @Value("注入普通数据")
    private String str;
    @Value("${jdbc.driver}")
    private String driver;
    @Override
    public void save() {
        System.out.println(str);
        System.out.println(driver);
        System.out.println("save running... ...");
    }
}
```

使用@Scope标注Bean的范围

```java
//@Scope("prototype")
@Scope("singleton")
public class UserDaoImpl implements UserDao {
   //此处省略代码
}
```

使用@PostConstruct标注初始化方法，使用@PreDestroy标注销毁方法

```java
@PostConstruct
public void init(){
    System.out.println("初始化方法....");
}
@PreDestroy
public void destroy(){
    System.out.println("销毁方法.....");
}
```

### 6.2 Spring新注解

使用上面的注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下：

非自定义的Bean的配置： `<bean>`

加载properties文件的配置：`<context:property-placeholder>`

组件扫描的配置：`<context:component-scan>`

引入其他文件：`<import>`

| 注解            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Configuration  | 用于指定当前类是一个 Spring   配置类，当创建容器时会从该类上加载注解 |
| @ComponentScan  | 用于指定 Spring   在初始化容器时要扫描的包。   作用和在 Spring   的 xml 配置文件中的   <context:component-scan   base-package="com.itheima"/>一样 |
| @Bean           | 使用在方法上，标注将该方法的返回值存储到   Spring   容器中   |
| @PropertySource | 用于加载.properties   文件中的配置                           |
| @Import         | 用于导入其他配置类                                           |

@Configuration

@ComponentScan

@Import

```java
@Configuration
@ComponentScan("com.itheima")
@Import({DataSourceConfiguration.class})
public class SpringConfiguration {
}
```

@PropertySource

@value

```java
@PropertySource("classpath:jdbc.properties")
public class DataSourceConfiguration {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
```

@Bean

```java
@Bean(name="dataSource")
public DataSource getDataSource() throws PropertyVetoException { 
    ComboPooledDataSource dataSource = new ComboPooledDataSource(); 
    dataSource.setDriverClass(driver);
    dataSource.setJdbcUrl(url);
    dataSource.setUser(username);
    dataSource.setPassword(password);
    return dataSource;
} 
```

测试加载核心配置类创建Spring容器

```java
@Test
public void testAnnoConfiguration() throws Exception {
ApplicationContext applicationContext = new 
          AnnotationConfigApplicationContext(SpringConfiguration.class);    UserService userService = (UserService)    
    applicationContext.getBean("userService");
    userService.save();
    DataSource dataSource = (DataSource) 
    applicationContext.getBean("dataSource");
    Connection connection = dataSource.getConnection(); 
    System.out.println(connection);
    }
```

## 7. Spring整合Junit

### 7.1 原始Junit测试Spring的问题

在测试类中，每个测试方法都有以下两行代码：

```java
 ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
 IAccountService as = ac.getBean("accountService",IAccountService.class);
```

这两行代码的作用是获取容器，如果不写的话，直接会提示空指针异常。所以又不能轻易删掉。

### 7.2 上述问题解决思路

让SpringJunit负责创建Spring容器，但是需要将配置文件的名称告诉它

将需要进行测试Bean直接在测试类中进行注入

### 7.3 Spring集成Junit步骤

①导入spring集成Junit的坐标

②使用@Runwith注解替换原来的运行期

③使用@ContextConfiguration指定配置文件或配置类

④使用@Autowired注入需要测试的对象

⑤创建测试方法进行测试

### 7.4 Spring集成Junit代码实现

①导入spring集成Junit的坐标

```xml
<!--此处需要注意的是，spring5 及以上版本要求 junit 的版本必须是 4.12 及以上-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.2.RELEASE</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

②使用@Runwith注解替换原来的运行期

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class SpringJunitTest {
}
```

③使用@ContextConfiguration指定配置文件或配置类

```java
@RunWith(SpringJUnit4ClassRunner.class)
//加载spring核心配置文件
//@ContextConfiguration(value = {"classpath:applicationContext.xml"})
//加载spring核心配置类
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {
}
```

④使用@Autowired注入需要测试的对象

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {
    @Autowired
    private UserService userService;
}
```

⑤创建测试方法进行测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})public class SpringJunitTest {
    @Autowired
    private UserService userService;
    @Test
    public void testUserService(){
    userService.save();
    }
}
```

Spring集成Junit步骤

①导入spring集成Junit的坐标

②使用@Runwith注解替换原来的运行期

③使用@ContextConfiguration指定配置文件或配置类

④使用@Autowired注入需要测试的对象

⑤创建测试方法进行测试

---

### 1.Spring 的 AOP 简介

#### 1.1 什么是 AOP

AOP 为 Aspect Oriented Programming 的缩写，意思为面向切面编程，是通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

AOP 是 OOP 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对**业务逻辑的各个部分进行隔离**，从而使得业务逻辑各部分之间的**耦合度降低**，提高程序的可重用性，同时提高了开发的效率。

#### 1.2 AOP 的作用及其优势

作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强

优势：减少重复代码，提高开发效率，并且便于维护

#### 1.3 AOP 的底层实现

实际上，AOP 的底层是通过 Spring 提供的的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法，从而完成功能的增强。

#### 1.4 AOP 的动态代理技术

常用的动态代理技术

JDK 代理 : 基于接口的动态代理技术

cglib 代理：基于父类的动态代理技术

![](img\图片1.png)

#### 1.5 JDK 的动态代理

①目标类接口

 ```java
public interface TargetInterface {
    public void method();
}
 ```

②目标类

```java
public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
```

③动态代理代码

```java
Target target = new Target(); //创建目标对象
//创建代理对象
TargetInterface proxy = (TargetInterface) Proxy.newProxyInstance(target.getClass()
.getClassLoader(),target.getClass().getInterfaces(),new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) 
            throws Throwable {
                System.out.println("前置增强代码...");
                Object invoke = method.invoke(target, args);
                System.out.println("后置增强代码...");
                return invoke;
            }
        }
);
```

④  调用代理对象的方法测试

```java
// 测试,当调用接口的任何方法时，代理对象的代码都无序修改
proxy.method();
```

![](img\图片2.png)

#### 1.6 cglib 的动态代理

①目标类

  ```java
public class Target {
    public void method() {
        System.out.println("Target running....");
    }
}
  ```

②动态代理代码

```java
Target target = new Target(); //创建目标对象
Enhancer enhancer = new Enhancer();   //创建增强器
enhancer.setSuperclass(Target.class); //设置父类
enhancer.setCallback(new MethodInterceptor() { //设置回调
    @Override
    public Object intercept(Object o, Method method, Object[] objects, 
    MethodProxy methodProxy) throws Throwable {
        System.out.println("前置代码增强....");
        Object invoke = method.invoke(target, objects);
        System.out.println("后置代码增强....");
        return invoke;
    }
});
Target proxy = (Target) enhancer.create(); //创建代理对象

```

③调用代理对象的方法测试

```java
//测试,当调用接口的任何方法时，代理对象的代码都无序修改
proxy.method();
```

![](img\图片3.png)

#### 1.7 AOP 相关概念

Spring 的 AOP 实现底层就是对上面的动态代理的代码进行了封装，封装后我们只需要对需要关注的部分进行代码编写，并通过配置的方式完成指定目标的方法增强。

在正式讲解 AOP 的操作之前，我们必须理解 AOP 的相关术语，常用的术语如下：

- Target（目标对象）：代理的目标对象

- Proxy （代理）：一个类被 AOP 织入增强后，就产生一个结果代理类

-  Joinpoint（连接点）：所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法，因为spring只支持方法类型的连接点

- Pointcut（切入点）：所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义

- Advice（通知/ 增强）：所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知

-  Aspect（切面）：是切入点和通知（引介）的结合

- Weaving（织入）：是指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入

1.8 AOP 开发明确的事项

1.8 AOP 开发明确的事项

#### 1.8 AOP 开发明确的事项

##### 1)需要编写的内容

- 编写核心业务代码（目标类的目标方法）

- 编写切面类，切面类中有通知(增强功能方法)

- 在配置文件中，配置织入关系，即将哪些通知与哪些连接点进行结合

##### 2）AOP 技术实现的内容

Spring 框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

##### 3）AOP 底层使用哪种代理方式

在 spring 中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

#### 1.9 知识要点

- aop：面向切面编程

- aop底层实现：基于JDK的动态代理 和 基于Cglib的动态代理

- aop的重点概念：

        Pointcut（切入点）：被增强的方法

        Advice（通知/ 增强）：封装增强业务逻辑的方法

        Aspect（切面）：切点+通知

        Weaving（织入）：将切点与通知结合的过程

- 开发明确事项：

        谁是切点（切点表达式配置）

        谁是通知（切面类中的增强方法）

        将切点和通知进行织入配置

### 2. 基于 XML 的 AOP 开发

#### 2.1 快速入门

①导入 AOP 相关坐标

②创建目标接口和目标类（内部有切点）

③创建切面类（内部有增强方法）

④将目标类和切面类的对象创建权交给 spring

⑤在 applicationContext.xml 中配置织入关系

⑥测试代码

①导入 AOP 相关坐标

```xml
<!--导入spring的context坐标，context依赖aop-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
<!-- aspectj的织入 -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.8.13</version>
</dependency>
```

②创建目标接口和目标类（内部有切点）

```java
public interface TargetInterface {
    public void method();
}

public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
```

③创建切面类（内部有增强方法）

```java
public class MyAspect {
    //前置增强方法
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

④将目标类和切面类的对象创建权交给 spring

```xml
<!--配置目标类-->
<bean id="target" class="com.itheima.aop.Target"></bean>
<!--配置切面类-->
<bean id="myAspect" class="com.itheima.aop.MyAspect"></bean>

```

⑤在 applicationContext.xml 中配置织入关系

导入aop命名空间

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

```

⑤在 applicationContext.xml 中配置织入关系

配置切点表达式和前置增强的织入关系

```xml
<aop:config>
    <!--引用myAspect的Bean为切面对象-->
    <aop:aspect ref="myAspect">
        <!--配置Target的method方法执行时要进行myAspect的before方法前置增强-->
        <aop:before method="before" pointcut="execution(public void com.itheima.aop.Target.method())"></aop:before>
    </aop:aspect>
</aop:config>
```

⑥测试代码

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {
    @Autowired
    private TargetInterface target;
    @Test
    public void test1(){
        target.method();
    }
}
```

⑦测试结果

![](img\图片4.png)

#### 2.2 XML 配置 AOP 详解

##### 1) 切点表达式的写法

表达式语法：

```java
execution([修饰符] 返回值类型 包名.类名.方法名(参数))
```

- 访问修饰符可以省略

- 返回值类型、包名、类名、方法名可以使用星号*  代表任意

- 包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类

- 参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表

例如：

```xml
execution(public void com.itheima.aop.Target.method())	
execution(void com.itheima.aop.Target.*(..))
execution(* com.itheima.aop.*.*(..))
execution(* com.itheima.aop..*.*(..))
execution(* *..*.*(..))
```

##### 2) 通知的类型

通知的配置语法：

```xml
<aop:通知类型 method=“切面类中方法名” pointcut=“切点表达式"></aop:通知类型>
```

![](img\图片5.png)

##### 3) 切点表达式的抽取

当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用 pointcut-ref 属性代替 pointcut 属性来引用抽取后的切点表达式。

```xml
<aop:config>
    <!--引用myAspect的Bean为切面对象-->
    <aop:aspect ref="myAspect">
        <aop:pointcut id="myPointcut" expression="execution(* com.itheima.aop.*.*(..))"/>
        <aop:before method="before" pointcut-ref="myPointcut"></aop:before>
    </aop:aspect>
</aop:config>
```

#### 2.3 知识要点

- aop织入的配置

```xml
<aop:config>
    <aop:aspect ref=“切面类”>
        <aop:before method=“通知方法名称” pointcut=“切点表达式"></aop:before>
    </aop:aspect>
</aop:config>
```

- 通知的类型：前置通知、后置通知、环绕通知、异常抛出通知、最终通知
- 切点表达式的写法：

```xml
execution([修饰符] 返回值类型 包名.类名.方法名(参数))
```

### 3.基于注解的 AOP 开发

#### 3.1 快速入门

基于注解的aop开发步骤：

①创建目标接口和目标类（内部有切点）

②创建切面类（内部有增强方法）

③将目标类和切面类的对象创建权交给 spring

④在切面类中使用注解配置织入关系

⑤在配置文件中开启组件扫描和 AOP 的自动代理

⑥测试



①创建目标接口和目标类（内部有切点）

```java
public interface TargetInterface {
    public void method();
}

public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
```

②创建切面类（内部有增强方法)

```java
public class MyAspect {
    //前置增强方法
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

③将目标类和切面类的对象创建权交给 spring

```java
@Component("target")
public class Target implements TargetInterface {
    @Override
    public void method() {
        System.out.println("Target running....");
    }
}
@Component("myAspect")
public class MyAspect {
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

④在切面类中使用注解配置织入关系

```java
@Component("myAspect")
@Aspect
public class MyAspect {
    @Before("execution(* com.itheima.aop.*.*(..))")
    public void before(){
        System.out.println("前置代码增强.....");
    }
}
```

⑤在配置文件中开启组件扫描和 AOP 的自动代理

```xml
<!--组件扫描-->
<context:component-scan base-package="com.itheima.aop"/>

<!--aop的自动代理-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>

```

⑥测试代码

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {
    @Autowired
    private TargetInterface target;
    @Test
    public void test1(){
        target.method();
    }
}
```

⑦测试结果

![](img\图片6.png)

#### 3.2 注解配置 AOP 详解

##### 1) 注解通知的类型

通知的配置语法：@通知注解(“切点表达式")

![](img\图片7.png)

##### 2) 切点表达式的抽取

同 xml配置
aop 一样，我们可以将切点表达式抽取。抽取方式是在切面内定义方法，在该方法上使用@Pointcut注解定义切点表达式，然后在在增强注解中进行引用。具体如下：

```java
@@Component("myAspect")
@Aspect
public class MyAspect {
    @Before("MyAspect.myPoint()")
    public void before(){
        System.out.println("前置代码增强.....");
    }
    @Pointcut("execution(* com.itheima.aop.*.*(..))")
    public void myPoint(){}
}
```

#### 3.3 知识要点

- 注解aop开发步骤

①使用@Aspect标注切面类

②使用@通知注解标注通知方法

③在配置文件中配置aop自动代理<aop:aspectj-autoproxy/>

- 通知注解类型

![](img\图片8.png)

