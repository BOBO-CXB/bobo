## MyBatis

> **相关笔记**：[[Spring-Mybatis]] · [[Spring]] · [[Mysql安装]] · [[依赖管理]]


#### 中文文档：https://mybatis.org/mybatis-3/zh/index.html

#### Meavn：https://mvnrepository.com/artifact/org.mybatis/mybatis

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.6</version>
</dependency>

```

#### Github：https://github.com/mybatis/mybatis-3



#### 数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存：**断电即失**（即为什么需要持久化，有些东西不能让它丢掉）



### 持久层：

- 完成持久化工作的代码块
- 层界限十分明显



### MyBatis程序

##### 		搭建环境 ---- 导入Mybatis ----- 编写代码 ----- 测试

### 导入依赖

```xml
<dependencies>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>


        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>

        <!--junit依赖-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
```

### 创建一个模块

- 编写mybatis的核心配置文件
- 编写mybatis的工具类

### mybatis的核心配置文件mybatis-config.xml（中文文档）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
<!--environments环境集-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/><!--com.mysql.jdbc.Driver-->
                <property name="url" value="jdbc:mysql://localhost:3306/db_bookstore?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
<!--每一个Mapper.xml文件都需要在Mybatis 的核心配置文件中注册-->
    <mappers>
        <mapper resource="/Mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

### mybatis的工具类

```java
package com.yzu.utils;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

//sqlSessionFactory  sqlSession
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static{
        try {
            //使用Mybatis第一步获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (IOException e){
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }

}

```

### 编写代码

- 实体类

  ```java
  package com.yzu.pojo;
  
  public class User {
      private int id;
      private String username;
      private String password;
      private int age;
      private String sex;
  
      public int getId() {
          return id;
      }
  
      public String getUsername() {
          return username;
      }
  
      public String getPassword() {
          return password;
      }
  
      public int getAge() {
          return age;
      }
  
      public String getSex() {
          return sex;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public void setUsername(String username) {
          this.username = username;
      }
  
      public void setPassword(String password) {
          this.password = password;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  
      public void setSex(String sex) {
          this.sex = sex;
      }
  
      public User() {
      }
  
      public User(String username, String password, int age, String sex) {
          this.username = username;
          this.password = password;
          this.age = age;
          this.sex = sex;
      }
  }
  
  ```

- Dao接口

  ```java
  package com.yzu.Dao;
  import com.yzu.pojo.User;
  import java.util.List;
  
  public interface UserDao {
      List<User> getUserList();
  }
  
  ```

  

- 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <!--namespace会绑定一个对应的Dao/Mapper接口-->
  <!--相当于实现接口-->
  <mapper namespace="com.yzu.Dao.UserDao">
  <!--相当于重写方法-->
  <!--select查询语句-->
      <select id="getUserList" resultType="com.yzu.pojo.User">
          select * from db_bookstore.user
      </select>
  </mapper>
  ```



### 测试：junit

#### 注意点：MapperRegistry

核心配置文件中注册mappers

org.apache.ibatis.binding.BindingException: Type interface com.yzu.Dao.UserDao is not known to the MapperRegistry.

```xml
<!--每一个Mapper.xml文件都需要在Mybatis 的核心配置文件中注册-->
    <mappers>
        <mapper resource="Mapper/UserMapper.xml"/>
    </mappers>
```

#### Meavn约定大于配置，所以可能遇到配置文件无法被导出或生效，需要在pom.xml中build中进行配置resource

```xml
<!--在buld中配置resources，来防止资源导出失败-->
  <build>
    <resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>

      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
```

### Junit代码：

```java
package com.yzu.Dao;

import com.yzu.pojo.User;
import com.yzu.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test(){

        //第一步：获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        //执行sql
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();
        System.out.println("Test1");
        for(User user : userList){
            System.out.println(user);
        }

        //关闭SqlSession
        sqlSession.close();
    }

    @Test
    public void getUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserDao mapper = sqlSession.getMapper(UserDao.class);
        User user = mapper.getUserById(1);
        System.out.println("Test2");
        System.out.println(user);

        sqlSession.close();
    }



    //增删改需要提交事务
    @Test
    public void addUser(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        User user = new User("小明","abc",22,"男");
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        int result = mapper.addUser(user);
        System.out.println("Test3");
        if(result>0){
            System.out.println("插入成功，返回值为："+result);
        }

        //提交事务
        sqlSession.commit();
        sqlSession.close();
    }
}

```

### 增删改需要提交事务,否则不会影响数据库

```java
  sqlSession.commit();
```

### Mybatis环境配置

mybatis默认的事务管理器就是JDBC

连接池:POOLED   

**POOLED**– 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这种处理方式很流行，能使并发 Web 应用快速响应请求。

### 属性（properties）

我们可以通过properties属性来实现引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。-------**db.properties**

#### 原先配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
<!--environments环境集-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/db_bookstore?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
<!--每一个Mapper.xml文件都需要在Mybatis 的核心配置文件中注册-->
    <mappers>
        <mapper resource="Mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 编写配置文件db.properties

```properties
#数据库连接驱动
driver=com.mysql.cj.jdbc.Driver
#数据库连接链接
jdbc.url=jdbc:mysql://localhost:3306/db_bookstore?useSSL=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
#数据库登陆用户名
username=root
#数据库登陆密码
password=123456
```

#### 在核心配置文件中引入db.properties

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>
<!--引入外部配置文件-->
    <properties resource="db.properties"/>
<!--environments环境集-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
<!--每一个Mapper.xml文件都需要在Mybatis 的核心配置文件中注册-->
    <mappers>
        <mapper resource="Mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字。 
- 它仅用于 XML 配置，意在降低冗余的全限定类名书写

```xml
<!--可以给实体类设置别名-->
    <typeAliases>
        <typeAlias type="com.yzu.pojo.User" alias="User"/>
    </typeAliases>
```

原先代码

```xml
<!--select查询语句-->
    <select id="getUserList" resultType="com.yzu.pojo.User">
        select * from db_bookstore.user
    </select>
```

优化后

```xml
<!--select查询语句-->
    <select id="getUserList" resultType="User">
        select * from db_bookstore.user
    </select>
```

#### 也可以指定一个包名

Mybati会在包名下搜索需要的Java Bean

扫描实体类的包，它的默认别名就是为这个类的类名首字母小写

#### 在实体类比较少的情况下，建议使用第一种方式

#### 如果实体类十分多，建议使用第二种

#### 第一种可以DIY别名，自定义名称，第二种不行，如果非要改，可以在实体上增加注释

```java
@Alias("user")
public class User {...}
```

### 设置（settings）

是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

![QQ截图20210214151514](MyBatis.assets/QQ%E6%88%AA%E5%9B%BE20210214151514.jpg)

![QQ截图20210214151612](MyBatis.assets/QQ%E6%88%AA%E5%9B%BE20210214151612.jpg)

### 映射器（mappers）

#### MapperRegistry

核心配置文件中注册mappers

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>


```

```xml

<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

#### 如果使用映射器接口实现类的完全限定类名

注意点;

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

```xml

<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

### 作用域（Scope）和生命周期7

作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的并发问题。

### 四大作用域：

#### application（ServletContext）

- 生命周期：当Web应用被加载进容器时创建**代表整个web应用**的application对象，当服务器关闭或Web应用被移除时，application对象跟着销毁。  
- 作用范围：整个Web应用。

#### session 域 (HttpSession)

- 生命周期：在第一次调用 request.getSession() 方法时，服务器会检查是否已经有对应的session,如果没有就在内存中创建一个session并返回。  当一段时间内session没有被使用（默认为30分钟），则服务器会销毁该session
- 作用范围：一次会话。 

#### request域  --(HttpServletRequest)

- 生命周期：在service 方法调用前由服务器创建，传入service方法。整个请求结束，request生命结束。
- 作用范围：整个请求链（请求转发也存在）

#### pageContext域—(PageContext)

- 生命周期：当对JSP的请求时开始，当响应结束时销毁。  

- 作用范围：整个JSP页面，是四大作用域中最小的一个。

  

### SqlSessionFactoryBuilder：

- 一旦创建了 SqlSessionFactory，就不再需要它了

- 最佳作用域是方法作用域（也就是局部方法变量）

  

### SqlSessionFactory：

- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**
- 可以想象为**数据库连接池**
- SqlSessionFactory 的最佳作用域是**应用作用域**
- 使用**单例模式**或者静态单例模式。



### SqlSession：

- 连接到连接池的一个请求
- 用完之后需要赶紧关闭，否则会占用连接池资源
- 每个线程都应该有它自己的 SqlSession 实例
- sqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的**最佳的作用域是请求或方法作用域。**



### 日志

#### 日志工厂：

如果一个数据库操作出现异常，我们需要进行排错，日志就是最好的助手

曾经：sout,debug

现在：日志工厂

#### logImpl 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。

SLF4J 

LOG4J 【掌握】

LOG4J2 

JDK_LOGGING

COMMONS_LOGGING

STDOUT_LOGGING【掌握】

NO_LOGGING



#### 具体使用哪个日志实现，在设置中设定

```xml
<!--设置日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING "/>
    </settings>

<!--输出-->
Opening JDBC Connection
Created connection 1603198149.
Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5f8edcc5]
==>  Preparing: select * from db_bookstore.user where username like ? 
==> Parameters: %b%(String)
<==    Columns: id, username, password, age, sex
<==        Row: 1, BOBO, 123456, 21, 男
<==      Total: 1
User{id=1, username='BOBO', password='123456', age=21, sex='男'}
Resetting autocommit to true on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5f8edcc5]
Closing JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@5f8edcc5]
Returned connection 1603198149 to pool.

Process finished with exit code 0

```

### Log4j

- Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等；
- 我们也可以控制每一条日志的输出格式；
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。
- 这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。



#### 1、导包

#### 2、创建配置文件log4j.properties

https://blog.csdn.net/eagleuniversityeye/article/details/80582140

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/bobo.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

#### 3、配置log4j为日志的实现

```xml
<settings>
        <setting name="logImpl" value="LOG4J"/>
</settings>
```

#### 4、log4j使用

##### 直接运行

```xml
[org.apache.ibatis.logging.LogFactory]-Logging initialized using 'class org.apache.ibatis.logging.commons.JakartaCommonsLoggingImpl' adapter.
[org.apache.ibatis.logging.LogFactory]-Logging initialized using 'class org.apache.ibatis.logging.log4j.Log4jImpl' adapter.
[org.apache.ibatis.datasource.pooled.PooledDataSource]-PooledDataSource forcefully closed/removed all connections.
[org.apache.ibatis.datasource.pooled.PooledDataSource]-PooledDataSource forcefully closed/removed all connections.
[org.apache.ibatis.datasource.pooled.PooledDataSource]-PooledDataSource forcefully closed/removed all connections.
[org.apache.ibatis.datasource.pooled.PooledDataSource]-PooledDataSource forcefully closed/removed all connections.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Opening JDBC Connection
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Created connection 1997353766.
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@770d3326]
[com.yzu.Dao.UserDao.getUserLike]-==>  Preparing: select * from db_bookstore.user where username like ? 
[com.yzu.Dao.UserDao.getUserLike]-==> Parameters: %b%(String)
[com.yzu.Dao.UserDao.getUserLike]-<==      Total: 1
User{id=1, username='BOBO', password='123456', age=21, sex='男'}
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Resetting autocommit to true on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@770d3326]
[org.apache.ibatis.transaction.jdbc.JdbcTransaction]-Closing JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@770d3326]
[org.apache.ibatis.datasource.pooled.PooledDataSource]-Returned connection 1997353766 to pool.

Process finished with exit code 0
```



### 分页

- 减少数据的处理量



#### 使用Limit分页

语法

```sql
select * from user limit startIndex,pagesize;
```

