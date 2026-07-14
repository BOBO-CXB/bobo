# Spring Boot

### pom.xml

- #### spring-boot-dependencies：核心依赖在父工程中

- #### 我们在写或引入Spring Boot依赖的时候不需要指定版本号，因为有上面的版本仓库



### 启动器：

- ```xml
  <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter</artifactId>
  </dependency>
  ```

- 启动器，说白了就是Spring Boot的启动环境

- 如启动器配置spring-boot-starter-web，会自动帮我们导入web环境所需的所有依赖！

- Spring Boot 会将所有的功能场景都变成一个个启动器

- 要使用什么功能就只要找到对应的启动器即可（starter）

- ```xml
  spring-boot-starter  核心启动器，包括自动配置支持、日志记录和 YAML
  
  spring-boot-starter-activemq  使用 Apache ActiveMQ 的 JMS 消息传递入门
  
  spring-boot-starter-amqp  使用 Spring AMQP 和 Rabbit MQ 的入门者
  
  spring-boot-starter-aop  使用 Spring AOP 和 AspectJ 进行面向方面编程的入门
  
  spring-boot-starter-data-jdbc  使用 Spring Data JDBC 的入门者
  
  spring-boot-starter-web  使用 Spring MVC 构建 Web（包括 RESTful）应用程序的入门者。使用 Tomcat 作为默认的嵌入式容器
  
  ...
  
  ```



### 主程序：

```java
/*程序主入口*/
@SpringBootApplication
public class SpringbootProjectApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootProjectApplication.class, args);
    }

}

############################################################################
@SpringBootApplication: 标注这个类为一个Spring Boot应用，保证启动类下的所有资源被导入                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              
SpringApplication.run(SpringbootProjectApplication.class, args);将Spring Boot应用启动
```

#### 注解：

- @SpringBootApplication

- ```java
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @SpringBootConfiguration 
  @EnableAutoConfiguration
  @ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
  		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
  ```

- ```java
  @SpringBootConfiguration  //Spring Boot的配置
  	@Configuration //Spring 配置类
  	@Component     //说明这也是一个Spring组件
  
  
  @EnableAutoConfiguration  //自动配置
  	@AutoConfigurationPackage //自动配置包
  		@Import(AutoConfigurationPackages.Registrar.class) //自动配置 ‘包注册’
  	@Import(AutoConfigurationImportSelector.class)  //自动配置导入选择
  ```

  
  

### 配置文件：修改自动配置的默认值

#### SpringBoot使用一个全局的配置文件，文件名是固定的

- #### application.properties

  - 语法：key=value

- #### application.yml

  - 语法：key：空格value

#### application.properties

```properties
#springboot 核心配置文件
#修改端口号
server.port=8080

#自定义Banner  banner.txt

```

#### application.yml

```yml
#springboot 核心配置文件
#修改端口号
server:
  port: 8080
```



#### yaml可以直接给实体类赋值

原先可以使用注解@Value进行赋值

```java
public class Dog {
    @Value("BOBO")
    private String name;
    @Value("2")
    private Integer age;
    ...
    ...
    ...
```

可以在yaml中为对象赋值

```yaml
Person:
  name: CXB
  age: 22
  happy: true
  birth: 2021/12/09
  maps: {
          key1: value1,
          key2: value2,
          key3: value3
  }
  list: 
    - 音乐
    - CODE
  dog: 
    name: bobo
    age: 2
```

实体类上加上注解进行数据绑定@ConfigurationProperties(prefix = "person")

```java
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private Integer age;
    private boolean happy;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> list;
    private Dog dog;
```

#### 输出：

​		Person{name='CXB', age=22, happy=true, birth=Thu Dec 09 00:00:00 CST 2021, maps={key1=value1, key2=value2, key3=value3}, list=[音乐, CODE], dog=Dog{name='bobo', age=2}}

#### #加载指定的配置文件

##### 可以使用注解@PropertySource(value = "classpath:application.properties")来指定加载application.properties配置文件



### 使用注解绑定支持松散绑定，JSR303数据校验，复杂类型封装等。

#### 松散绑定：

- 在注解中last-name 和 lastName是一样的，两者可以进行绑定，-后面跟着的字母默认是大写的

#### JSR303校验：

- 在字段出增加一层过滤器验证，保证数据的合法性
- @Validated
- @Email

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {

    @Email(message = "邮箱格式错误")
    private String email;
```



输出：

	    Property: person.name
	    Value: CXB
	    Origin: class path resource [application.yml] - 8:9
	    Reason: 邮箱格式错误
#### 

### Config文件：

配置文件可以写在4个地方：

- 1、项目目录下的config文件中  file：./config/
- 2、项目目录下    file:./
- 3、类路径下的config  classpath:/config/
- 4、类路径下 classpath:/  （默认）

### 优先级依次递减，加载顺序依次递增4-3-2-1，最后1生效

#### 同一路径下三种配置文件优先级：

#### properties>yaml>yml  

#### 即若三个不同后缀的配置文件同时存在，优先加载yml，后加载的会覆盖先前的配置文件



### 多环境下可以选择激活哪一个配置文件：

如有以下三种环境：

- application.properties  正常环境
- application-test.properties 测试环境
- application-dev.properties 开发环境

可以在配置文件中选择激活哪个：

```properties
spring.profiles.active=dev  #只要选择后缀即可
```



### yaml可以在一个文件中写3种环境，用---隔开即可

```yaml
server:
  port: 8080
  
#选择配置  
spring:
  profiles:
    active: dev

---
server:
  port: 8081
spring:
  config:
    activate:
      on-profile: dev   #后缀
---
server:
  port: 8082
spring:
  config:
    activate:
      on-profile: test
```

#### 能在配置文件中配置的属性，都存在一个规律，即存在相应的 XXProperties  被相应的XXAutoConfiguration装配

#### XXProperties与配置文件绑定  类似于@ConfigurationProperties(prefix = "person") 配置文件给相应对象赋值



### 自动装配原理：

### @SpringBootApplication

- @SpringBootConfiguration

  - @Configuration    标注该该类为一个配置类

- #### @EnableAutoConfiguration（核心）

  - @AutoConfigurationPackage  自动配置包

    - @Import(AutoConfigurationPackages.Registrar.class)
      - 将Registrar类导入到容器中，Registrar类作用是扫描主配置类同级目录以及子包，并将相应的组件导入到springboot创建管理的容器中；

  - @Import(AutoConfigurationImportSelector.class) 自动配置导入选择器

    - 导入AutoConfigurationImportSelector类,通过getAutoConfigurationEntry方法获取自动配置实体

    - ```java
      @Override
      	public String[] selectImports(AnnotationMetadata annotationMetadata) {
      		if (!isEnabled(annotationMetadata)) {
      			return NO_IMPORTS;
      		}
      		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
      		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
      	}
      ```

    - getAutoConfigurationEntry方法调用getCandidateConfigurations获取所有候选配置信息

    - ```java
      List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
      ```

    - getCandidateConfigurations调用SpringFactoriesLoader.loadFactoryNames

    - ```java
      List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
      				getBeanClassLoader());
      ```

    - loadFactoryNames调用loadSpringFactories方法

    - ```java
      return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList()); 
      ```

    - loadSpringFactories方法读取META-INF/spring.factories配置文件中的信息,将读取出的配置文件信息转化为Properties配置类

    - ```java
      Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
      //public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
      ...
      Properties properties = PropertiesLoaderUtils.loadProperties(resource);
      ```

    - spring.factories中是各依赖自动配置文件的全限定名如下：

    - ```xml
      org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
      ```

    - WebMvcAutoConfiguration.java就是用java编写SpringMVC的配置文件（和xml配置文件类似）

    - 该类上有以下注解

    - ```java
      @ConditionalOnWebApplication(type = Type.SERVLET)
      @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
      @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
      ```

    - 用于设置该配置类是否生效

      - 第一个注解表示该应用为WEB应用时生效
      - 第二个表示应用中存在Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class这三个类时生效，即如果没导入相关依赖应用中就没有这三个类，该配置类也就不生效了
      - 第三个注解表示系统中没有WebMvcConfigurationSupport.class时生效，即当用户完全接管SpringMVC配置时，会使用@EnableWebMvc注解，该注解会导入@Import(DelegatingWebMvcConfiguration.class)类，DelegatingWebMvcConfiguration.class继承自WebMvcConfigurationSupport类，这就使得应用中存在WebMvcConfigurationSupport.class类，从而导致自动配置失效，这就是@EnableWebMvc注解会使自动配置失效的原因

