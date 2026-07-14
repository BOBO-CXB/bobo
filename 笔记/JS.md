# JS

初始化执行：

```js
$(function (){  
    //页面初始化完成后执行
}
```

定时执行任务：

```js
setInterval(function(){  //每隔30秒初始化一次用户告警信息数量
        initUserAlarmCount();
    },30000);
```

**setTimeout和setInterval都属于JS中的定时器，可以规定延迟时间再执行某个操作，不同的是setTimeout在规定时间后执行完某个操作就停止了，而setInterval则可以一直循环下去。**