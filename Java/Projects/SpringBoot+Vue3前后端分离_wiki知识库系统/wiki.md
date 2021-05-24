# Spring Boot + Vue3 前后端分离 实战wiki知识库系统

## 遇到问题

1. 跨越访问
2. 雪花算法ID到前端之后精度丢失问题
3. 前端编辑信息是时候相应式修改数据，使用tool.ts添加copy过程

## 基本知识点

* SpringCloud = SpringBoot + 一堆组件
* SpringBoot核心知识体系
    1. AOP  
    2. 拦截器
    3. 过滤器
    4. 异步化
    5. 定时任务
    6. websocket
    7. 多环境
    8. 缓存
    9. 消息队列
* 最火的前端框架：Vue与 Vue CLI（Vue CLI = Vue + 一堆js组件）
* 前端相关技术
  * Vue CLI--代码生成器
  * Vue3--核心部分  
  * Ant Design Vue--最早一批支持Vue3的前端UI组件
  * router--路由
  * axios--
  * vuex--
* WIKI项目流程：设计-->开发-->打包--> 部署
* WIKI项目工程化技术：Git Maven 阿里云
* 开发环境：idea;MySQL;Git;node;jdk8
* 源码仓库学习，打开version control 查看每一步提交

## 重要知识

1. import自动管理
  Settings - search "auto" - Auto Import - 选中Optimize imports on the fly(for current project)
  ----------等价于commit时勾选 Optimize imports
2. Git分开提交
  当改动很多想提交为多次可通过勾选提交文件控制
3. 分支还原
  右击分支 - Reset Current Branch to Here...
4. 数据库划分，开发、测试、生产数据库，各个数据库专用账号(wikidev开发数据库对应wikidev开发者账号）
5. 在进行http  controller接口测试，接口通常做更改，以以判断热部署是否生效，从而在此基础上判断代码。测试成功改回接口名称
6. ctrl + alt + v 快速生成变量
7. Maven出现编译缓存问题，已创建的包却显示不存在
  右侧Maven插件->项目->Lifecycle->clean->build编译（直接重启项目会自动编译，也可以解决）
8. 如果代码改动了，但是出现莫名的错，可以试一下maven clean，比如:
  代码没错却编译出错
  测试结果跟代码不符
  引入了jar包却没反应
9. 修改配置类或pom.xml,建议重启应用，不要热部署

## 两种方式创建项目

1. ![官网创建](start.spring.io)
  Project--Maven；Language--Java;Spring Boot Version--anyone;Project Metadata--Group+Artifact+Name+Packaging+denpendencies
2. IDEA创建
    * 项目创建
      Spring Initializr项目创建
      Spring Boot版本可pom.xml 中 `<parent>`中的`<version>`修改
    * 当下载很慢，**配置Maven镜像**
      Setting - Maven - User settings file - 修改settings.xml
    * SpringBoot默认配置
      * tomcat默认为嵌入式容器
    * 目录结构
      * Maven
        maven wrapper可以自动下载maven，实际上我们常用IDEA自带的maven

        ```text
          ├── .mvn 

          │   └── wrapper 

          │       ├── maven-wrapper.jar 

          │       └── maven-wrapper.properties 

          ├── mvnw --linux命令

          └── mvnw.cmd--windows命令
        ```

      * src

        ```text
          ├── main

          │   ├── java

          │   └── resources -- 配置文件，包括前端文件

          │      ├── staic  -- js css

          │      ├── templates -- Spring Boot官方推荐前端用Thymeleaf，不过我们是前后端分离，用Vue CLI，使用最新Vue3，“前后端分离，staic 和 templates 一般可以删除”

          │      └── application.properities
 
          └── test —— java 
        ```

      * .gitignore -- git提交忽略文件目录
      * HELP.md / README.md -- 对项目的描述
      * pom.xml -- Maven核心文件，Maven管理JAR包通过该文件管理
      * wiki.iml -- 项目/模块特有文件，不需要提交Git

## 项目配置

* 编码配置 utf-8
  IDEA - Setting - search "encode" - File Encodings + SSH Terminal
* JDK配置
  File - Projet Structure - Project
* Maven配置
  IDEA - Setting - search "Maven" - Maven home directory定制自己的Maven仓库 + User settings file 配置文件换源

  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
                http://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository>C:\Users\甲蛙\.m2\repository--仓库路径</localRepository>
      <pluginGroups></pluginGroups>
      <proxies></proxies>
      <servers></servers>
      <mirrors>
        <mirror>
          <id>alimaven</id>
          <mirrorOf>central</mirrorOf>
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/repositories/central</url>
        </mirror>
        <mirror>
          <id>alimaven</id>
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public</url>
          <mirrorOf>central</mirrorOf>
          </mirror>
        <mirror>
          <id>central</id>
          <name>Maven Repository Switchboard</name>
          <url>http://repo1.maven.org/maven2</url>
          <mirrorOf>central</mirrorOf>
        </mirror>
        <mirror>
          <id>repo2</id>
          <mirrorOf>central</mirrorOf>
          <name>Human Readable Name for this Mirror.</name>
          <url>http://repo2.maven.org/maven2</url>
        </mirror>
        <mirror>
          <id>ibiblio</id>
          <mirrorOf>central</mirrorOf>
          <name>Human Readable Name for this Mirror.</name>
          <url>http://mirrors.ibiblio.org/pub/mirrors/maven2</url>
        </mirror>
        <mirror>
          <id>jboss-public-repository-group</id>
          <mirrorOf>central</mirrorOf>
          <name>JBoss Public Repository Group</name>
          <url>http://repository.jboss.org/nexus/content/groups/public</url>
        </mirror>
        <mirror>
          <id>google-maven-central</id>
          <name>Google Maven Central</name>
          <url>https://maven-central.storage.googleapis.com
          </url>
          <mirrorOf>central</mirrorOf>
        </mirror>
        <!-- 中央仓库在中国的镜像 -->
        <mirror>
          <id>maven.net.cn</id>
          <name>one of the central mirrors in china</name>
          <url>http://maven.net.cn/content/groups/public</url>
          <mirrorOf>central</mirrorOf>
        </mirror>
      </mirrors>
      <profiles>
        <profile>
          <id>jdk-1.8</id>
          <activation>
            <activeByDefault>true</activeByDefault>
            <jdk>1.8</jdk>
          </activation>
          <properties>
            <maven.compiler.source>1.8</maven.compiler.source>
            <maven.compiler.target>1.8</maven.compiler.target>
            <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
          </properties>
        </profile>
        <profile>
          <id>repository_set</id>
          <repositories>
            <repository>
              <snapshots>
              <enabled>false</enabled>
              </snapshots>
              <id>public</id>
              <name>Public Repository</name>
              <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            </repository>
          </repositories>
          <pluginRepositories>
            <pluginRepository>
              <releases>
                <updatePolicy>never</updatePolicy>
              </releases>
              <snapshots>
              <enabled>false</enabled>
              </snapshots>
              <id>public</id>
              <name>Public Repository</name>
              <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            </pluginRepository>
          </pluginRepositories>
        </profile>
      </profiles>
    </settings>
  ```

* Git配置
  * 开启:VCS - Enable Version Control Integration - Git
  * 基本介绍:
    * 红色：表还没交给Git管理
    * 蓝色：作过修改，还未提交
    * 绿色：Git已管理，还未提交
    * 灰色：文件删除，还未提交
  * 本地工作空间相关的文件不必提交，.gitignore忽略
    * .idea
    * *.iml
    * tartget
  * 设置
    * 提交时候取消Perform code analysis 和 Check TODO(Show All),减少检查耗时
* 项目异常-解决
  * 关闭项目
  * 删除根目录下的.idea文件夹
  * 重新打开项目

## 代码关联Git远程仓库

* 步骤
  1. 创建远程仓库
  2. SSH 用户名密码连接
  3. 本地仓库创建
  4. 远程仓库进行关联
* 配置方法：1. SSH 2. 用户名密码
* SSH方法--推荐
  * 本地主机生成密钥：Git GUI Here - Help --Show SSH Key
  * 远程仓库增加密钥
* 本地仓库创建并推送命令

  ```git
    touch README.md
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin ssh://git@git.imooc.com:80/coding-474/jiawawiki.git
    git push -u origin master
  ```

* 推送已创建的仓库命令
  
  ```git
    git remote add origin ssh://git@git.imooc.com:80/coding-474/jiawawiki.git
    git push -u origin master
  ```

* Git工具栏配置
  Settings - search "menu" - Menus and Toolbars -  Navigation Bar Toolbar - NavBarVcsGroup - add Action - Version Control Systems
* 创建README.md 删除HELP.md（远程仓库不识别HELP.md）
* 设置新增文件自动被Git管理

## logbback日志样式修改

* 日志等级
  logback 5个日志级别 TRACE < DEBUG < INFO < WARN < ERROR

* 版本问题
  2.2.X版本，可以识别logback.xml.2.3以后只能用logback-spring.xml
  放置目录：resources下，自动识别无需配置

* logback-spring.xml作用
  * 修改日志样式
  * 日志输出到文件

* logback-spring.xml文件模板

  ```xml
    <!--注意：如果是Mac，路径要改为"\"-->
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <!-- 修改一下路径-->
      <property name="PATH" value="./log"></property>

      <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
    <!-- <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %highlight(%-5level)
  %blue(%-50logger{50}:%-4line) %thread %msg%n</Pattern>-->
          <Pattern>%d{ss.SSS} %highlight(%-5level) %blue(%-30logger{30}:%-4line) %thread
  %msg%n</Pattern>
        </encoder>
      </appender>

      <appender name="TRACE_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${PATH}/trace.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
          <FileNamePattern>${PATH}/trace.%d{yyyy-MM-dd}.%i.log</FileNamePattern>
          <timeBasedFileNamingAndTriggeringPolicy
          class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>10MB</maxFileSize>
          </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <layout>
          <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %-50logger{50}:%-4line
        %green(%-18X{LOG_ID}) %msg%n</pattern>
        </layout>
      </appender>

      <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${PATH}/error.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
          <FileNamePattern>${PATH}/error.%d{yyyy-MM-dd}.%i.log</FileNamePattern>
          <timeBasedFileNamingAndTriggeringPolicy
          class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>10MB</maxFileSize>
          </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <layout>
          <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %-50logger{50}:%-4line
        %green(%-18X{LOG_ID}) %msg%n</pattern>
        </layout>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
          <level>ERROR</level>
          <onMatch>ACCEPT</onMatch>
          <onMismatch>DENY</onMismatch>
        </filter>
      </appender>

      <root level="ERROR">
        <appender-ref ref="ERROR_FILE" />
      </root>

      <root level="TRACE">
        <appender-ref ref="TRACE_FILE" />
      </root>

      <root level="INFO">
        <appender-ref ref="STDOUT" />
      </root>
    </configuration>

  ```

## 修改启动图案

* 新增banner.txt 于resources下
* ![网址](http://patorjk.com/software/taag)获取图案txt

## 增加启动成功日志

* 作用
  打印自定义配置信息，打印欢迎页，测试地址，或者提示性方案等等

* application.xml部分模板

  ```java
    public class WikiApplication {
      private static final Logger LOG = LoggerFactory.getLogger(WikiApplication.class);
      public static void main(String[] args){
        SpringApplication app = new SpringApplication(WikiApplication.class);
        Environment env = app.run(args).getEnvironment();
        LOG.info("启动成功！");
        LOG.info("地址：\t http://127.0.0.1:{}",env.getProperty("server.port"));
      }
    }
  ```

## 代码分层

1. controller 接口层，请求接口
2. domain/entity/POJO 实体类层，与数据库表一一映射
3. Mapper/Dao(早期) 持久层，用Mapper原因是官方代码生成器生成代码XXXMapper
4. mapper SQL脚本，位于resources下，与持久层接口对应

## Controller层

controller层用于定义接口，是请求的入口

### 注解

1. @RestController注解用于声明返回文本数据，一般返回JSON
2. @Controller注解用于声明返回界面
3. @RestController = @Controller + ResponseBody

### RESTful风格

* 格式改变：/user?id=1 --> /user/1
* RESTful对应增删改查请求GET,POST,PUT,DELETE
* 实现方式
  * GET
    * @GetMapping("/")
    * @ResquestMapping(value = "/",method = RequestMethod.GET)
  * POST
    * PostMapping("/")
    * @ResquestMapping(value = "/",method = RequestMethod.POST)
  * PUT
    * PutMapping("/")
    * @ResquestMapping(value = "/",method = RequestMethod.PUT)
  * DELETE
    * DeleteMapping("/")
    * @ResquestMapping(value = "/",method = RequestMethod.DELETE)

## 请求status码

* 404 不存在该请求接口
* 405 请求方法不支持

## 启动类移动路径

* 具体操作:启动类移动到包内部，比如放入config文件夹中
* 启动类扫描各层的注解原理
  WikiApplication.java 启动类中有@SpringBootApplication注解
  @SpringBootApplication注解中含有@ComponentScan扫描注解，扫描启动类同级的所有包
* 问题解决：@ComponentScan 启动类中手动导入包 @ComponentScan("com.hopkin")
* 扫描多个包：@ComponentScan({"com.hopkin","com.lhp"})

## 插件工具HTTP Client测试接口

* HTTP Client 优点
  省去了窗口切换，测试更流畅
  IDEA会自动识别xxx.http文件
* 使用
  Live Templates - HTTP Request
* 常用Live Templates
  * gtr - get请求不带参数
  * gtrp - get请求带参数
  * ptr - post请求不带参数
  * ptrp - post请求带参数
* 附加内容
  * 参数传递
    写在请求下方，形式为 name=lhp 的.properties形式
  * **结果验证**
    * 结果验证脚本模板(***>和{之间空格***)

      ```http
        > {%
          client.test("test-api",function(){
            client.log("测试 /api 接口");
            client.log(response.body);
            client.log(JSON.stringify(response.body));//虽然IDEA没有提示JSON，但是可以用
            client.assert(response.status === 200,"返回码不是200");
            client.assert(response.body.id ==== "return responsebody","结果验证失败");
          });
        %}
      ```

    * 细节介绍
      * 若返回一个值，则使用response.body
      * 若返回JSON，则response.body.id获取、
      * 绿色的钩即为test成功通过
* 接口测试记录路径
  .idea - httpRequests

## SpringBoot配置文件

### 默认支持的配置文件（复写默认配置）

* 细节介绍
  * 识别文件类型 .properties .yml
  * 识别路径 resources/   resoutces/config/
  * 自动识别特定名称的配置文件(6种，bootstrap用于SpringCloud)
    * resources/application.properties
    * resources/config/application.properties
    * resources/application.yml
    * resources/config/application.yml
    * resources/bootstrap.yml
    * resources/config/bootstrap.properties
* bootstrap.yml注意
  单个SpringBoot不会读bootstrap配置，要求SpringCloud架构下的SpringBoot应用才会读取。bootstrap一般用于动态配置，线上可以实时修改实时生效的配置，一般可配合上nacos使用
* Properties与YAML文件转换(数组类型很复杂)
  [格式转换网址](toyaml.com/index.html)
* 优先级问题....

### 自定义配置项（创建新配置项）

@Value("${配置项:TEST}")-- SPTL读取配置项内容,默认值TEST

## 热部署

* 缘由：修改配置后，build -> run 修改的内容没有体现
* 热部署步骤三步
  1. 导入依赖

      ```xml
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
      </dependency>
      ```
  
  2. 静态：IDEA 设置Compiler 勾选 Build project automatically
  3. 动态：连续两次shift 或者 工具栏Help - Find Action ,search "Registry",勾选compiler.automake.allow.when.app.running
* 触发保存 ctrl + s
* 热部署通常用法:修改代码 -> Build project -> 等待1-2秒访问

## 数据库创建

* 步骤
  1. 创建数据库 字符集utf8mb4(可以放置表情符号)
  2. 创建专用账户，root权限太大
      * localhost 本地访问
      * % 外网访问
  3. 配置权限 - 添加权限（选中快捷键 shirt + space）
  4. 可视化工具以专用账号创建连接
* 细节介绍
  * 连接命名规范：databaseName@localhost
  * 云服务：阿里云RDS先按月购买，最终和ECS部署完成后再按年，ECS和RDS访问有地域限制
  * 专库专用：每个数据库配置单独的用户

## IDEA数据库插件配置

* 步骤
  1. IDEA Database插件(右侧，或者左下角可打开)
  2. 添加 Database MySQL连接
  3. 新建doc项目文档文件夹，all.sql文件，db文件夹存流程图和图片等
  4. all.sql文件 选中后右击Execute
* 报错
  * Server returns invalid timezone. Go to 'Advanced' tab and set 'serverTimezone' prope
    * 解决方法为：设置mysql的时区（可能需要管理员方式运行）
      1. 进入mysql
      2. show variables like'%time_zone';
      3. set global time_zone = '+8:00';
* 细节处理
  * Data Source 命名 ： 用户名@localhost/RDS
  * 可视化界面修改要 DB提交事务
  * all.sql使用注意：先选择操作的数据库和console
* 优点
  字段和表的提示信息

## 集成Mybatis

* 常见的持久层框架
  * Mybatis
    半自动--自己写SQL
  * Hibernate
    全自动--不需要写SQL
* 使用步骤
  1. 导入pom.xml依赖

      ```xml
      <!-- 集成MyBatis-->
      <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>2.1.4</version>
      </dependency>
      <!-- 集成MySQL连接-->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
      </dependency>
      ```

  2. DataSource数据源配置（不然运行报错）

      ```properties
      # 增加数据库连接
      spring.datasource.url=jdbc:mysql://localhost:3306/wiki?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf8&useSSL=false
      spring.datasource.username=wiki
      spring.datasource.password=131415
      spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
      ```

  3. 编写实体类，持久层Mapper接口，Mapper层对应的SQL脚本（放在Resources/mapper下，取名和持久层Mapper接口层相同，为.xml文件）
    SQL脚本模板.xml
  
      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

      <!--对应持久层Mapper接口-->
      <mapper namespace="com.hopkin.wiki.mapper.TestMapper" >
        <select id="list" resultType="com.jiawa.wiki.domain.Test">
            select `id`, `name`, `password` from `test`
        </select>
      </mapper>
      ```

  4. Free MyBatis plugin 插件将Mapper接口与SQL关联，实现代码跳转
  5. 持久层Mapper识别,配置mybatis所有Mapper.xml所在的路径
    启动类包扫描  `@MapperScan("com.hopkin.wiki.mapper")`
  6. mapper SQL脚本识别
    application.properties 配置mybatis 所有Mapper.xml所在的路径

      ```properties
      # 配置mybatis 所有Mapper.xml所在的路径
      mybatis.mapper-locations=classpath:/mapper/**/*.xml
      ```

  7. 三层架构组装：service编写调用Mapper接口，Controller调用service
    使用@Service或@RestController注解，将Service类或Controller类交给Spring来管理了
    使用@Resource或@Autowire将一个类注入到另一个类中
  8. http client 请求测试
* 报错
  * 500 status  ERROR c.z.hikari.pool.HikariPool
    java.sql.SQLNonTransientConnectionException: Could not create connection to database server.
    时区问题：spring.datasource.url配置 加上&serverTimezone=Asia/Shanghai
  * org.springframework.jdbc.CannotGetJdbcConnectionException
    数据库连接未连接上，检查用户和密码

## MyBatis Generator 官方代码生成器

* 优点：**单表** 增删改查不要自己写代码
* 步骤
  1. 导入pom插件依赖

      ```xml
      <!-- mybatis generator 自动生成代码插件 -->
      <plugin>
          <groupId>org.mybatis.generator</groupId>
          <artifactId>mybatis-generator-maven-plugin</artifactId>
          <version>1.4.0</version>
          <!--配置文件-->
          <configuration>
              <configurationFile>src/main/resources/generator/generator-config.xml</configurationFile>
              <overwrite>true</overwrite>
              <verbose>true</verbose>
          </configuration>
          <!--数据库连接依赖-->
          <dependencies>
              <dependency>
                  <groupId>mysql</groupId>
                  <artifactId>mysql-connector-java</artifactId>
                  <version>8.0.22</version>
              </dependency>
          </dependencies>
      </plugin>
      ```

  2. 创建mybatis generator配置文件
    src/main/resources/generator/generator-config.xml
    **模板中主要修改connection的连接参数和包的路径，table标签中对应的表名**

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE generatorConfiguration
          PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
          "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

      <generatorConfiguration>
        <context id="Mysql" targetRuntime="MyBatis3" defaultModelType="flat">

          <!-- 自动检查关键字，为关键字增加反引号 -->
          <property name="autoDelimitKeywords" value="true"/>
          <property name="beginningDelimiter" value="`"/>
          <property name="endingDelimiter" value="`"/>

          <!--覆盖生成XML文件-->
          <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin" />
          <!-- 生成的实体类添加toString()方法 -->
          <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>

          <!-- 不生成注释 -->
          <commentGenerator>
              <property name="suppressAllComments" value="true"/>
          </commentGenerator>
           <!-- 配置数据库连接 -->
          <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                          connectionURL="jdbc:mysql://rm-uf6470s9615e13hc4no.mysql.rds.aliyuncs.com:3306/wikidev?serverTimezone=Asia/Shanghai"
                          userId="wikidev"
                          password="131415">
          </jdbcConnection>

          <!-- domain类的位置 -->
          <javaModelGenerator targetProject="src\main\java"
                              targetPackage="com.hopkin.wiki.domain"/>

          <!-- mapper xml的位置 -->
          <sqlMapGenerator targetProject="src\main\resources"
                          targetPackage="mapper"/>

          <!-- mapper类的位置 -->
          <javaClientGenerator targetProject="src\main\java"
                              targetPackage="com.hopkin.wiki.mapper"
                              type="XMLMAPPER"/>

          <table tableName="demo" domainObjectName="Demo"/>
          <!--<table tableName="ebook"/>-->
          <!--<table tableName="category"/>-->
          <!--<table tableName="doc"/>-->
          <!--<table tableName="content"/>-->
          <!--<table tableName="user"/>-->
          <!--<table tableName="ebook_snapshot"/>-->
        </context>
      </generatorConfiguration>

      ```

  3. 新增Maven启动命令
    name:简洁明了，形如：mybatis-generator
    Command line:mybatis-generator:generate -e
  4. 执行命令，生成四个文件
* 生成文件介绍(Demo表)
  1. Demo.java(com/hopkin/wiki/domain)
  2. DemoExample.java(com/hopkin/wiki/domain)
  3. DemoMapper.java(com/hopkin/wiki/**mapper**)
  4. DemoMapper.xml(**resources/mapper**)
* MyBatis Generator生成代码的使用
  1. 编写service层（持久层已经生成，复制其他已经写好的service，进行查找并替换实体类名称。**大小写匹配设置**，先替换首字母大小的，后替换首字母小写的，共两次替换）
  2. 调用mapper接口的函数，其中Example类型相当于SQL查询的where后的条件
  3. 编写controller层，同service层复制，查找替换（**不能勾选word,只替换同一个单词**，而是替换所有）
* 报错解决
  * generatorConfig.xml的头文件`http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd`标红
    左边有红色小灯泡，点击Fetch external resource获取外部资源，即可解决
  * Access denied for user 'wikidev'@'123.147.250.63'
    看到有连接具体服务器报错，在配置文件中服务器地址未改未localhost
  * 成功build,但是为生成4个相应文件
    generator-config.xml配置文件中未配置table标签
  * 配插件需要版本号

## 电子数列表查询接口开发

* 步骤
  1. 执行SQL，创建数据表和插入信息
  2. 修改generator-config.xml的table部分，执行 mybatis generator，生成相应的表的持久层代码
  3. service、controller编写
  4. http 测试

## 统一后端返回值类型

* 目的：后端会有很多的接口，为了让前端能够统一处理逻辑（登录校验、权限校验），需要统一后端的返回值
* 步骤
  1. 新建文件夹 com.hopkin.wiki.**resp**
  2. 新建通用返回类 CommonResp.java
    主要部分：业务上的成功或失败；返回信息；返回泛型数据，自定义类型

      ```java
      public class CommonResp<T> {

        /**
        * 业务上的成功或失败
        */
        private boolean success = true;

        /**
        * 返回信息
        */
        private String message;

        /**
        * 返回泛型数据，自定义类型
        */
        private T content;

        public boolean getSuccess() {
            return success;
        }

        public void setSuccess(boolean success) {
            this.success = success;
        }

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }

        public T getContent() {
            return content;
        }

        public void setContent(T content) {
            this.content = content;
        }

        @Override
        public String toString() {
            final StringBuffer sb = new StringBuffer("ResponseDto{");
            sb.append("success=").append(success);
            sb.append(", message='").append(message).append('\'');
            sb.append(", content=").append(content);
            sb.append('}');
            return sb.toString();
        }
      }

      ```
  
  3. 对应controller层注入Service对象
  4. 将已有接口的返回值类型更改为通用类型
    具体代码：

      ```java
      @GetMapping("")
      public CommonResp list(){
        CommonResp< List<Ebook>  > resp = new CommonResp<>();
        List<Ebook> list = ebookService.list();
        //resp的success属性默认true
        resp.setContent(list);
      }
      ```

  5. http测试，前端通过"succcess"值进行相应处理
* 注意
  实际工作中，有些项目会在CommonResp中加上其它通用的属性，比如接口版本号、返回码等。

## 封装请求参数和返回参数

* 优点：由于需求迭代，接口传参/数据返回时候需要变更，通常为增加。一般会把传进来的所有参数封装成一个类。（最主要原因，我们不会去动和数据表对应好的实体类，跟代理一样，我们要实现功能会另外在原先的基础上改）
* 请求参数封装步骤
  1. 新建包 com.hopkin.wiki.**req**
  2. 新建请求参数封装类，实体类+Req命名，如EbookReq.java
  3. EbookReq.java是实体类Ebook做一些修改而成（变量、gettersetter、toString三部分中）
  4. 修改controller层和service层的传参
  5. http测试（改动service代码，保证热部署生效，之后改回去），参数名字和属性相同即spring自动映射
* 返回参数封装步骤
  1. 新建包 com.hopkin.wiki.**resp**--与CommonResp同包
  2. 新建返回参数封装类，实体类+Resp命名，如EbookResp.java(通常返回参数封装类与实体类相同)
  3. 按需求修改返回参数封装类（变量、gettersetter、toString三部分中）
  4. 持久层返回实体类，service需要转换成返回参数封装类---将实体类的所需属性设置到返回参数封装类
  **BeanUtils.copyProperties(Object source,Object target)**--工具类简化复制
  5. http测试（改动service代码，保证热部署生效，之后改回去）
* 细节
  * Spring将传入参数自动映射到请求参数类中**相同命名**的属性

## 模糊查询

* 步骤
  1. 创建Example对象，相当于where查询条件
  2. criteria调用查询方法
  3. criteria参数配置通配符
  4. 调用mapper接口中方法时候传入Example对象
  5. http测试 以 **?** 连接参数

```java
  //Example生成的固定代码
  EbookExample ebookExample = new EbookExample();
  EbookExample.Criteria criteria = ebookExample.createCriteria();
  criteria.andNameLike("%"+req.getName()+"%");
  List<Ebook> ebookList = ebookMapper.selectByExample(ebookExample);
```

## 制作CopyUtil封装BeanUtils

* 原因：工具类制作的本质是将复制重复的代码抽象出来
* 主要知识：泛型
* 主要内容: 单体复制和列表复制
* 步骤
  * 新建包com.hopkin.wiki.**utils**
  * 编写CopyUtil类

    ```java
    import org.springframework.beans.BeanUtils;
    import org.springframework.util.CollectionUtils;

    import java.util.ArrayList;
    import java.util.List;

    public class CopyUtil {

        /**
        * 单体复制
        */
        public static <T> T copy(Object source, Class<T> clazz) {
            if (source == null) {
                return null;
            }
            T obj = null;
            try {
                obj = clazz.newInstance();
            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
            BeanUtils.copyProperties(source, obj);
            return obj;
        }

        /**
        * 列表复制
        */
        public static <T> List<T> copyList(List source, Class<T> clazz) {
            List<T> target = new ArrayList<>();
            if (!CollectionUtils.isEmpty(source)){
                for (Object c: source) {
                    T obj = copy(c, clazz);
                    target.add(obj);
                }
            }
            return target;
        }
    }

    ```

  * 使用

    ```java
    //单体复制使用
    //EbookResp ebookResp = new EbookResp();
    //BeanUtils.copyProperties(ebook,ebookResp);
    EbookResp ebookResp = CopyUtil.copy(ebook,EbookResp.class);

    //列表复制使用
    /*
    List<Ebook> ebookList = ebookMapper.selectByExample(ebookExample);
    ArrayList<EbookResp> respList = new ArrayList<>();    
    for(Ebook ebook: ebookList){
        EbookResp ebookResp = new EbookResp();
        BeanUtils.copyProperties(ebook,ebookResp);
        respList.add(ebookResp);
    }
    */
    CopyUtil.copyList(ebookList,EbookResp.class)

    ```

## Vue和Vue CLI

Vue.js使用方法和jQuery.js有点像，只需要引入vue.js就可以使用。jQuery用起来更像是个工具，Vue是个框架，我们写的代码被这个框架使用。

* Vue.js使用方法：
  * CDN引入（依赖别人，不占自己的带宽、流量。可能导致网站打开很慢）

    ```html
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    ```

  * 本地文件引入Vue.js
    CDN变本地的通用方法：访问地址，把内容复制到本地，再引入本地文件
* Vue.js的使用场景
  jsp、thymeleaf等老项目
* Vue核心功能：数据绑定  

* Vue CLI
  * 介绍
    Vue CLI = Vue.js + 一堆插件
  * 局限
    * Vue CLI是一个基于Vue.js进行快速开发的**完整**系统，无法用于老项目
    * Vue CLI 和 Vue 版本不同步，Vue CLI 4.5.X 才支持 Vue3
  * 使用
    1. 安装Node.js
    2. npm配置淘宝的镜像网址

        ```shell
        # 显示当前的镜像网址
        npm get registry
        # 使用淘宝的镜像网址
        npm config set registry http://registry.npm.taobao.org
        # 最初的源
        https://registry.npmjs.org/
        


        ```

    3. npm命令安装Vue CLI

        ```shell
        # 默认安装最新的正式版，升级Vue CLI时，已创建好的应用不受影响
        # 指定版本安装 npm install -g @vue/cli@4.5.9
        npm install -g @vue/cli
        # OR
        yarn global add @vue/cli
        ```

    4. 查看vue版本

        ```shell
        vue --version
        ```

    5. 创建web应用
      要求定位在项目目录下创建web应用，IDEA Terminal默认在项目内

          ```shell
          vue create web
          ```

    6. 创建web应用信息选择
        * Manually select features(手动)
        * 空格选中features(choose Vue version;Babel;TypeScript;Router路由;Vuex保存全局变量;Linter/Formatter代码校验)
        * 版本选择 3.x(Preview)
        * class-style component syntax(java类形式)--NO
        * Babel alongside TypeScript(Babel配合TypeScript？) --- NO 直接使用TypeScript
        * history mode for router(地址栏含#的方式) -- Yes
        * ESLint 校验规则 -- ESLint with error prevention only
        * additional lint features(何时触发检查)-- Lint on save
        * where place config(router等配置是放在单独的文件还是以前放到package.json) -- In dedicated config files
        * save as a preset(以上配置是否保存为模板) -- Yes
        * save preset as name (模板名)
    7. 启动

        ```command
        cd web
        npm run serve
        ```

    8. 设置IDEA窗口按钮启动npm命令
      web->package.json->右击Show npm Scripts->双击serve(npm run serve)->习惯最左边的npm窗口拖到右边

## Vue CLI项目结构讲解

* 结构

   ```text
   web —— ├── node_modules  放整个项目依赖的js模块，类似于Maven的jar包依赖
          │
          ├── dist  build编译后出现，部署时候用到
          │
          ├── public  public不参与打包
          │    ├── favicon.ico  浏览器小图标
          │    └── index.html  项目启动的首页
          │
          ├── src Vue项目代码
          │     ├── assets  静态资源,assets会被打包
          │     ├── components 组件
          │     ├── router  路由   
          │     ├── store 存全局数据
          │     ├── views 页面，与路由对应
          │     │ 
          │     ├── App.vue 初始内容页面
          │     ├── main.ts 初始启动（配置）文件 -- 配置引用的插件
          │     └── shims-vue.d.ts  定义文件，一般不需要管
          ├── .browserslistrc 浏览器兼容，不需要管
          ├── .eslintrc.js  语法检查插件，规则校验，按需修改
          ├── .gitignore  配置不需要给git管理的文件，任何目录都可以添加该文件
          ├── .package.json 相当于后端pom.xml，导入依赖
          ├── .package-lock.json  锁定版本号，可以设置下载最新的小版本
          ├──  README.md  Git说明文件，子目录的该文件无用,README.md只有根目录下的生效
          └──  tsconfig.json 整个项目的配置文件，不用管
        ```

* 主要使用的目录
  * src:项目开发主要集中的地方
  * src/view：写页面
  * src/components:写组件
  * src/router:写路由
  * src/store:加全局变量
  * src/assets：加静态资源
* 运行逻辑
  Vue CLI初始执行main.ts,将内容页App.vue渲染到index.xml,完成页面显示
* 细节注意
  * 静态资源引用：assets中用src相对路径，public中用BASE_URL引入
  * Vue CLI需要编译才能发布（npm窗口中的build按钮）
    `<link rel = "icon" href="<%= BASE_URL %>favicon.ico">`

## 集成Ant Design Vue

* Vue前端编写
  * 法一：基于原生html css js
  * 法二：基于第三方css库，如bootstrap
  * 法三：基于Vue的UI组件，Element UI（Vue2），ant-design-vue(Vue3)
* ant-design-vue使用  ![官网](https://2x.antdv.com/docs/vue/introduce-cn)
  1. 安装脚手架工具vue-cli

      ```cmd
      npm install -g @vue/cli
      # OR
      yarn global add @vue/cli
      ```
  
  2. 创建项目 `vue create antd-demo`
  3. IDEA安装ant-design-vue

      ```shell
      cd web
      # 最新正式版
      npm install ant-design-vue --save
      # 安装最新的未发布的版本
      npm install ant-design-vue@next --save
      # 安装固定版本
      npm install ant-design-vue@2.0.0-rc.3 --save
      ```

  4. 完整引入

      ``` js
      import { createApp } from 'vue';
      import Antd from 'ant-design-vue';
      import App from './App';
      import 'ant-design-vue/dist/antd.css';

      const app = createApp();
      app.config.productionTip = false;

      app.use(Antd);
      ```

  5. 局部导入组件

      ```js
      import { createApp } from 'vue';
      import { Button, message } from 'ant-design-vue';
      import App from './App';

      const app = createApp();
      app.config.productionTip = false;

      /* Automatically register components under Button, such as Button.Group */
      app.use(Button);

      app.config.globalProperties.$message = message;
      ```

  6. 按需加载

      ```js
      import Button from 'ant-design-vue/lib/button';
      import 'ant-design-vue/lib/button/style'; // 或者 ant-design-vue/lib/button/style/css 加载 css 文件
      ```

  7. 使用了 babel，`babel-plugin-import`插件简化加载代码（可以不使用）
  8. 导入组件并运行

* 细节处理
  * 安装错误版本 选中package.json和package-lock.json右击回退revert
  * use(Antd)保错，版本问题，要安装最新的非正式版才支持Vue3

## 网站首页布局开发

* 最重要部分：**路由**
* 步骤
  1. 分析网站布局
  2. 选定Ant Design Vue布局![Layout 布局](https://2x.antdv.com/components/layout-cn),选定一个template（lay-out、js、css）
  3. 编写前端页面
    将布局写在App.vue,将各页面变化的部分放到各自的路由，打开Structure窗口查看结构，逐一添加小的布局，和对应的css样式
  4. 改造布局，调整为自己需要的样子
  5. 首页路由（路由由router中控制）

      ```html
      <!--link跳转标签，点击后将对应to中的页面显示在<router-view>标签处
      当不配置link，默认导入路由为"/"的主页路由-->
      <router-link to="/">click</router-link> 
      <router-view>
      ```

  6. 修改eslintrc组件校验
* 细节注意
  * 组件报错因为定义却未使用 vue/no-unused-components
    方法为取消该组件或是.eslintrc.js中设置取消该规则
  * 报错后修改代码无法进行热部署

## 制作Vue自定义组件

* 将所有页面都有的部分提取为公共的组件，将header和footer提取成组件
* 步骤
  1. 在components新增the-header.vue
    将原来app.vue内相应的组件的代码拷贝到此处
  2. 组件script编写，参考HelloWorld.vue编辑script部分。

      ```js
      // HelloWorld.vue 组件 的 script
      <script lang="ts">
        import {defineComponent} from 'vue';

        export default defineComponent({
          // 组件名称
          name: 'HelloWorld',
          // props用于父子组件之间传递数据，参考HelloWorld的写法
          props: {
            msg: String,
          },
        });
      </script>
      ```

  3. 参考Home.vue引入HelloWorld组件，将对应script粘贴到app.vue中在app.vue的组件部分替换为新增的组件
      * import 组件页面
      * name改为当前文件名
      * components加入该组件
      * 在template中使用组件

      ```js
      // app.vue 的 script   
      <script lang="ts">
        import {defineComponent} from 'vue';
        // 下面导入的组件名不能有'-'
        import HelloWorld from '@/components/HelloWorld.vue';

        export default defineComponent({
          name: 'App',
          components: {
            HelloWorld,
          },
        });
      </script>
      ```

  4. 删除HelloWorld.vue与项目无关的代码
* 细节注意
  * import 组件名不能出现'-'
  * F12 Maximum call...循环引用报错
    栈溢出，常出现在递归，组件嵌套等
    将script中的name改为当前文件
  * 从已有组件复制script到app.vue页面时候，需要将name改为当前页面
  * 自定义组件建议加the-component
  * 要求script中import的组件名和components中的组件名相同
  * import组件名时用对应组件script中的name

## 集成HTTP库axios

也可以选择集成jQuery来实现http请求

* 步骤
  1. Terminal进入WEB项目目录
  2. 安装axios

    ```shell
    cd web
    npm install axios  --save
    npm install axios@0.21.0  --save //设定版本号
    ```

## 配置跨域访问

* 步骤
  1. 在com.hopkin.wiki中新增config包
  2. 编写CorsConfig类

    ```java
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.cors.CorsConfiguration;
    import org.springframework.web.servlet.config.annotation.CorsRegistry;
    import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

    @Configuration
    public class CorsConfig implements WebMvcConfigurer {

      @Override
      public void addCorsMappings(CorsRegistry registry) {
          registry.addMapping("/**") //针对所有的接口
                  .allowedOriginPatterns("*") //允许来源
                  .allowedHeaders(CorsConfiguration.ALL)
                  .allowedMethods(CorsConfiguration.ALL) //get post delet...
                  .allowCredentials(true) // 允许前端携带凭证，如cookie
                  .maxAge(3600); // 1小时内不需要再预检（发OPTIONS请求）在get之前有options请求判断接口的存在
      }
    }
    ```

## 电子书列表接口的前后端交互

* 步骤
  * 导入axios
  * 编写页面中`<script>`标签下的的setup()初始化函数
  * 启动前端和后端项目

  ```js
  import {defineComponent} from 'vue';
  import axios from 'axios';

  export default defineComponent({
    name: 'Home',
    setup(){
      console.log("setup");
      axios.get("http://...").then((response)=>{
        console.log(response);
      })
    }
  })

  ```

* 报错
  * 属性未在实例中定义 -- 界面绑定参数，但JS中并未定义改该参数
    方法：`ctrl+shift+F`删除之后看文档添加/移除出Vue的标签

## Vue3数据绑定显示列表数据

Vue核心功能：数据双向绑定

* 数据绑定
  * Vue2格式数据绑定
  data中定义，声明周期方法中赋值
  * Vue3 ref
  * Vue3 reactive
* 步骤(使用ref)
  1. import ref相应功能
  2. 在setup中设置响应式变量val
  3. import导入生命周期函数onMounted
  4. 在setup中编写onMounted函数，response获取值,val.value赋值
  5. 变量值传给html界面，编写 return 函数

```js
import {defineComponent, onMounted, reactive, ref, toRef} from 'vue';
    import axios from "axios";


    export default defineComponent({
        name: 'Home',
        setup(){
            console.log("setup");
            const ebooks = ref();
            const ebooks1 = reactive({books: []});

            onMounted(()=>{
                console.log("onMounted");
                axios.get("http://localhost:8080/ebook/list?name=Spring").then((response) => {
                    const data = response.data;
                    ebooks.value = data.content;
                    ebooks1.books = data.content;
                    console.log(response);
                })
            })

            return {
                ebooks,
                ebooks2: toRef(ebooks1, "books")
            }
        }
    });
```

## 图标库导入

* 进入web项目安装图标库 `npm install @ant-design/icons-vue --save`
  * import导入整个图标库

    ```js
    // main.ts
    import * as Icons from '@ant-design/icons-vue';

    //全局使用图标
    const icons: any = Icons;
    for(const i in icons){
      app.component(i, icons[i]);
    }
    ```

## Ant Design Vue list组件添加

* 步骤
  1. 在Ant Design Vue中找到需要的组件
  2. 定义数据并初始化（定义，初始化，return）
  3. 导入图标库显示
  4. 按需修改组件样式布局
  5. 调整接口为动态SQL
  6. 修改图标样式（正方形圆形），先在前端修改，后复制到后端

* 报错
  * Typesctipt报错
    在定义数据时添加`: any`
  * 校验规则:vue/no-unused-vars
    删除未使用到的变量/删除该校验规则
  * 校验规则@typescript-eslint/no-explicit-any
    在 .eslintrc.js 删除该校验规则

## 接口调整为动态SQL

当请求参数为空时候做特殊判断

```java
//动态SQL
        if(!ObjectUtils.isEmpty(req.getName())){
            //模糊查询
            criteria.andNameLike("%"+req.getName()+"%");
        }
```

## Vue CLI多环境配置

http请求主机地址不能耦合在代码中

* 步骤
  * web目录下新建包 .env.dev（text） -- 环境为开发环境

    ```text
    NODE_ENV=development
    VUE_APP_SERVER=http://127.0.0.1:8880
    ```

  * web目录下新建包 .env.prod（text）-- 生产环境
  
    ```text
    NODE_ENV=development
    VUE_APP_SERVER=http://server.imooc.com
    ```

  * 配置编译和启动命令读取多环境配置的变量

    ```json
    // package.json
    "serve": "vue-cli-service serve",

    "serve-dev": "vue-cli-service serve --mode dev",
    "serve-prod": "vue-cli-service serve --mode prod"
    ```
  
  * 日志打印测试配置成功(读环境变量)

    ```ts
    console.log('环境：', process.env.NODE_ENV);
    console.log('服务端：', process.env.VUE_APP_SERVER);
    ```

  * 对应调整buid编译命令
  * 全局设定项目端口地址的系统变量baseurl

    ```js
    //main.ts
    import axios from 'axios';
    axio.defaults.baseURL = process.env.VUE_APP_SERVER;
    ```

  * 修改vue页面中axios的baseurl读配置文件

## axios拦截器使用

* 原因：前端调试需要在前端打印日志，众多前后端接口都需要如此调试，axios拦截器功能把请求日志和返回参数一起打印出来
* 步骤
  1. main.ts中配置axios拦截器

    ```js
    /**
     * axios拦截器
    */
    axios.interceptors.request.use(function (config) {
      console.log('请求参数：', config);
      const token = store.state.user.token;
      if (Tool.isNotEmpty(token)) {
        config.headers.token = token;
        console.log("请求headers增加token:", token);
      }
      return config;
    }, error => {
      return Promise.reject(error);
    });
    axios.interceptors.response.use(function (response) {
      console.log('返回结果：', response);
      return response;
    }, error => {
      console.log('返回错误：', error);
      const response = error.response;
      const status = response.status;
      if (status === 401) {
        // 判断状态码是401 跳转到首页或登录页
        console.log("未登录，跳到首页");
        store.commit("setUser", {});
        message.error("未登录或登录超时");
        router.push('/');
      }
      return Promise.reject(error);
    });
    ```

## 使用过滤器记录接口耗时

* 步骤
  1. 新建过滤器层包 com.hopkin.wiki.filter
  2. 编写LogFilter.java（较为固定，用现成的代码）

  ```java
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  import org.springframework.stereotype.Component;

  import javax.servlet.*;
  import javax.servlet.http.HttpServletRequest;
  import java.io.IOException;

  @Component
  public class LogFilter implements Filter {

    private static final Logger LOG = LoggerFactory.getLogger(LogFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 打印请求信息
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        LOG.info("------------- LogFilter 开始 -------------");
        LOG.info("请求地址: {} {}", request.getRequestURL().toString(), request.getMethod());
        LOG.info("远程地址: {}", request.getRemoteAddr());

        long startTime = System.currentTimeMillis();
        filterChain.doFilter(servletRequest, servletResponse);
        LOG.info("------------- LogFilter 结束 耗时：{} ms -------------", System.currentTimeMillis() - startTime);
    }
  }

  ```

## 使用拦截器记录接口耗时

* 步骤
  1. 新建拦截器层包 com.hopkin.wiki.interceptor
  2. 编写LogInterceptor.java（较为固定，用现成的代码）
  3. 拦截器在config包中增加配置类SpringMvcConfig.java
  4. SpringMvcConfig.java注入拦截器，编写addInterceptors方法，设置针对的请求，排除某些接口请求

## 使用AOP记录接口耗时

* 注意：过滤器拦截器aop选其一，一般使用aop
* 主要内容：配置AOP，打印接口耗时、请求参数、返回参数
* 步骤
  1. 新增AOP的aspect层
  2. pom增加新的依赖 aop和fastjson

      ```xml
      <!-- aop -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <!--json 处理-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.70</version>
        </dependency>
      ```

  3. 新增aop 切点、前置通知、后置通知、环绕通知

      ```java
      import com.alibaba.fastjson.JSONObject;
      import com.alibaba.fastjson.support.spring.PropertyPreFilters;
      import org.aspectj.lang.JoinPoint;
      import org.aspectj.lang.ProceedingJoinPoint;
      import org.aspectj.lang.Signature;
      import org.aspectj.lang.annotation.Around;
      import org.aspectj.lang.annotation.Aspect;
      import org.aspectj.lang.annotation.Before;
      import org.aspectj.lang.annotation.Pointcut;
      import org.slf4j.Logger;
      import org.slf4j.LoggerFactory;
      import org.springframework.stereotype.Component;
      import org.springframework.web.context.request.RequestContextHolder;
      import org.springframework.web.context.request.ServletRequestAttributes;
      import org.springframework.web.multipart.MultipartFile;

      import javax.servlet.ServletRequest;
      import javax.servlet.ServletResponse;
      import javax.servlet.http.HttpServletRequest;

      @Aspect
      @Component
      public class LogAspect {

          private final static Logger LOG = LoggerFactory.getLogger(LogAspect.class);

          /** 定义一个切点 */
          @Pointcut("execution(public * com.hopkin.*.controller..*Controller.*(..))")
          public void controllerPointcut() {}

          @Before("controllerPointcut()")
          public void doBefore(JoinPoint joinPoint) throws Throwable {

              // 开始打印请求日志
              ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
              HttpServletRequest request = attributes.getRequest();
              Signature signature = joinPoint.getSignature();
              String name = signature.getName();

              // 打印请求信息
              LOG.info("------------- 开始 -------------");
              LOG.info("请求地址: {} {}", request.getRequestURL().toString(), request.getMethod());
              LOG.info("类名方法: {}.{}", signature.getDeclaringTypeName(), name);
              LOG.info("远程地址: {}", request.getRemoteAddr());

              // 打印请求参数
              Object[] args = joinPoint.getArgs();
          // LOG.info("请求参数: {}", JSONObject.toJSONString(args));

          Object[] arguments  = new Object[args.length];
              for (int i = 0; i < args.length; i++) {
                  if (args[i] instanceof ServletRequest
                          || args[i] instanceof ServletResponse
                          || args[i] instanceof MultipartFile) {
                      continue;
                  }
                  arguments[i] = args[i];
              }
              // 排除字段，敏感字段或太长的字段不显示
              String[] excludeProperties = {"password", "file"};
              PropertyPreFilters filters = new PropertyPreFilters();
              PropertyPreFilters.MySimplePropertyPreFilter excludefilter = filters.addFilter();
              excludefilter.addExcludes(excludeProperties);
              LOG.info("请求参数: {}", JSONObject.toJSONString(arguments, excludefilter));
          }

          @Around("controllerPointcut()")
          public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
              long startTime = System.currentTimeMillis();
              Object result = proceedingJoinPoint.proceed();
              // 排除字段，敏感字段或太长的字段不显示
              String[] excludeProperties = {"password", "file"};
              PropertyPreFilters filters = new PropertyPreFilters();
              PropertyPreFilters.MySimplePropertyPreFilter excludefilter = filters.addFilter();
              excludefilter.addExcludes(excludeProperties);
              LOG.info("返回结果: {}", JSONObject.toJSONString(result, excludefilter));
              LOG.info("------------- 结束 耗时：{} ms -------------", System.currentTimeMillis() - startTime);
              return result;
          }
      }

      ```

## 增加电子书管理页面

* 新页面开发：页面，路由，菜单
* 具体步骤
  * 由于相应页面管理员需要登录才可访问，新增admin文件夹
  * 制定页面取名规则：小写开头，多单词-连接
  * 新增vue.页面
  * 在router中修改相应路由
  * 配置菜单跳转入口router-link

## 电子书表格展示

* 步骤
  1. public/image 添加静态图片资源
  2. 数据库中将相应记录添加图片资源路径
  3. 删除定义变量未使用的校验规则`'vue/no-unused-vars': 0,`
  4. 编辑相应的vue页面（组件，参数，分页，查询和表格点击函数）

## PageHelper实现后端分页

* PageHelper本质
  自动实现SQL 中的limit分页
  一共查两次，第一次count(*)查总数据数，第二次查当前页信息
* 注意
  * 页面从1开始而不是0
  * PageHelper.startPage(1,3)只对遇到的第一句SQL作用
* 步骤
  1. 导入PageHelper依赖
  2. pagehelper 和 pageInfo使用
  3. 设置log打印SQL语句

      ```prop
      # 打印所有的sql日志：sql, 参数, 结果
      logging.level.com.hopkin.wiki.mapper=trace
      ```
  
  4. 封装pagehelper相关的请求参数和返回参数

## 前后端分页功能整合

* 步骤
  1. 设置pagination相关分页数据
  2. 前端页面axios请求参数修改，设为动态handleQuery
  3. 由于后端封装分页请求参数和返回参数，修改前端数据获取方式
  4. 修改重置分页按钮，total
  5. onMounted设置初始页相关数据

## 制作电子书表单

* 注意
  * 表格内容未显示，但是返回参数正确--colums列变量设置错误，每列的源数据未对应好返回数据
* 步骤
  1. Model对话框，带年纪编辑按钮，弹出模态框
  2. Model对话框放入文本框

## 电子书编辑功能

增加后端保存接口
点击保存时候，调用保存接口
保存成功刷新列表

* 注意
  * json方式（post）提交，controller需要在参数中添加@RequestBody注解（form表单方式可以不需要注解）
* 步骤
  1. 复制controller层其它接口进行修改（请求返回参数，具体service层操作）
  2. 相应的请求封装类创建（由于保存直接对应与数据库表，可复制domain实体类）
  3. service层新增业务方法
  4. 前端页面当点击完成按钮绑定函数，提交http请求
  5. 请求成功后刷新当前页面

## 雪花算法

* 原理：4部分组成：时间戳+数据中心+机器中心（标识机器数）+递增的序列号
* 使用方法
  1. 写配置文件，解耦数据中心和机器中心数的配置
  2. new SnowFlake(datacenterId, machineId)/@Component注入
  3. snowFlake.nextId()

## 电子书新增功能

1. 注入雪花算法Id生成器对象
2. 前端添加新增按钮
3. 前端编写add按钮点击新增的函数
4. return add
5. JacksonConfig全局配置解决前端端精度丢失问题

## 电子书删除功能

1. 后端编写删除接口（DeleteMapping；"/delete/{id}"；@PathVariable Long id）
2. 前端添加删除按钮和删除确认框
3. 前端删除按钮绑定删除函数
4. return delete删除函数

## 使用Validation做参数校验

对电子书查询和保存做参数校验
集成spring-boot-starter-validation
对保存接口和查询接口增加参数校验
校验不通过时，前端弹出错误提示

* 步骤
  1. 导入依赖
  2. 对应请求封装类设置校验
  3. 接口处开启校验 @Valid
  4. 统一异常处理
  5. 前端添加message组件给出提示

## 电子书管理功能优化

增加名字查询
编辑时复制对象

## 分类管理功能开发

* 注意
  * ctrl+E快速回到上个页面/双屏幕 --- 对应数据表修改属性
  * 单词复数 categorys -- 项目风格，所有列表加s
* 步骤
  1. 设计分类表，生成持久层代码
  2. service和controller层---从电子书管理部分拷贝一套分类管理代码，用搜索替换(ctrl+r)功能替换表名，完成分类的基本增删改查功能（不勾选word）
  3. 封装类编写，从持久层domain中复制，validation检验
  4. 前端页面编写--从已有页面复制,关键字替换，修改表格、表单
  5. 修改前端路由
  6. 修改菜单部分，添加相应功能

## 分类表格显示优化

* 注意
  * 编辑内部的表单内的值未更改，Action按钮未渲染
* 不分页
  * 编写后端接口（将分页功能的页显示条数设置1000也可，两次查询效率低）
  * 编写前端接口，删除所有有关分页的（搜索pagination）
* 树型表格
  1. 添加tool类，递归将数据转换成树形
  2. 为表格添加新的数据源变量
  3. 调用tool中递归方法，return 变量
* 父分类用select选择器下拉框实现
  1. 添加select下拉框组件
  2. 将特定的分类置为disabled
* 电子书管理功能优化-级联组件
  1. 添加级联组件
  2. 编写option树形结构数组变量
  编辑电子书时，可以选中分类一、分类二；表格显示分类名称
  
8. 首页显示分类菜单
7. 点击某分类时，显示该分类下所有的电子书
