# MyBatis

## 1.Mybatis简介

### 1.1 原始jdbc操作的分析

原始jdbc开发存在的问题如下：

1. 数据库连接创建、释放频繁造成**系统资源浪费**从而影响系统性能
2. **sql与java强耦合**。sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变java代码。**XML解耦合**
3. 查询操作时，需要手动将结果集中的数据手动U**封装到实体**中。插入操作时，需要手动将实体的数据设置到sql语句的占位符位置

应对上述问题给出的解决方案：

1. 使用数据库连接池初始化连接资源
2. 将sql语句抽取到**xml配置**文件中
3. 使用**反射、内省**等底层技术，自动将实体与表进行属性与字段的**自动映射**

### 1.2 什么是Mybatis

mybatis 是一个优秀的基于java的**持久层框架**，它内部**封装了jdbc**，使开发者 ***只需要关注sql语句本身***，而不需要花费精力去处理加载*驱动*、*创建连接*、*创建statement*等繁杂的过程。
x'x'x
mybatis通过**xml或注解**的方式将要执行的各种 statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句。

最后mybatis框架执行sql并将结果***映射为java对象***并返回。采用**ORM思想（object relationship mapping）**解决了实体和数据库映射的问题，对jdbc 进行了**封装**，屏蔽了 **jdbc api**底层访问细节，使我们不用与jdbc api 打交道，就可以完成对数据库的持久化操作。

---

## 2.Mybatis的快速入门

### 2.1 MyBatis开发步骤

MyBatis官网地址：<http://www.mybatis.org/mybatis-3/>

**MyBatis开发步骤：**

1. 添加MyBatis的坐标(数据库驱动，MyBatis坐标dependency)
2. 创建user数据表
3. 编写User实体类
4. 编写映射文件UserMapper.xml(描述SQL语句，namespace.id  resultType)
5. 编写核心文件SqlMapConfig.xml（MyBatis配置：环境和映射文件路径）
6. 编写测试类

### 2.2 环境搭建

1. 导入MyBatis的坐标和其他相关坐标

    ```xml
    <!--mybatis坐标-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.5</version>
    </dependency>
    <!--mysql驱动坐标-->
    <dependency>    
        <groupId>mysql</groupId>   
        <artifactId>mysql-connector-java</artifactId>    
        <version>5.1.6</version>    
        <scope>runtime</scope>
    </dependency>
    <!--单元测试坐标-->
    <dependency>    
        <groupId>junit</groupId>    
        <artifactId>junit</artifactId>    
        <version>4.12</version>    
        <scope>test</scope>
    </dependency>
    <!--日志坐标-->
    <dependency>    
        <groupId>log4j</groupId>    
        <artifactId>log4j</artifactId>    
        <version>1.2.12</version>
    </dependency>
    ```

2. 创建user数据表（user是maper映射的实体类）
3. 编写User实体

    ```java
    public class User {    
        private int id;    
        private String username;    
        private String password;
        //省略get个set方法
    }
    ```

4. 编写UserMapper映射文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper        
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"        
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="userMapper">    
        <select id="findAll" resultType="com.itheima.domain.User">        
            select * from User    
        </select>
    </mapper>
    ```

5. 编写MyBatis核心文件

    ```xml
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN“ "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>    
        <environments default="development">        
            <environment id="development">            
                <transactionManager type="JDBC"/>            
                <dataSource type="POOLED">                
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql:///test"/>                
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>            
                </dataSource>        
            </environment>    
        </environments>    

        <mappers> 
            <mapper resource="com/itheima/mapper/UserMapper.xml"/> 
        </mappers>
    </configuration>
    ```

### 2.3 编写测试代码

```java
//加载核心配置文件
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
//获得sqlSession工厂对象
SqlSessionFactory sqlSessionFactory = new            
                        SqlSessionFactoryBuilder().build(resourceAsStream);
//获得sqlSession对象
SqlSession sqlSession = sqlSessionFactory.openSession();
//执行sql语句
List<User> userList = sqlSession.selectList("userMapper.findAll");
//打印结果
System.out.println(userList);
//释放资源
sqlSession.close();
```

### 2.4 知识小结

**MyBatis开发步骤：**

1. 添加MyBatis的坐标
2. 创建user数据表
3. 编写User实体类
4. 编写映射文件UserMapper.xml
5. 编写核心文件SqlMapConfig.xml
6. 编写测试类

## 3. MyBatis的映射文件概述

![MyBatis的映射文件概述](img\MyBatis的映射文件概述.png)

---

## 4. MyBatis的增删改查操作

### 4.1 MyBatis的插入数据操作(MyBatis默认不自动提交事务)

**1编写UserMapper映射文件：**

```xml
<mapper namespace="userMapper">    
    <insert id="add" parameterType="com.itheima.domain.User">        
        insert into user values(#{id},#{username},#{password})    
    </insert>
</mapper>
```

**2)编写插入实体User的代码：**

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new 
                        SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int insert = sqlSession.insert("userMapper.add", user);
System.out.println(insert);
//提交事务
sqlSession.commit();
sqlSession.close();
```

**3)插入操作注意问题：**

* 插入语句使用insert标签
* 在映射文件中使用parameterType属性指定要插入的数据类型
* Sql语句中使用#{实体**属性名**}方式引用实体中的属性值
* 插入操作使用的API是**sqlSession.insert(“命名空间.id”,实体对象)**;
* 插入操作涉及数据库数据变化，所以要使用sqlSession对象显示的**提交事务**，即sqlSession.commit()

### 4.2 MyBatis的修改数据操作

**1)编写UserMapper映射文件：**

```xml
<mapper namespace="userMapper">
    <update id="update" parameterType="com.itheima.domain.User">
        update user set username=#{username},password=#{password} where id=#{id}
    </update>
</mapper>
```

**2)编写修改实体User的代码：**

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int update = sqlSession.update("userMapper.update", user);
System.out.println(update);
sqlSession.commit();
sqlSession.close();
```

**3)修改操作注意问题：**

• 修改语句使用update标签

• 修改操作使用的API是sqlSession.update(“命名空间.id”,实体对象);

### 4.3 MyBatis的删除数据操作

**1)编写UserMapper映射文件：**

mapper配置参数举例**java.lang.Integer**（不是非要特别写得实体类）

```xml
<mapper namespace="userMapper">
    <delete id="delete" parameterType="java.lang.Integer">
        delete from user where id=#{id}
    </delete>
</mapper>

```

**2)编写删除数据的代码：**

```java
InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
int delete = sqlSession.delete("userMapper.delete",3);
System.out.println(delete);
sqlSession.commit();
sqlSession.close();
```

**3)删除操作注意问题：**

• 删除语句使用delete标签

•Sql语句中使用#{任意字符串}方式引用传递的单个参数

•删除操作使用的API是sqlSession.delete(“命名空间.id”,Object);

### 4.4 知识小结

```java
增删改查映射配置与API：
查询数据： List<User> userList = sqlSession.selectList("userMapper.findAll");
    <select id="findAll" resultType="com.itheima.domain.User">
        select * from User
    </select>
添加数据： sqlSession.insert("userMapper.add", user);
    <insert id="add" parameterType="com.itheima.domain.User">
        insert into user values(#{id},#{username},#{password})
    </insert>
修改数据： sqlSession.update("userMapper.update", user);
    <update id="update" parameterType="com.itheima.domain.User">
        update user set username=#{username},password=#{password} where id=#{id}
    </update>
删除数据：sqlSession.delete("userMapper.delete",3);
    <delete id="delete" parameterType="java.lang.Integer">
        delete from user where id=#{id}
    </delete>
```

---

## 5. MyBatis核心配置文件概述

### 5.1 MyBatis核心配置文件层级关系(顺序严格)

![MyBatis核心配置文件层级关系](img\MyBatis核心配置文件层级关系.png)

#### 5.2 MyBatis常用配置解析

**1)environments标签：**

数据库环境的配置，支持多环境配置

![MyBatis映射文件_环境配置](img\MyBatis映射文件_环境配置.png)

其中，事务管理器（**transactionManager**）类型有两种：

* JDBC：这个配置就是直接使用了JDBC 的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。
* MANAGED：这个配置几乎没做什么。它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会**关闭连接**，然而一些容器并不希望这样，因此需要将 closeConnection 属性设置为 false 来阻止它默认的关闭行为。  

其中，数据源（dataSource）类型有三种：

* UNPOOLED：这个数据源的实现只是每次被请求时打开和关闭连接。
* **POOLED**：这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来。
* JNDI：这个数据源的实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的引用。

**2)mapper标签：**

该标签的作用是加载映射的，加载方式有如下几种：

* 使用**相对类路径**的资源引用，例如：
    `<mapper resource="org/mybatis/builder/AuthorMapper.xml"/>`
* 使用完全限定资源定位符（URL），例如：
    `<mapper url="file:///var/mappers/AuthorMapper.xml"/>`
* 使用**映射器接口**实现类的完全限定类名，例如：
    `<mapper class="org.mybatis.builder.AuthorMapper"/>`
* 将包内的映射器接口实现全部注册为映射器，例如：
    `<package name="org.mybatis.builder"/>`

**3)Properties标签:**

实际开发中，习惯将数据源的配置信息单独抽取成一个properties文件，该标签可以加载额外配置的properties文件

  ![Properties标签](img\Properties标签.png)

**4)typeAliases标签:**

类型别名是为Java 类型设置一个短的名字。原来的类型名称配置如下

![全限定名别名](img\全限定名.png)

配置typeAliases，为com.itheima.domain.User定义别名为user

![全限定名别名](img\全限定名别名.png)

上面我们是自定义的别名，mybatis框架已经为我们设置好的一些常用的类型的别名

![全限定名自带别名](img\全限定名自带别名.png)

### 5.3 知识小结

**核心配置文件常用配置：**

**properties标签**：该标签可以加载外部的数据源配置的properties文件

```xml
<properties resource="jdbc.properties"></properties>
```

**typeAliases标签**：设置类型别名，mapper可直接使用别名

```xml
<typeAlias type="com.itheima.domain.User" alias="user"></typeAlias>
```

**mappers标签**：加载映射配置

```xml
<mapper resource="com/itheima/mapper/UserMapping.xml"></mapper>
```

**environments标签**：数据源环境配置标签

![数据源环境配置标签](img\数据源环境配置标签.png)  

---

### 6.MyBatis相应 *API*

#### 6.1 SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：SqlSessionFactory  build(InputStream inputStream)

通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象

 ```java
String resource = "org/mybatis/builder/mybatis-config.xml"; 
InputStream inputStream = Resources.getResourceAsStream(resource); 
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder(); 
SqlSessionFactory factory = builder.build(inputStream);
 ```

其中， Resources 工具类，这个类在 org.apache.ibatis.io 包中。Resources 类帮助你从类路径下、文件系统或一个 web URL 中加载资源文件。

#### 6.2 SqlSession工厂对象SqlSessionFactory

SqlSessionFactory 有多个个方法创建SqlSession 实例。常用的有如下两个：

![1552653723002](img\SqlSessionFactory方法.png)

#### 6.3 SqlSession会话对象

SqlSession 实例在 MyBatis 中是非常强大的一个类。在这里你会看到所有执行语句、提交或回滚事务和获取映射器实例的方法。

执行语句的方法主要有：

```java
<T> T selectOne(String statement, Object parameter) 
<E> List<E> selectList(String statement, Object parameter) 
int insert(String statement, Object parameter) 
int update(String statement, Object parameter) 
int delete(String statement, Object parameter)

```

操作事务的方法主要有：

```java
void commit()  
void rollback() 
```

## 7.Mybatis的Dao层实现

### 7.1 传统开发方式

#### 7.1.1编写UserDao接口

```java
public interface UserDao {
    List<User> findAll() throws IOException;
}
```

#### 7.1.2.编写UserDaoImpl实现

```java
public class UserDaoImpl implements UserDao {
    public List<User> findAll() throws IOException {
        InputStream resourceAsStream = 
                    Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new 
                    SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<User> userList = sqlSession.selectList("userMapper.findAll");
        sqlSession.close();
        return userList;
    }
}
```

#### 7.1.3 测试传统方式

```java
@Test
public void testTraditionDao() throws IOException {
    UserDao userDao = new UserDaoImpl();
    List<User> all = userDao.findAll();
    System.out.println(all);
}

```

### 7.2 代理开发方式

#### 7.2.1 代理开发方式介绍

采用 Mybatis 的代理开发方式实现 DAO 层的开发，这种方式是我们后面进入企业的主流。

Mapper 接口开发方法只需要程序员编写Mapper 接口（相当于Dao 接口），由Mybatis 框架根据接口定义创建接口的动态代理对象，代理对象的方法体同上边Dao接口实现类方法。

Mapper 接口开发需要遵循以下规范：

1. **Mapper.xml文件中的namespace与mapper接口的全限定名相同**
2. **Mapper接口方法名和Mapper.xml中定义的每个statement的id相同**
3. **Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同**
4. **Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同**

#### 7.2.2 编写UserMapper接口

![UserMapper接口](img\UserMapper接口.png)

#### 7.2.3测试代理方式

```java
@Test
public void testProxyDao() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //获得MyBatis框架生成的UserMapper接口的实现类
  UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    User user = userMapper.findById(1);
    System.out.println(user);
    sqlSession.close();
}
```

### 7.3 知识小结

MyBatis的Dao层实现的两种方式：

手动对Dao进行实现：传统开发方式

**代理方式对Dao进行实现：**
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

## 8.MyBatis映射文件深入

### 8.1 动态sql语句

#### 8.1.1动态sql语句概述

Mybatis 的映射文件中，前面我们的 SQL 都是比较简单的，有些时候业务逻辑复杂时，我们的 SQL是动态变化的，此时在前面的学习中我们的 SQL 就不能满足要求了。

参考的官方文档，描述如下：

![DynamicSQL](img\DynamicSQL.png)

#### 8.1.2动态 SQL  之<**if>**

我们根据实体类的不同取值，使用不同的 SQL语句来进行查询。比如在 id如果不为空时可以根据id查询，如果username 不同空时还要加入用户名作为条件。这种情况在我们的多条件组合查询中经常会碰到。

```xml
<select id="findByCondition" parameterType="user" resultType="user">
    select * from User
    <where>
        <if test="id!=0">
            and id=#{id}
        </if>
        <if test="username!=null">
            and username=#{username}
        </if>
    </where>
</select>

```

当查询条件id和username都存在时，控制台打印的sql语句如下：

```java
     … … …
     //获得MyBatis框架生成的UserMapper接口的实现类
  UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    User condition = new User();
    condition.setId(1);
    condition.setUsername("lucy");
    User user = userMapper.findByCondition(condition);
    … … …
```

当查询条件只有id存在时，控制台打印的sql语句如下：

```java
 … … …
 //获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
User condition = new User();
condition.setId(1);
User user = userMapper.findByCondition(condition);
… … …

```

#### 8.1.3 动态 SQL  之<**foreach>**

循环执行sql的拼接操作，例如：SELECT * FROM USER WHERE id IN (1,2,5)。

 ```xml
<select id="findByIds" parameterType="list" resultType="user">
    select * from User
    <where>
        <foreach collection="array" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
 ```

测试代码片段如下：

```java
 … … …
 //获得MyBatis框架生成的UserMapper接口的实现类
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
int[] ids = new int[]{2,5};
List<User> userList = userMapper.findByIds(ids);
System.out.println(userList);
… … …

```

foreach标签的属性含义如下：

`<foreach>`标签用于遍历集合，它的属性：

* collection：代表要遍历的集合元素，注意编写时不要写#{}
* open：代表语句的开始部分
* close：代表结束部分
* item：代表遍历集合的每个元素，生成的变量名
* sperator：代表分隔符

### 8.2 SQL片段抽取

Sql 中可将重复的 sql 提取出来，使用时用 include 引用即可，最终达到 sql 重用的目的

```xml
<!--抽取sql片段简化编写-->
<sql id="selectUser" select * from User</sql>
<select id="findById" parameterType="int" resultType="user">
    <include refid="selectUser"></include> where id=#{id}
</select>
<select id="findByIds" parameterType="list" resultType="user">
    <include refid="selectUser"></include>
    <where>
        <foreach collection="array" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```

#### 8.3 知识小结

MyBatis映射文件配置：
`<select>`：查询
`<insert>`：插入
`<update>`：修改
`<delete>`：删除
`<where>`：where条件
`<if>`：if判断
`<foreach>`：循环
`<sql>`：sql片段抽取

## 9. MyBatis核心配置文件深入

### 9.1typeHandlers标签

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器（截取部分）。

 ![默认的类型处理器](img\默认的类型处理器.png)

你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。具体做法为：实现 org.apache.ibatis.type.TypeHandler 接口， 或继承一个很便利的类 org.apache.ibatis.type.BaseTypeHandler， 然后可以选择性地将它映射到一个JDBC类型。例如需求：一个Java中的Date数据类型，我想将之存到数据库的时候存成一个1970年至今的毫秒数，取出来时转换成java的Date，即java的Date与数据库的varchar毫秒值之间转换。

开发步骤：

1. 定义转换类继承类BaseTypeHandler`<T>`
2. 覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，getNullableResult为查询时 mysql的字符串类型转换成 java的Type类型的方法
3. 在MyBatis核心配置文件中进行注册

测试转换是否正确

```java
public class MyDateTypeHandler extends BaseTypeHandler<Date> {
    public void setNonNullParameter(PreparedStatement preparedStatement, int i, Date date, JdbcType type) {
        preparedStatement.setString(i,date.getTime()+"");
    }
    public Date getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return new Date(resultSet.getLong(s));
    }
    public Date getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return new Date(resultSet.getLong(i));
    }
    public Date getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return callableStatement.getDate(i);
    }
}
```

```xml
<!--注册类型自定义转换器-->
<typeHandlers>
    <typeHandler handler="com.itheima.typeHandlers.MyDateTypeHandler"></typeHandler>
</typeHandlers>
```

测试添加操作：

```java
user.setBirthday(new Date());
userMapper.add2(user);
```

### 9.2 plugins标签

MyBatis可以使用第三方的插件来对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即可获得分页的相关数据

开发步骤：

1. 导入通用PageHelper的坐标
2. 在mybatis核心配置文件中配置PageHelper插件
3. 测试分页数据获取

### 1导入通用PageHelper坐标

```xml
<!-- 分页助手 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>3.7.5</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>0.9.1</version>
</dependency>

```

#### 2在mybatis核心配置文件中配置PageHelper插件

```xml
<!-- 注意：分页助手的插件  配置在通用馆mapper之前 -->
<plugin interceptor="com.github.pagehelper.PageHelper">
    <!-- 指定方言 -->
    <property name="dialect" value="mysql"/>
</plugin>
```

### 3测试分页代码实现

```java
@Test
public void testPageHelper(){
    //设置分页参数
    PageHelper.startPage(1,2);

    List<User> select = userMapper2.select(null);
    for(User user : select){
        System.out.println(user);
    }
}
```

### 4获得分页相关的其他参数

```java
//其他分页的数据
PageInfo<User> pageInfo = new PageInfo<User>(select);
System.out.println("总条数："+pageInfo.getTotal());
System.out.println("总页数："+pageInfo.getPages());
System.out.println("当前页："+pageInfo.getPageNum());
System.out.println("每页显示长度："+pageInfo.getPageSize());
System.out.println("是否第一页："+pageInfo.isIsFirstPage());
System.out.println("是否最后一页："+pageInfo.isIsLastPage());

```

### 9.3 知识小结

MyBatis核心配置文件常用标签：

1. properties标签：该标签可以加载外部的properties文件
2. typeAliases标签：设置类型别名
3. environments标签：数据源环境配置标签
4. typeHandlers标签：配置自定义类型处理器
5. plugins标签：配置MyBatis的插件

---

## 10.Mybatis多表查询

### 10.1 一对一查询

#### 10.1.1 一对一查询的模型MapperScannerConfigurer

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对一查询的需求：查询一个订单，与此同时查询出该订单所属的用户

#### 10.1.2一对一查询的语句

对应的sql语句：select *  from orders o,user u where o.uid=u.id;

#### 10.1.3 创建Order和User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    //代表当前订单从属于哪一个客户
    private User user;
}

public class User {
    
    private int id;
    private String username;
    private String password;
    private Date birthday;

}
```

#### 10.1.4 创建OrderMapper接口

```java
public interface OrderMapper {
    List<Order> findAll();
}
```

#### 10.1.5 配置OrderMapper.xml

```xml
<mapper namespace="com.itheima.mapper.OrderMapper">
    <resultMap id="orderMap" type="com.itheima.domain.Order">
        <result column="uid" property="user.id"></result>
        <result column="username" property="user.username"></result>
        <result column="password" property="user.password"></result>
        <result column="birthday" property="user.birthday"></result>
    </resultMap>
    <select id="findAll" resultMap="orderMap">
        select * from orders o,user u where o.uid=u.id
    </select>
</mapper>
```

其中`<resultMap>`还可以配置如下：

```xml
<!--
    property:当前实体（order）中的属性名称
    javaType:当前实体（order）中的属性类型
    -->

<resultMap id="orderMap" type="com.itheima.domain.Order">
    <result property="id" column="id"></result>
    <result property="ordertime" column="ordertime"></result>
    <result property="total" column="total"></result>
    <association property="user" javaType="com.itheima.domain.User">
        <result column="uid" property="id"></result>
        <result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>
    </association>
</resultMap>
```

#### 10.1.6 测试结果

```java
OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
List<Order> all = mapper.findAll();
for(Order order : all){
    System.out.println(order);
}
```

### 10.2 一对多查询

#### 10.2.1 一对多查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询一个用户，与此同时查询出该用户具有的订单

#### 10.2.2 一对多查询的语句

对应的sql语句：select *,o.id oid from user u left join orders o on u.id=o.uid;

#### 10.2.3 修改User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    //代表当前订单从属于哪一个客户
    private User user;
}

public class User {
    
    private int id;
    private String username;
    private String password;
    private Date birthday;
    //代表当前用户具备哪些订单
    private List<Order> orderList;
}

```

#### 10.2.4 创建UserMapper接口

```java
public interface UserMapper {
    List<User> findAll();
}

```

#### 10.2.5 配置UserMapper.xml

```xml
<mapper namespace="com.itheima.mapper.UserMapper">
    <resultMap id="userMap" type="com.itheima.domain.User">
        <result column="id" property="id"></result>
        <result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>
        <collection property="orderList" ofType="com.itheima.domain.Order">
            <result column="oid" property="id"></result>
            <result column="ordertime" property="ordertime"></result>
            <result column="total" property="total"></result>
        </collection>
    </resultMap>
    <select id="findAll" resultMap="userMap">
        select *,o.id oid from user u left join orders o on u.id=o.uid
    </select>
</mapper>
```

#### 10.2.6 测试结果

```java
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
List<User> all = mapper.findAll();
for(User user : all){
    System.out.println(user.getUsername());
    List<Order> orderList = user.getOrderList();
    for(Order order : orderList){
        System.out.println(order);
    }
    System.out.println("----------------------------------");
}
```

### 10.3 多对多查询

#### 10.3.1 多对多查询的模型

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用

多对多查询的需求：查询用户同时查询出该用户的所有角色

![多对多表关系](img\多对多表关系.png)

#### 10.3.2 多对多查询的语句

对应的sql语句：select u.*,r.*,r.id rid from user u left join user_role ur on u.id=ur.user_id

 inner join role r on ur.role_id=r.id;

#### 10.3.3 创建Role实体，修改User实体

```java
public class User {
    private int id;
    private String username;
    private String password;
    private Date birthday;
    //代表当前用户具备哪些订单
    private List<Order> orderList;
    //代表当前用户具备哪些角色
    private List<Role> roleList;
}

public class Role {

    private int id;
    private String rolename;

}

```

#### 10.3.4  添加UserMapper接口方法

```java
List<User> findAllUserAndRole();
```

#### 10.3.5 配置UserMapper.xml

```xml
<resultMap id="userRoleMap" type="com.itheima.domain.User">
    <result column="id" property="id"></result>
    <result column="username" property="username"></result>
    <result column="password" property="password"></result>
    <result column="birthday" property="birthday"></result>
    <collection property="roleList" ofType="com.itheima.domain.Role">
        <result column="rid" property="id"></result>
        <result column="rolename" property="rolename"></result>
    </collection>
</resultMap>
<select id="findAllUserAndRole" resultMap="userRoleMap">
    select u.*,r.*,r.id rid from user u left join user_role ur on u.id=ur.user_id
    inner join role r on ur.role_id=r.id
</select>
```

#### 10.3.6 测试结果

```java
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
List<User> all = mapper.findAllUserAndRole();
for(User user : all){
    System.out.println(user.getUsername());
    List<Role> roleList = user.getRoleList();
    for(Role role : roleList){
        System.out.println(role);
    }
    System.out.println("----------------------------------");
}
```

### 10.4 知识小结

MyBatis多表配置方式：

**一对一配置：使用`<resultMap>`做配置**

**一对多配置：使用`<resultMap>+<collection>`做配置**

**多对多配置：使用`<resultMap>+<collection>`做配置**

---

## 12.Mybatis的注解开发

### 12.1 MyBatis的常用注解

这几年来注解开发越来越流行，Mybatis也可以使用注解开发方式，这样我们就可以减少编写Mapper映射文件了。我们先围绕一些基本的CRUD来学习，再学习复杂映射多表操作。

```java
@Insert：实现新增  
@Update：实现更新
@Delete：实现删除
@Select：实现查询
@Result：实现结果集封装
@Results：可以与@Result 一起使用，封装多个结果集
@One：实现一对一结果集封装
@Many：实现一对多结果集封装
```

### 12.2 MyBatis的增删改查

我们完成简单的user表的增删改查的操作

```java
private UserMapper userMapper;

@Before
public void before() throws IOException {
    InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
    SqlSessionFactory sqlSessionFactory = new 
                 SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    userMapper = sqlSession.getMapper(UserMapper.class);
}

@Test
public void testAdd() {
    User user = new User();
    user.setUsername("测试数据");
    user.setPassword("123");
    user.setBirthday(new Date());
    userMapper.add(user);
}
@Test
public void testUpdate() throws IOException {
    User user = new User();
    user.setId(16);
    user.setUsername("测试数据修改");
    user.setPassword("abc");
    user.setBirthday(new Date());
    userMapper.update(user);
}

@Test
public void testDelete() throws IOException {
    userMapper.delete(16);
}
@Test
public void testFindById() throws IOException {
    User user = userMapper.findById(1);
    System.out.println(user);
}
@Test
public void testFindAll() throws IOException {
    List<User> all = userMapper.findAll();
    for(User user : all){
        System.out.println(user);
    }
}

```

修改MyBatis的核心配置文件，我们使用了注解替代的映射文件，所以我们只需要加载使用了注解的Mapper接口即可

```xml
<mappers>
    <!--扫描使用注解的类-->
    <mapper class="com.itheima.mapper.UserMapper"></mapper>
</mappers>
```

或者指定扫描包含映射关系的接口所在的包也可以

```xml
<mappers>
    <!--扫描使用注解的类所在的包-->
    <package name="com.itheima.mapper"></package>
</mappers>
```

### 2.3 MyBatis的注解实现复杂映射开发

实现复杂关系映射之前我们可以在映射文件中通过配置`<resultMap>`来实现，使用注解开发后，我们可以使用@Results注解，@Result注解，@One注解，@Many注解组合完成复杂关系的配置

![@result注解](img\result注解.png)

![oneMany注](img\oneMany注解.png)

### 12.4 一对一查询

#### 12.4.1 一对一查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对一查询的需求：查询一个订单，与此同时查询出该订单所属的用户

#### 12.4.2 一对一查询的语句

对应的sql语句：

```sql
    select * from orders;
    select * from user where id=查询出订单的uid;
```

#### 12.4.3 创建Order和User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    //代表当前订单从属于哪一个客户
    private User user;
}

public class User {
    
    private int id;
    private String username;
    private String password;
    private Date birthday;

}
```

#### 12.4.4 创建OrderMapper接口

```java
public interface OrderMapper {
    List<Order> findAll();
}
```

#### 12.4.5 使用注解配置Mapper

```java
public interface OrderMapper {
    @Select("select * from orders")
    @Results({
            @Result(id=true,property = "id",column = "id"),
            @Result(property = "ordertime",column = "ordertime"),
            @Result(property = "total",column = "total"),
            @Result(property = "user",column = "uid",
                    javaType = User.class,
                    one = @One(select = "com.itheima.mapper.UserMapper.findById"))
    })
    List<Order> findAll();
}
```

```java
public interface UserMapper {

    @Select("select * from user where id=#{id}")
    User findById(int id);
    
}
```

#### 12.4.6 测试结果

```java
@Test
public void testSelectOrderAndUser() {
    List<Order> all = orderMapper.findAll();
    for(Order order : all){
        System.out.println(order);
    }
}
```

### 12.5 一对多查询

#### 12.5.1 一对多查询的模型

用户表和订单表的关系为，一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询一个用户，与此同时查询出该用户具有的订单

#### 12.5.2 一对多查询的语句

对应的sql语句：

```sql
    select * from user;
    select * from orders where uid=查询出用户的id;
```

#### 12.5.3 修改User实体

```java
public class Order {

    private int id;
    private Date ordertime;
    private double total;

    //代表当前订单从属于哪一个客户
    private User user;
}

public class User {
    
    private int id;
    private String username;
    private String password;
    private Date birthday;
    //代表当前用户具备哪些订单
    private List<Order> orderList;
}
```

#### 12.5.4 创建UserMapper接口

```java
List<User> findAllUserAndOrder();
```

#### 12.5.5 使用注解配置Mapper

```java
public interface UserMapper {
    @Select("select * from user")
    @Results({
            @Result(id = true,property = "id",column = "id"),
            @Result(property = "username",column = "username"),
            @Result(property = "password",column = "password"),
            @Result(property = "birthday",column = "birthday"),
            @Result(property = "orderList",column = "id",
                    javaType = List.class,
                    many = @Many(select = "com.itheima.mapper.OrderMapper.findByUid"))
    })
    List<User> findAllUserAndOrder();
}

public interface OrderMapper {
    @Select("select * from orders where uid=#{uid}")
    List<Order> findByUid(int uid);

}
```

#### 12.5.6 测试结果

```java
List<User> all = userMapper.findAllUserAndOrder();
for(User user : all){
    System.out.println(user.getUsername());
    List<Order> orderList = user.getOrderList();
    for(Order order : orderList){
        System.out.println(order);
    }
    System.out.println("-----------------------------");
}
```

### 12.6 多对多查询

#### 12.6.1 多对多查询的模型

用户表和角色表的关系为，一个用户有多个角色，一个角色被多个用户使用

多对多查询的需求：查询用户同时查询出该用户的所有角色

#### 12.6.2 多对多查询的语句

对应的sql语句：

```sql
    select * from user;
    select * from role r,user_role ur where r.id=ur.role_id and ur.user_id=用户的id
```

#### 12.6.3 创建Role实体，修改User实体

```java
public class User {
    private int id;
    private String username;
    private String password;
    private Date birthday;
    //代表当前用户具备哪些订单
    private List<Order> orderList;
    //代表当前用户具备哪些角色
    private List<Role> roleList;
}

public class Role {

    private int id;
    private String rolename;

}
```

#### 12.6.4 添加UserMapper接口方法

```java
List<User> findAllUserAndRole();
```

#### 12.6.5 使用注解配置Mapper

```java
public interface UserMapper {
    @Select("select * from user")
    @Results({
        @Result(id = true,property = "id",column = "id"),
        @Result(property = "username",column = "username"),
        @Result(property = "password",column = "password"),
        @Result(property = "birthday",column = "birthday"),
        @Result(property = "roleList",column = "id",
                javaType = List.class,
                many = @Many(select = "com.itheima.mapper.RoleMapper.findByUid"))
})
List<User> findAllUserAndRole();}



public interface RoleMapper {
    @Select("select * from role r,user_role ur where r.id=ur.role_id and ur.user_id=#{uid}")
    List<Role> findByUid(int uid);
}

```

#### 12.6.6 测试结果

```java
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
List<User> all = mapper.findAllUserAndRole();
for(User user : all){
    System.out.println(user.getUsername());
    List<Role> roleList = user.getRoleList();
    for(Role role : roleList){
        System.out.println(role);
    }
    System.out.println("----------------------------------");
}
```

## 13 SSM框架整合

### 13.1 原始方式整合

1. 创建数据库
2. 创建Maven工程
3. 导入Maven坐标
4. 编写实体类

    ```java
    public class Account {
        private int id;
        private String name;
        private double money;
        //省略getter和setter方法
    }
    ```

5. 编写Mapper接口

    ```java
    public interface AccountMapper {
        //保存账户数据
        void save(Account account);
        //查询账户数据
        List<Account> findAll();
    }
    ```

6. 编写Service接口

    ```java
    public interface AccountService {
        void save(Account account); //保存账户数据
        List<Account> findAll(); //查询账户数据
    }
    ```

7. 编写Service接口实现

    ```java
    @Service("accountService")
    public class AccountServiceImpl implements AccountService {
        public void save(Account account) {
            SqlSession sqlSession = MyBatisUtils.openSession();
            AccountMapper accountMapper = sqlSession.getMapper(AccountMapper.class);
            accountMapper.save(account);
            sqlSession.commit();
            sqlSession.close();
        }
        public List<Account> findAll() {
            SqlSession sqlSession = MyBatisUtils.openSession();
            AccountMapper accountMapper = sqlSession.getMapper(AccountMapper.class);
            return accountMapper.findAll();
        }
    }
    ```

8. 编写Controller

    ```java
    @Controller
    public class AccountController {
        @Autowired
        private AccountService accountService;
        @RequestMapping("/save")
        @ResponseBody
        public String save(Account account){
            accountService.save(account);
            return "save success";
        }
        @RequestMapping("/findAll")
        public ModelAndView findAll(){
            ModelAndView modelAndView = new ModelAndView();
            modelAndView.setViewName("accountList");
            modelAndView.addObject("accountList",accountService.findAll());
            return modelAndView;
        }
    }
    ```

9. 编写添加页面

    ```html
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h1>保存账户信息表单</h1>
        <form action="${pageContext.request.contextPath}/save.action" method="post">
            用户名称<input type="text" name="name"><br/>
            账户金额<input type="text" name="money"><br/>
            <input type="submit" value="保存"><br/>
        </form>
    </body>
    </html>
    ```

10. 编写列表页面

    ```html
    <table border="1">
        <tr>
            <th>账户id</th>
            <th>账户名称</th>
            <th>账户金额</th>
        </tr>
        <c:forEach items="${accountList}" var="account">
            <tr>
                <td>${account.id}</td>
                <td>${account.name}</td>
                <td>${account.money}</td>
            </tr>
        </c:forEach>
    </table>
    ```

11. 编写相应配置文件(文件参考目录：素材/配置文件)

    •Spring配置文件：[applicationContext.xml](配置文件/applicationContext.xml)

    •SprngMVC配置文件：[spring-mvc.xml](配置文件/spring-mvc.xml)

    •MyBatis映射文件：[AccountMapper.xml](配置文件/AccountMapper.xml)

    •MyBatis核心文件：[sqlMapConfig.xml](配置文件/sqlMapConfig.xml)

    •数据库连接信息文件：[jdbc.properties](配置文件/jdbc.properties)

    •Web.xml文件：[web.xml](配置文件/web.xml)

    •日志文件：[log4j.xml](

12. 测试添加账户
13. 测试账户列表
14. Spring整合MyBatis
    1. 整合思路
    ![Spring整合MyBatis](img\Spring整合MyBatis.png)
    2. 将SqlSessionFactory配置到Spring容器中

        ```xml
        <!--加载jdbc.properties-->
        <context:property-placeholder location="classpath:jdbc.properties"/>
        <!--配置数据源-->
        <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="driverClass" value="${jdbc.driver}"/>
            <property name="jdbcUrl" value="${jdbc.url}"/>
            <property name="user" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
        </bean>
        <!--配置MyBatis的SqlSessionFactory-->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <property name="dataSource" ref="dataSource"/>
            <property name="configLocation" value="classpath:sqlMapConfig.xml"/>
        </bean>
        ```

    3. 扫描Mapper，让Spring容器产生Mapper实现类

        ```xml
        <!--配置Mapper扫描-->
        <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
            <property name="basePackage" value="com.itheima.mapper"/>
        </bean>
        ```

    4. 配置声明式事务控制

        ```xml
        <!--配置声明式事务控制-->
        <bean id="transacionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
            <property name="dataSource" ref="dataSource"/>
        </bean>
        <tx:advice id="txAdvice" transaction-manager="transacionManager">
            <tx:attributes>
                <tx:method name="*"/>
            </tx:attributes>
        </tx:advice>
        <aop:config>
            <aop:pointcut id="txPointcut" expression="execution(* com.itheima.service.impl.*.*(..))"/>
            <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
        </aop:config>
        ```

    5. 修改Service实现类代码

        ```java
        @Service("accountService")
        public class AccountServiceImpl implements AccountService {

            @Autowired
            private AccountMapper accountMapper;

            public void save(Account account) {
                accountMapper.save(account);
            }
            public List<Account> findAll() {
                return accountMapper.findAll();
            }
        }
        ```
