# JSON   (JavaScript Object Notation)

### 是一种轻量级的数据交换格式

### JSON是JS对象的字符串表示法，它使用文本表示一个JS对象的信息，本质是字符串。相当于一个toString()方法

### 

## JS和JSON的转换方法：

### JSON字符串转JS对象：

#### var obj = JSON.parse('{"a":"hello","b","word"}');

### JS对象转成JSON字符串：

#### var json = JSON.stringify({a:'hello',b:'word'});

```javascript
<script type="text/javascript">
    //编写一个js对象
    var user = {
        name:"波波",
        age:21,
        sex:"男"
    };
    //输出对象
    console.log(user);
    // 将js对象转化为JSON字符串
    var json = JSON.stringify(user);
    console.log(json); //{"name":"波波","age":21,"sex":"男"}

    //将JSON字符串转化为js对象
    var obj = JSON.parse(json);
    console.log(obj);

</script>
```



### 前后端分离时代：

​		后端部署后端，提供接口

​		前端独立部署，负责渲染后端的数据，展示给用户



### Controller返回JSON数据

### 可以用jackson,fastJson等工具，或手动重写toStringff

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.75</version>
</dependency>

```

```java
package com.kuang.controller;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.kuang.pojo.User;

import java.util.ArrayList;
import java.util.List;

public class FastJsonDemo {
   public static void main(String[] args) {
       //创建一个对象
       User user1 = new User("秦疆1号", 3, "男");
       User user2 = new User("秦疆2号", 3, "男");
       User user3 = new User("秦疆3号", 3, "男");
       User user4 = new User("秦疆4号", 3, "男");
       List<User> list = new ArrayList<User>();
       list.add(user1);
       list.add(user2);
       list.add(user3);
       list.add(user4);

       System.out.println("*******Java对象 转 JSON字符串*******");
       String str1 = JSON.toJSONString(list);
       System.out.println("JSON.toJSONString(list)==>"+str1);
       String str2 = JSON.toJSONString(user1);
       System.out.println("JSON.toJSONString(user1)==>"+str2);

       System.out.println("\n****** JSON字符串 转 Java对象*******");
       User jp_user1=JSON.parseObject(str2,User.class);
       System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

       System.out.println("\n****** Java对象 转 JSON对象 ******");
       JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
       System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

       System.out.println("\n****** JSON对象 转 Java对象 ******");
       User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
       System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);
  }
}
```



