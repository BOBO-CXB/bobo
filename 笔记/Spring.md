# 1、Spring

> **相关笔记**：[[SpringMVC]] · [[SpringBoot]] · [[SpringClould]] · [[MyBatis]] · [[Spring-Mybatis]] · [[注解说明]]


### 1.1简介

- Spring：春天    -----------  给软件行业带来了春天
- Spring框架是由于软件开发的复杂性而创建的
- Spring是一个**轻量级控制反转(IoC)和面向切面(AOP)**的容器框架。
- 2002年，首次推出了Spring框架的雏形：**inerface 21**
- 2004年3月24日，Spring框架以interface21框架为基础，经过重新设计，发布了1.0正式版
- **Rod Johnson**
- Spring理念：使现有技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架
- SSH：Struct2+Spring+Hibernate
- SSM：SpringMVC+Spring+Mybatis

### 1.2相关网站

- 官方文档：https://docs.spring.io/spring-framework/docs/current/reference/html/
- 官方下载地址：https://repo.spring.io/release/org/springframework/spring/
- Github：https://github.com/spring-projects/spring-framework

### 1.3导入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>


```

### 1.4优点

- Spring是一个开源的免费的框架（容器）
- Spring是一个轻量级，非入侵式的框架
- **控制反转（IOC）和面向切面编程（AOP）**
- 支持事务的处理，对框架整合的支持

### 1.5总结

- Spring就是一个**轻量级**的**控制反转（IOC）**和**面向切面编程（AOP）**的框架

### 1.6组成

![QQ截图20210215112508](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210215112508.jpg)

**核心容器（Spring Core）**

　　核心容器提供Spring框架的基本功能。Spring以bean的方式组织和管理Java应用中的各个组件及其关系。Spring使用BeanFactory来产生和管理Bean，它是工厂模式的实现。BeanFactory使用控制反转(IoC)模式将应用的配置和依赖性规范与实际的应用程序代码分开。

**应用上下文（Spring Context）**

　　Spring上下文是一个配置文件，向Spring框架提供上下文信息。Spring上下文包括企业服务，如JNDI、EJB、电子邮件、国际化、校验和调度功能。

**Spring面向切面编程（Spring AOP）**

　　通过配置管理特性，Spring AOP 模块直接将面向方面的编程功能集成到了 Spring框架中。所以，可以很容易地使 Spring框架管理的任何对象支持 AOP。Spring AOP 模块为基于 Spring 的应用程序中的对象提供了事务管理服务。通过使用 Spring AOP，不用依赖 EJB 组件，就可以将声明性事务管理集成到应用程序中。

**JDBC和DAO模块（Spring DAO）**

　　JDBC、DAO的抽象层提供了有意义的异常层次结构，可用该结构来管理异常处理，和不同数据库供应商所抛出的错误信息。异常层次结构简化了错误处理，并且极大的降低了需要编写的代码数量，比如打开和关闭链接。

**对象实体映射（Spring ORM）**

　　Spring框架插入了若干个ORM框架，从而提供了ORM对象的关系工具，其中包括了Hibernate、JDO和 IBatis SQL Map等，所有这些都遵从Spring的通用事物和DAO异常层次结构。

**Web模块（Spring Web）**

　　Web上下文模块建立在应用程序上下文模块之上，为基于web的应用程序提供了上下文。所以Spring框架支持与Struts集成，web模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作。

**MVC模块（Spring Web MVC）**

　　MVC框架是一个全功能的构建Web应用程序的MVC实现。通过策略接口，MVC框架变成为高度可配置的。MVC容纳了大量视图技术，其中包括JSP、POI等，模型来有JavaBean来构成，存放于m当中，而视图是一个街口，负责实现模型，控制器表示逻辑代码，由c的事情。Spring框架的功能可以用在任何J2EE服务器当中，大多数功能也适用于不受管理的环境。Spring的核心要点就是支持不绑定到特定J2EE服务的可重用业务和数据的访问的对象，毫无疑问这样的对象可以在不同的J2EE环境，独立应用程序和测试环境之间重用。

### 1.7拓展

#### Spring Boot

- 一个快速开发的脚手架
- 基于Spring Boot可以快速的开发单个微服务
- 约定大于配置

#### Spring Cloud

- Spring Cloud是基于Spring Boot实现的

#### 现在大多数公司都在使用SpringBoot进行开发

- 学习SpringBoot前，需要完全掌握Spring及SpringMVC，承上启下



# 2、IOC理论推导

#### 1.UserDao接口

```java
package com.yzu.dao;

public interface UserDao {
    void getUser();
}
```



#### 2.UserDaoImpl实现类

```java
package com.yzu.dao;

public class UserDaoImpl implements UserDao{
    @Override
    public void getUser() {
        System.out.println("默认获取用户数据!");
    }
}
```



#### 3.UserService业务接口

```java
package com.yzu.service;

public interface UserService {
    void getUser();
}
```



#### 4.UserServiceImpl业务实现类

```java
package com.yzu.service;
import com.yzu.dao.UserDao;
import com.yzu.dao.UserDaoImpl;

public class UserServiceImpl implements UserService  {
    private  UserDao userDao = new UserDaoImpl();

    @Override
    public void getUser() {
        userDao.getUser();
    }
}
```

#### 5.测试

```java
import com.yzu.service.UserService;
import com.yzu.service.UserServiceImpl;

public class MyTest {
    public static void main(String[] args) {

        //用户实际调用的是业务层，dao层则不需要接触
        UserService userService = new UserServiceImpl();
        userService.getUser();
    }
}

```

#### 	用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码！如果程序代码量十分大，修改一次代码的成本代价十分昂贵。

#### 	我们使用一个set接口实现,发生了革命性的变化

```java
package com.yzu.service;
import com.yzu.dao.UserDao;

public class UserServiceImpl implements UserService  {
    private  UserDao userDao;

    //利用set进行动态实现值的注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void getUser() {
        userDao.getUser();
    }
}
```

- 之前是程序主动创建对象，控制权在程序员手上
- 使用了set注入后，程序不再具有主动性，而是变成了被动的接收对象



这种思想，从本质上解决了问题，程序员不需要再管理对象的创建了，系统的耦合性大大降低

可以更加专注于业务的实现上，这是IoC的原型



### IoC是一种设计思想

- DI依赖注入是实现IoC的一种方法



#### Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从IoC容器中取出需要的对象

![QQ截图20210215133228](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210215133228.jpg)

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）**



# 3、HelloSpring

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<!--使用Spring来创建对象，在Spring这些都被称为Bean
    bean = 对象  new Hello();
    类型 变量名 = new 类型();
    Hello hello = new Hello();
    id = 变量名
    class = new的对象
    property 相当于给对象中的属性设置一个值
-->
    <bean id="hello" class="com.yzu.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

</beans>
```

#### Test

```java
import com.yzu.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        //获取Spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //我们的对象都在Spring中管理，我们要使用，直接去里面取出来就可以了
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}

```

#### 不再需要修改程序了，要实现不同的操作，只需要在xml配置文件中进行修改

## 对象由Spring来创建、管理、修改 



# 4、IoC创建对象的方式

#### 1、使用无参构造创建对象，默认

#### 2、假设要使用有参构造方法，有以下几种方式

```xml
<!--第一种，利用下标赋值-->
    <bean id="user" class="com.yzu.pojo.User">
        <constructor-arg index="0" value="利用下标赋值有参构造"/>
    </bean>

```

```xml
<!--第二种，通过类型创建，不建议使用-->
    <bean id="user" class="com.yzu.pojo.User">
        <constructor-arg type="java.lang.String" value="利用参数类型有参构造"/>
    </bean>
```

```xml
<!-- 第三种，直接通过参数名有参构造-->
    <bean id="user" class="com.yzu.pojo.User">
        <constructor-arg name="name" value="直接通过参数名有参构造"/>
    </bean>
```

#### 在创建配置文件加载的时候，配置文件即容器中管理的实例就已经被创建初始化了，不管是否使用，且只有一份实例



# 5、Spring配置

### 5.1、别名

```xml
 <!--别名，如果添加了别名，可以使用别名获取到对象-->
    <alias name="UserServiceImpl" alias="service"/>
```

```java
//UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");
        UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("service");
```



### 5.2、Bean的配置

```xml
<!--
        id:bean的唯一标识符，也就是相当于对象名
        class:bean对象所对应的全限定名，包名+类型
        name:也是别名
    -->
```



### 5.3、import

#### 			一般用于团队开发使用，它可以将多个配置文件导入合并为一个,使用的时候直接使用总的配置即可

```xml
  <import resource="applicationContext.xml"/>
```



# 6、DI依赖注入



### 6.1、构造器注入

- 见上

### 6.2、Set方式注入【重点】

- 依赖注入：Set注入

  - 依赖：bean对象的创建依赖于容器

  - 注入：bean对象中的所有属性由容器来注入

  

  【环境搭建】

  1、复杂类型

  ```java
  package com.yzu.pojo;
  public class Address {
      private String address;
  
      public String getAddress() {
          return address;
      }
  
      public void setAddress(String address) {
          this.address = address;
      }
  }
  ```

  

  2、真实测试对象

  ```java
  public class Student {
  
      private String name;
      private Address address;
      private String[] books;
      private List<String> hobbies;
      private Map<String, String> card;
      private Set<String> games;
      private Properties info;
      private String wife;
      ...
      ...
  }
  ```

  3、applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="address" class="com.yzu.pojo.Address">
          <property name="address" value="江苏"/>
      </bean>
  
      <bean id="student" class="com.yzu.pojo.Student">
  
      <!--第一种，普通值注入，用value-->
          <property name="name" value="波波"/>
  
      <!--第二种，Bean注入，用ref-->
          <property name="address" ref="address"/>
  
      <!--第三种，数组注入-->
          <property name="books">
              <array>
                  <value>红楼梦</value>
                  <value>西游记</value>
                  <value>三国演义</value>
                  <value>水浒传</value>
              </array>
          </property>
  
      <!--第四种，List注入-->
          <property name="hobbies">
              <list>
                  <value>听歌</value>
                  <value>看电影</value>
              </list>
          </property>
  
      <!--第五种，Map注入-->
          <property name="card">
              <map>
                  <entry key="身份证" value="372847329948773294"/>
                  <entry key="银行卡" value="2483927492034892"/>
              </map>
          </property>
  
      <!--第六种，Set注入-->
          <property name="games">
              <set>
                  <value>LOL</value>
                  <value>COC</value>
              </set>
          </property>
  
      <!--第七种，null值注入-->
          <property name="wife">
              <null/>
          </property>
  
      <!--Properties
          key=value
          key=value
      -->
          <property name="info">
              <props>
                  <prop key="学号">171001101</prop>
                  <prop key="性别">男</prop>
                  <prop key="姓名">小明</prop>
              </props>
          </property>
      </bean>
  </beans>
  ```

  4、测试

  ```java
  import com.yzu.pojo.Student;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  
  public class MyTest {
      public static void main(String[] args) {
          ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
  
          Student student = (Student) context.getBean("student");
          System.out.println(student.toString());
      }
  }
     /* Student{
          name='波波',
          address=Address{address='江苏'},
          books=[红楼梦, 西游记, 三国演义, 水浒传],
          hobbies=[听歌, 看电影],
          card={身份证=372847329948773294, 银行卡=2483927492034892},
          games=[LOL, COC],
          info={学号=171001101, 性别=男, 姓名=小明},
          wife='null'}*/
  ```

  

### 6.3、拓展方式注入

#### p-namespace   ----  对应于Set方式注入

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
   <!--p命名空间注入，可以直接注入属性的值-->
    <bean id="person" class="com.yzu.pojo.Person" p:name="BOBO"/>
```



#### c-namespace    -----  对应于构造器注入

```xml
 xmlns:c="http://www.springframework.org/schema/c"
```

```xml
<bean id="person2" class="com.yzu.pojo.Person" c:name="c命名空间注入"/>
```



#### 注意：不可以直接使用，需要导入约束，如下：

- xmlns:p="http://www.springframework.org/schema/p"

-  xmlns:c="http://www.springframework.org/schema/c"



# 7、Bean的作用域

![QQ截图20210215170526](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210215170526.jpg)

#### 1、默认为单例，singleton

![QQ截图20210215170546](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210215170546.jpg)

```xml
 <bean id="person2" class="com.yzu.pojo.Person" c:name="c命名空间注入" scope="singleton"/>
```

#### 2、原型模式:每次从容器中get的时候，都会产生一个新的对象

![QQ截图20210215170836](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210215170836.jpg)

```xml
    <bean id="person2" class="com.yzu.pojo.Person" c:name="c命名空间注入" scope="prototype"/>
```

### 3、其他：request、session、application，这些只在web开发中用到



# 8、Bean的自动装配

- 自动装配是Spring满足bean依赖的一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性



在Spring中有三种自动装配的方式：

- 在xml中显示的配置
- 在java中显示配置
- 隐式的自动装配bean【重要】



## 8.1、测试

环境搭建：一个人有两个宠物！

## 8.2、ByName自动装配

```xml
<!--
    byName:会自动在容器上下文中查找和自己对象set方法后面的值对应的beanid
-->
    <bean id="person" class="com.yzu.pojo.Person" autowire="byName">
        <property name="name" value="波波"/>
    </bean>
```

## 8.3、ByType自动装配

```xml
<!--
    byType:会自动在容器上下文中查找和自己对象属性类型相同的bean，需要类型全局唯一
-->
    <bean id="person" class="com.yzu.pojo.Person" autowire="byType">
        <property name="name" value="波波"/>
    </bean>
```



小结：

- byName时需要保证所有的Bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致

- byType时需要保证所有的Bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

  

## 8.4、使用注解实现自动装配

#### jdk1.5支持的注解，spring 2.5就支持注解了

 “better” than XML

### 使用须知：

- 导入约束 context约束

- 配置注解的支持 **<context:annotation-config/>**

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd">
  
      <!--注解的支持-->
      <context:annotation-config/>
  
  </beans>
  ```



@Autowired 自动导入

- 直接在属性上使用即可!也可以在set方式上使用
- 当自动装配的属性在IoC容器中存在，且符合byName要求时，可以不写set方法

```java
public @interface Autowired {
    boolean required() default true;//required如果定义为false，说明这个对象可以为null
}
```

@Qualifier（value="xxxx"）配合@Autowired使用

@Nullable

- 字段标记了这个注解，说明这个字段可以为空

@Resource注解

小结：@Resource和@Autowired 

- 都是用来自动装配的，都可以放在属性字段上
- @Autowired 先通过byType的方式实现，而且要求这个对象存在
- @Resource默认通过byName的方式实现，如果找不到名字，则通过byType实现，如果都找不到就报错

@Resource的作用相当于@Autowired，只不过@Autowired按byType自动注入，而@Resource默认按 byName自动注入罢了。@Resource有两个属性是比较重要的，分是name和type，Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不指定name也不指定type属性，这时将通过反射机制使用byName自动注入策略。

@Resource装配顺序

　　1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
　　2. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常
　　3. 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
　　4. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配；



# 9、使用注解开发

1、bean

2、属性如何注入

3、衍生的注解

​	@Component有几个衍生的注解，我们在web开发中，会按照MVC三层架构分层

- dao 【@Repository】

- service 【@Service】
- controller 【@Controller】

这是个注解功能都是一样的，都代表将某个类注册到spring中，装配bean

4、自动装配

5、作用域

- @Scope("singleton") 单例
- @Scope("prototype") 原型

6、小结

xml与注解：

- xml更加万能，适用于任何场合，维护简单方便
- 注解不是自己的类无法使用，维护相对麻烦

最佳实践：

- xml用来管理bean
- 注解只负责完成属性的注入





# 10、使用java的方式配置spring

完全不使用xml配置，全权交给java

JavaConfig是spring的一个子项目



测试：

1、编写一个实体类，Dog

```java
@Component  //将这个类标注为Spring的一个组件，放到容器中！
public class Dog {
   public String name = "dog";
}
```

2、新建一个config配置包，编写一个MyConfig配置类

```java
@Configuration  //代表这是一个配置类
public class MyConfig {

   @Bean //通过方法注册一个bean，这里的返回值就Bean的类型，方法名就是bean的id！
   public Dog dog(){
       return new Dog();
  }

}
```

3、测试

```java
@Test
public void test2(){
   ApplicationContext applicationContext =
           new AnnotationConfigApplicationContext(MyConfig.class);
   Dog dog = (Dog) applicationContext.getBean("dog");
   System.out.println(dog.name);
}
```

4、成功输出结果！

**导入其他配置如何做呢？**

1、我们再编写一个配置类！

```java
@Configuration  //代表这是一个配置类
public class MyConfig2 {
}
```

2、在之前的配置类中我们来选择导入这个配置类

```java
@Configuration
@Import(MyConfig2.class)  //导入合并其他配置类，类似于配置文件中的 inculde 标签
public class MyConfig {

   @Bean
   public Dog dog(){
       return new Dog();
  }

}
```



# 11、代理模式

### 代理模式是SpringAOP的底层

#### 代理模式分类：

- 静态代理
- 动态代理

### 11.1、静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理后一般会做一些附属操作
- 客户：访问代理对象的人

代理模式的好处：

- 可以使真实用户的操作更加纯粹，不用去关注一些公共业务
- 公共业务交给代理角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理

缺点：

- 一个真实角色会产生一个代理角色，代码量翻倍，开发效率降低



### 代码步骤：

#### 1、接口

```java
package com.yzu.demo01;

//租房
public interface Rent {
    public void rent();
}

```



#### 2、真实角色

```java
package com.yzu.demo01;

//房东
public class Host implements Rent {
    @Override
    public void rent() {
        System.out.println("房东要出租房子！");
    }
}

```



#### 3、代理角色

```java
package com.yzu.demo01;

//代理
public class Proxy implements Rent {
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    @Override
    public void rent() {
        seeHouse();
        host.rent();
        offer();
        free();
    }

    //看房
    public void seeHouse(){
        System.out.println("中介带你看房！");
    }

    //收中介费
    public void free(){
        System.out.println("收中介费");
    }

    //签合同
    public void offer(){
        System.out.println("签合同");
    }
}

```



#### 4、客户端访问代理角色

```java
package com.yzu.demo01;


public class Client {
    public static void main(String[] args) {
        //房东要出租房子
        Host host = new Host();
        //代理，中介帮房东租房子，但代理角色一般会有一些附属操作
        Proxy proxy = new Proxy(host);

        //不用面对房东，直接找中介租房即可
        proxy.rent();

    }
}

```

### 我们在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想

纵向开发，横向开发



![图片](Spring.assets/640)



### 11.1、动态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理后一般会做一些附属操作
- 客户：访问代理对象的人

动态代理的代理类是动态生成的，不是我们直接写好的

动态代理分为两大类：

- 基于接口的动态代理
  - JDK动态代理
- 基于类的动态代理
  - cglib
  - java字节码 javassist

了解两个类Proxy：代理，InvocationHandler：调用处理程序

### InvocationHandler:调用处理程序

用于自动生成代理类

生成与代理类相关联的调用处理程序

### 理解:

​	动态代理首先需要有一个所需要的代理的接口，比如UserService，以及实现该接口，被代理的真实角色，比如UserServiceImpl，以实现对UserServiceImpl的代理。

​	还需要有一个客户端，客户端不直接访问被代理的真实角色，而访问代理类。

​	接着生成客户端访问的代理类，编写一个实现InvocationHandler接口的类，即代理的调用处理程序

​    InvocationHandler接口有一个方法，需要进行override：

```java
public Object invoke(Object proxy, Method method, Object[] args)
    throws Throwable;
```

其中有三个参数为**Object proxy, Method method, Object[] args**

####  Method method

- 对应于在代理实例上调用的接口方法的方法实例。
- 包含invoke方法，method.invoke(userService, args);
  - 参数为执行方法的对象和执行参数
  - 返回执行结果

#### Object[] args

- 代理实例的方法调用中传递的参数值的对象数组，如果接口方法不带参数，则为null。

#### Object proxy

- 调用方法的代理实例

```java
//处理代理实例，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        seeHouse();
        //动态代理的本质可就是使用反射机制
        Object result = method.invoke(userService, args);
        return result;
    }
```



### 每一个代理实例都有一个关联的调用处理程序。

#### 当在与其关联的代理实例上调用方法时，将在调用处理程序上调用此方法



### Proxy:真正生成代理类的类

- #### Proxy提供了创建动态代理类和实例的静态方法，它也是由这些方法创建的所有动态代理类的超类。

```java
public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
```

包含三个参数：

- 类加载器

  - ```java
    this.getClass().getClassLoader()
    ```

- 需要代理的接口

  - ```java
    XXXXService.getClass().getInterfaces()
    ```

- 一个调用处理程序

  - ```JAVA
    this
    ```

    

#### 用户生成一个InvocationHandler调用处理程序实例，并为其指定需要代理的真实角色

```java
//真实角色
        UserServiceImpl userService = new UserServiceImpl();
        //代理角色
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        //通过调用程序处理角色来处理我们要调用的接口对象
        pih.setObject(userService);
```

#### 

#### 获得一个pih实例，从而可以根据pih实例调用getProxy();方法获取一个代理类实例proxy，并将其映射为被代理角色的抽象接口

#### 客户端通过proxy实例进行代理访问。

```java
UserService proxy = (UserService) pih.getProxy();//动态生成的
proxy.delete(); 
```



可以对调用处理程序进行封装，将其变成一个工具类，传入需要代理的接口，返回相应的代理类实例，简化操作。

### ProxyInvocationHandler

```java
package com.yzu.demo03;


import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//用这个类自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {
    //被代理的接口
    private Object target;

    public void setObject(Object target) {
        this.target = target;
    }

    //生成并返回代理类
    public  Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }

    //处理代理实例，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        checkSys();
        //动态代理的本质可就是使用反射机制
        Object result = method.invoke(target, args);
        log(method.getName());
        return result;
    }

    public void checkSys(){
        System.out.println("进行系统环境监测！");
    }
    public void log(String msg){
        System.out.println("执行了"+msg+"方法");
    }
}

```

```java
package com.yzu.demo03;

import java.lang.reflect.Proxy;

public class Client {
    public static void main(String[] args) {
        //真实角色
        UserServiceImpl userService = new UserServiceImpl();
        
        //代理角色
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        //通过调用程序处理角色来处理我们要调用的接口对象
        pih.setObject(userService);

        UserService proxy = (UserService) pih.getProxy();
        proxy.delete();

    }
}

```



# 12、AOP

### AOP:面向切面编程

#### 通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术

![微信图片_20210216191631](Spring.assets/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210216191631.png)

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....
- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。
- 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。
- 目标（Target）：被通知对象。
- 代理（Proxy）：向目标对象应用通知之后创建的对象。
- 切入点（PointCut）：切面通知 执行的 “地点”的定义。
- 连接点（JointPoint）：与切入点匹配的执行点。



![微信图片_20210216191839](Spring.assets/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210216191839.jpg)

![微信图片_20210216192107](Spring.assets/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20210216192107.png)



## 12.1、AOP的Spring实现

导入依赖：

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>

```



### 方式一：使用Spring的API接口

### Log

```JAVA
package com.yzu.log;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice {

    //method：要执行的目标方法对象
    //object：参数
    //o：目标对象
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println(o.getClass().getName()+"的"+method.getName()+"被执行了");
    }
}

```

### AfterLog

```java
package com.yzu.log;

import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class AfterLog implements AfterReturningAdvice {
    //o：返回值
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法，返回结果为："+o);
    }
}

```

### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.yzu.service.UserServiceImpl"/>
    <bean id="log" class="com.yzu.log.Log"/>
    <bean id="afterLog" class="com.yzu.log.AfterLog"/>

    <!--方法一：使用原生Spring API接口-->
    <!--配置AOP:需要导入aop的约束-->
    <aop:config>
        <!--切入点expression:表达式，execution(要执行的位置)-->
        <aop:pointcut id="pointcut" expression="execution(* com.yzu.service.UserServiceImpl.*(..))"/>

        <!--执行环绕增加-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>

</beans>
```

### Test

```java
import com.yzu.service.UserService;
import com.yzu.service.UserServiceImpl;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    @Test
   public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        //动态代理代理的是接口
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
    }


}
```



![QQ截图20210216203827](Spring.assets/QQ%E6%88%AA%E5%9B%BE20210216203827.jpg)

### 方式二：自定义来实现AOP【主要是切面定义】

```java
package com.yzu.diy;

public class DiyPointCut {

    public void before(){
        System.out.println("==========方法执行前=========");
    }

    public void after(){
        System.out.println("==========方法执行后=========");
    }
}

```

```xml
<!--方式二：自定义类-->
    <bean id="diy" class="com.yzu.diy.DiyPointCut"/>
    <aop:config>
        <!--自定义切面，ref要引用的类-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.yzu.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
```



### 方式三：使用注解实现

```java
package com.yzu.diy;

//使用注解方式实现AOP

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect //标注这个类为切面
public class AnnotationPointCut {
    @Before("execution(* com.yzu.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("==========方法执行前=========");
    }

    @After("execution(* com.yzu.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("==========方法执行后=========");
    }

    //在环绕增强中我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.yzu.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        //获得签名
        Signature signature = jp.getSignature();
        System.out.println("签名："+signature);
        //执行方法
        Object proceed = jp.proceed();

        System.out.println("环绕后");
    }

}
```

```xml
 <!--方式三-->
    <bean id="annotationPointCut" class="com.yzu.diy.AnnotationPointCut"/>
    <!--开启注解支持-->
    <aop:aspectj-autoproxy/>
```



### 