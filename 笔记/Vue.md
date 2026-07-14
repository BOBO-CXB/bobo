# Vue

### CDN

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>

<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

```javascript
 methods: {
            currentTime: function (){
                return Date.now();
            }
        },
 computed: {  /*计算属性 computed 与methods中的方法不能重名，重名只会调用Methods中的方法*/
            currentTime2: function (){
                return Date.now();
            }
        }

//计算属性相当于缓存，可以将不经常变化的计算结果进行缓存，当该计算属性内有内容变化时会刷新缓存，节约开销
```

