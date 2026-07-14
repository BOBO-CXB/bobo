# Ajax技术

### Asynchronous JavaScript And XML：异步的JavaScript和XML

### 是一种在无需重新加载整个页面的前提下，更新部分网页的技术



#### 利用iframe实现伪ajax：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>iframe测试体验页面无刷新</title>
    <script>
        function go(){
            //所有的值变量，提前获取
            var src = document.getElementById("url").value;
            document.getElementById("iframe1").src=src;
        }
    </script>
</head>
<body>

<div>
    <p>请输入地址:</p>
    <input type="text" id="url">
    <input type="button" value="提交"  onclick="go()"/>

</div>

<div style="margin-top: 50px">
    <iframe id="iframe1" style="width:100%;height: 500px">

    </iframe>
</div>

</body>
</html>
```

### 使用jQuery

**jQuery 是一个 JavaScript 库。**

**jQuery 极大地简化了 JavaScript 编程。**

- 导入jQuery文件

- 静态资源过滤

  ```xml
  <mvc:default-servlet-handler/>
  ```

jQuery-Ajax

```html
jQuery.ajax(...)/$.ajax()
      部分参数：
            url：请求地址
            type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
            data：要发送的数据
    contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        timeout：设置请求超时时间（毫秒）
      beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>jQuery</title>

    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.5.1.js"></script>
    <script>
      function a(){
        $.post({
          url:"${pageContext.request.contextPath}/jQuery"
          ,data:{"name":$("#username").val()}
          ,success:function (data){
            alert(data);
          }
        })
      }
    </script>


  </head>
  <body>

  <!--失去焦点时，发起一个请求到后台-->
  用户名:<input type="text" id="username" onblur="a()"/>
  </body>
</html>

```

```java
@RequestMapping("/jQuery")
    @ResponseBody
    public void jQuery_Test(String name, HttpServletResponse response) throws IOException {
        System.out.println("接收到的参数为:"+name);

        if("bobo".equals(name)){
            response.getWriter().print("true");
        }else {
            response.getWriter().print("false");
        }
    }
```

