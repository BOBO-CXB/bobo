# Spring-Mybatis

http://mybatis.org/spring/zh/index.html

### 步骤：

#### 1、导入相关jar包：

- junit
- mybatis
- mysql数据库
- spring相关
- aop
- mybatis-spring

#### 2、编写配置文件

#### 3、测试



# 1、Mybatis

- 编写实体类
- 编写核心配置文件
- 编写接口
- 编写Mapper.xml
- 测试



# 2、整合

- 编写数据配置源

```xml
<!--配置数据源，DataSource:使用Spring的数据源来替换Mybatis的配置 c3p0 dbcp druid
        这里使用Spring提供的JDBC
    -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/db_bookstore?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>
```

- sqlSessionFactory

```xml
<!--配置sqlSessionFactory处理sqlSession的创建-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--绑定Mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:mapper/UserMapper.xml"/>
    </bean>
```

- SqlSessionTemplate

```xml
 <!--配置SqlSessionTemplate,即Mybatis中的sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--只能通过构造器注入的方式注入SqlSessionFactory，因为它没有set方法-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
```

- 给接口加实现类
- 将实现类注入到spring中
- 测试使用即可

# 3、声明式事务

### 1、事务

- 把一组业务当成一个业务来做，要么都成功，要么都失败
- 事务在项目的开发中十分重要，不容马虎，涉及到数据的一致性问题
- 确保完整性和一致性



事务的ACID原则：

- 原子性
- 一致性
- 隔离性
  - 多个业务可能操作同一资源，防止数据损坏
- 持久性
  - 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写到存储器中