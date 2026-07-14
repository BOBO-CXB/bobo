# Swagger

#### 前后端分离

后端时代：前端只用管理静态页面；html===》后端。模板引擎 JSP 后端是主力

前后端分离时代：

- 后端：后端控制层、服务层、数据访问层
- 前端：前端控制层，视图层
  - 伪造后端数据，json。已经存在了，不需要后端前端依旧可以跑起来
- 前后端交互=====》API (Application Programming Interface，应用程序接口) 
- 前后端相对独立，松耦合
- 前后端甚至可以部署在不同服务器上



问题：

- 前端人员和后端人员无法做到高效协同

解决方案：

- 首先指定schema【计划的提纲】，实时更新最新的API，降低集成风险
- 早些年：制定Word计划文档；
- 前后端分离：
  - 前端测试后端接口:Postman
  - 后端提供接口，需要实时更新最新的消息及改动



### Swagger

- 号称世界上最流行的API框架
- API文档与API定义同步更新
- 直接运行，可以在线测试API接口
- 支持多种语言



### 在项目中使用Swagger

1、导入相关依赖

#### springboot版本太高无法使用swagger2.9.2，启动时会报错，可以将springboot版本降到2.5.6

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

```

2、配置Swagger===》Springboot中没有的需要自己手动配置config(即没有直接导入starter的依赖)

```java
@Configuration
@EnableSwagger2  //开启swagger2
public class SwaggerConfig {
    
}
```

3、测试

访问http://localhost:8080/swagger-ui.html



4、配置Swagger信息

swagger 的bean实例Docket

```java
  public Docket(DocumentationType documentationType) {
    this.documentationType = documentationType;
  }
```

有一个有参构造函数，参数为DocumentationType

```java
public class DocumentationType extends SimplePluginMetadata {
  public static final DocumentationType SWAGGER_12 = new DocumentationType("swagger", "1.2");
  public static final DocumentationType SWAGGER_2 = new DocumentationType("swagger", "2.0");
  public static final DocumentationType SPRING_WEB = new DocumentationType("spring-web", "1.0");
```

传入SWAGGER_2，配置了docket的bean实例

```java
return new Docket(DocumentationType.SWAGGER_2);
```

docket有个字段apiInfo，用于显示api信息，默认信息如下：

```java
public static final ApiInfo DEFAULT = new ApiInfo("Api Documentation", "Api Documentation", "1.0", "urn:tos",
          DEFAULT_CONTACT, "Apache 2.0", "http://www.apache.org/licenses/LICENSE-2.0", new ArrayList<VendorExtension>());

```

docket提供一个方法.apiinfo用于设置apiinfo,返回的仍是Docket

```java
 public Docket apiInfo(ApiInfo apiInfo) {
    this.apiInfo = defaultIfAbsent(apiInfo, apiInfo);
    return this;
  }
```

ApiInfo构造方法：

```java
  public ApiInfo(
      String title,
      String description,
      String version,
      String termsOfServiceUrl,
      Contact contact,
      String license,
      String licenseUrl,
      Collection<VendorExtension> vendorExtensions) {
    this.title = title;
    this.description = description;
    this.version = version;
    this.termsOfServiceUrl = termsOfServiceUrl;
    this.contact = contact;
    this.license = license;
    this.licenseUrl = licenseUrl;
    this.vendorExtensions = newArrayList(vendorExtensions);
  }
```

修改自己所需的信息：

```java
//配置Swagger信息ApiInfo
    private ApiInfo apiInfo(){
        return new ApiInfo("BOBO的Swagger API文档", 
                "Api Documentation", 
                "1.0", 
                "http://yzu_bobo.top",
                new Contact("CHEN", "http://yzu_bobo.top", "821505517@qq.com"), /*作者信息*/
                "Apache 2.0", 
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList<VendorExtension>());

    }
```

#### Swagger配置扫描接口

```java
.select()
                    /*RequestHandlerSelectors,配置要扫描接口的方式  basePackage:要扫描的包*/
                    .apis(RequestHandlerSelectors.basePackage("com.yzu.controller"))
                    .paths(PathSelectors.ant("/bobo/**"))//请求路径过滤只显示请求为/bobo开始的接口
                        .build();//
```

#### 配置是否启动

```java
.enable(false)
```

可以在多环境配置下，设置在研发、测试环境下启动，产品环境不启动

```java
 Profiles profiles = Profiles.of("dev","test");//设置可以显示swagger的环境
        
 /* 获取项目环境*/
 boolean flag = environment.acceptsProfiles(profiles);//判断当前环境是否处在上面设定的两个环境中
```

#### API分组

```java
.groupName("BOBO")
```

配置多个分组===》设置多个docket实例即可

```java
	@Bean
    public Docket docket_CHEN(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("CHENXINBO");
    }


    @Bean
    public Docket docket(Environment environment){

        Profiles profiles = Profiles.of("dev","test");//设置可以显示swagger的环境
```

#### 实体类

只要我们的接口的返回值中存在实体类，它就会被扫描到swagger中

可以给实体类或字段增加注释

```java
@ApiModel("用户信息表")
public class UserInfo {
    @ApiModelProperty("用户ID")
    private String userid;
    ...
        
```

拓展：

```java
@GetMapping("/queryAllUser")
@ApiOperation("获取所有用户")    //给方法加注释
    public List<UserInfo> queryAllUser(){
        List<UserInfo> userInfos = userService.queryAllUser();
        return userInfos;
    }
```



### 可以进行接口测试



### #在正式发布时要关闭Swagger！！！#