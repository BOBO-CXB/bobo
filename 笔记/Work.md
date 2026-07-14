# 工作笔记

```js
axios.request({
    url: `${prefix}/monitorWellInfo/pageMonitorWellInfo`,
    params: params,
    method: 'get'
  })

// `url` 是用于请求的服务器 URL
// `method` 是创建请求时使用的方法  常用get put post
// `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
// `data` 是作为请求主体被发送的数据
// 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
// 在没有设置 `transformRequest` 时，必须是以下类型之一：
// - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
// - 浏览器专属：FormData, File, Blob
// - Node 专属： Stream
// 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL baseURL: 'https://some-domain.com/api/',
```

其他参数见Axios部分。

### 后端结构

##### 以模块为单位进行分层，每层包含dao service  entity controller三层

- dao层负责接口定义
- service层调用dao层实现业务操作
- controller层调用service层，因为页面跳转主要由vue路由实现，controller层基本都用@RestController
  - Controller层原则上一律以ResponseDTO.ok(T)的形式进行返回；	前端使用时，res.data.content来获取实际的内容。

后端为前端提供一个请求接口：

```java
 /**
     * 分页获取监测井
     * @param requestPage
     * @param filter
     * @return
     */
    @GetMapping(value = "/pageMonitorWellInfo")
    public ResponseDTO pageMonitorWellInfo(@Valid RequestPage requestPage, MonitorWellInfo filter) {
        return ResponseDTO.ok(monitorWellInfoService.pageMonitorWellInfo(requestPage, filter));
    }
```

前端将请求进行封装导出,页面上可以通过调用pageMonitorWellInfo(pageNo, pageSize, filter)来进行前后端交互

```js
/**
 * 分页获取监测井
 * @param requestPage
 * @param filter
 * @return
 */
export const pageMonitorWellInfo = (pageNo, pageSize, filter) => {
  let prefix = adminUrlPrefix
  let params = Object.assign(filter, {
    pageNo: pageNo,
    pageSize: pageSize
  })
  return axios.request({
    url: `${prefix}/monitorWellInfo/pageMonitorWellInfo`,
    params: params,
    method: 'get'
  })
}
```

#### post请求参数前要加上@RequestBody或@requestparam注解

#### remove一般用put请求，因为是软删除，只是修改相应标志位

### ResponseDTO

```java
public class ResponseDTO<T> {
    private Boolean successFlag;  //成功标志位 true false
    private T content;            //内容
    private String message;       //信息  ServiceCode.XXX.getMessage();
    private Integer code;         //信息代码  ServiceCode.XXX.getCode();
```

枚举类 ServiceCode

```java
public enum ServiceCode {
    SUCCESS(0, "success"),
    UNKNOWN_FAILURE(1, "服务器错误"),
    UNKNOWN_USER(2, "未找到用户"),
    UNAUTHORIZED(3, "权限不足"),
    SAME_LOGIN_NAME_USER(4, "账号已存在"),
    SAME_PERMISSION_NAME(5, "权限名称重复"),
    SAME_ROLE_NAME(6, "角色名称重复"),
    SAME_ROLE_NAME_EN(7, "角色英文名称重复"),
    SAME_PERMISSION_CODE(8, "权限code重复"),
    BIZ_USER_MAPPING_NOT_EXIST(9, "业务系统用户映射关系不存在，请联系管理员"),
    NOT_SUPPORTED_SQL(100, "SQL语法错误或不支持的SQL类型"),
    ROLE_ALREADY_BIND(1000, "该角色已被分配给用户"),
    PERMISSION_ALREADY_BIND(1001, "该权限已被分配给角色"),
    SAME_DEPT_CODE(1002, "机构编号重复"),
    PARENT_DEPT_CANNOT_BE_SELF(2001, "父机构不能设置为自身"),
    PARENT_DEPT_CANNOT_BE_CHILD(2002, "不能将原有子机构设置为父机构"),
    DEPT_HAS_BIND_USER(2003, "该机构已绑定用户不可删除"),
    DEPT_HAS_CHILDREN(2004, "该机构存在子机构不可删除"),
    PERMISSION_HAS_CHILDREN(2005, "该权限存在子权限不可删除"),
    USER_DISABLED(40001, "该用户无法使用"),
    CHECK_INPUT_PASSWORD(4001, "请检查输入的密码是否正确"),
    NO_PLAN_SHIP(5001, "请选择船舶"),
    NO_PLAN_CODE(5002, "请输入编号"),
    NO_PLAN_PIER(5003, "请选择码头"),
    NO_PLAN_COMPANY(5004, "请选择公司");

    private Integer code;
    private String message;
```

常用方法：ResponseDTO.ok(cityService.getNtCity());  //可以没有返回的content内容

```java
    public static <T> ResponseDTO<T> ok(T content) {
        ResponseDTO dto = new ResponseDTO();
        dto.setContent(content);
        dto.successFlag = true;
        dto.message = ServiceCode.SUCCESS.getMessage();
        return dto;
    }
```

#### 在vue中使用rules对表单字段进行验证

```vue
<template>
  <Form ref="loginForm" :model="form" :rules="rules" @keydown.enter.native="handleSubmit">
    .......
      <FormItem prop="password">
      <Input type="password" v-model="form.password" placeholder="请输入密码" autocomplete="on">
        <span slot="prepend">
          <Icon :size="14" type="md-lock"></Icon>
        </span>
      </Input>
    </FormItem>
      .......
  </Form>
</template>
```

-  ref：表单被引用时的名称，标识  this.$refs.loginForm.
- model：表单数据对象 “:” 是指令 “v-bind”的缩写
-  rules：表单验证规则

```js
props: {
      userNameRules: {
        type: Array,
        default: () => {
          return [
            { required: true, message: '账号不能为空', trigger: 'blur' }
          ]
        }
      },
      passwordRules: {
        type: Array,
        default: () => {
          return [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ]
        }
      },
      loading: {
        type: Boolean,
        required: true
      }
    },
computed: {
      rules () {
        return {
          username: this.userNameRules,
          password: this.passwordRules
        }
      }
    }

// prop 以一种原始的值传入且需要进行转换。在这种情况下，最好使用这个 prop 的值来定义一个计算属性
```

-  prop：表单域 model 字段，在使用 validate、resetFields 方法的情况下，该属性是必填的 prop="password"



### 导航守卫

`router.beforeEach` 注册一个全局前置守卫

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 **等待中**。

```js
router.beforeEach((to, from, next) => {
  // 2020-09-12 拦截中加入参数
  // if (to.query.paramType) {
  //   next()
  //   return
  // }

  iView.LoadingBar.start()
  const token = session.accessToken

  beforeCount++

  //先验证token是否有效
  let ticket = getUrlKey('ticket')
```

即进入index.js文件中这段代码，首先获取token和ticket用于判断用户是否进行了身份验证

```js
 if (IPConfig.casEnable) {
    if (!ticket && !token) {
      //如果没有登录且没有ticket，则跳转到统一登录页面
      gotoCasLogin()
    } else {
      //如果有ticket，则先验证ticket是否有效，如果有效，则直接登录
      if (ticket) {
        //如果没有token，且有ticket，则先验证ticket是否有效，如果有效，则直接登录
        let serviceUrl = window.location.href.split("?")[0]

        validateTicket(serviceUrl, ticket).then(res => {
          if (res.data.successFlag) {
            let data = res.data.content
            if (!!data) {
              let username = data.user
              validateSuccessLogin(username).then(res2 => {
                //设置token信息
                session.initTokenData(res2.data.content)
                window.location = window.location.pathname
              }, err => {
                casLoginFail()
              })
            } else {
              casLoginFail()
            }
          }
        }, error => {
        })
      } else {
        //回登录页面
        redi(to, from, next, beforeCount, token)
      }
    }
  } else {
    redi(to, from, next, beforeCount, token)
  }
```

- IPConfig.casEnable          IPConfig.casEnable = false      //统一认证开关，不部署统一认证时，为false

  - 如果为false则直接进入  redi(to, from, next, beforeCount, token)
  - 如果为true则判断是否存在ticket和token，如果都不存在就跳转到统一认证登陆界面  gotoCasLogin()
  - 地址 IPConfig.ssoLoginUrl = `http://192.168.0.189:9001/#/login?serviceUrl=${IPConfig.frontUrl}`  //sso跳转的地址
  - IPConfig.frontUr为要进入的系统 IPConfig.frontUrl = 'http://localhost:8890/'   //前端页面访问的地址（土壤环境监测系统）
  - 如果有ticket则说明进行过统一身份验证，接着验证该ticket是否有效  如果有效，则直接登录  validateTicket(serviceUrl, ticket)
  - 登陆成功后生成token  session.initTokenData(res2.data.content)
  - 若失败则   casLoginFail() 显示登陆失败 gotoCasLogout()登出统一认证

- 为false则直接进入redi(to, from, next, beforeCount, token)

  ```js
  let redi = function(to, from, next, beforeCount, token)  {
    if (beforeCount == 1 && token) {
      //如果是首次打开页面，且session中有accessToken，则请求用户信息
      getUserInfo().then(res => {
        if(res.data) {
          session.initUserInfoData(res.data.content)
          toNextPage(to, from, next)
        }
      }, error => {
        //如果有错误，则跳转回登录页面
        session.clear()
        next( {
          name: LOGIN_PAGE_NAME
        })
      })
    } else {
      if( !token && to.name != LOGIN_PAGE_NAME && to.name != 'register') {
        next( {
          name: LOGIN_PAGE_NAME
        })
        iView.LoadingBar.finish()
      } else {
        toNextPage(to, from, next)
      }
    }
  }
  ```

  toNextPage(to, from, next)  判断用户是否有该路由权限，有就放行next（）否则输出提示路由错误

  ```js
  let toNextPage = function(to, from, next) {
    if (haveRouterPermission(to)) {
      next()
    } else {
      iView.LoadingBar.finish()
      Notice.warning({
        title: '路由错误',
        desc: '没有该路由的权限',
        duration: 3
      })
    }
  }
  ```

  全局后置钩子 afterEach

  和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航 

  ```js
  router.afterEach(to => {
    iView.LoadingBar.finish()
    window.scrollTo(0, 0)
  })
  ```

## 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。



### 端口被占用

netstat -ano|findstr 8881



### 重置自增ID

ALTER TABLE `monitor_enterprise_data` AUTO_INCREMENT = 1



### 解决前端传值String转Date异常

在相应字段上加上以下代码

```java
@JsonFormat(
        shape = JsonFormat.Shape.STRING,
        pattern = "yyyy-MM-dd HH:mm:ss",
        timezone = "GMT+8"
)
```

