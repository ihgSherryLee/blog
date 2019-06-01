你不知道的JavaScript(下)

2.4.1 对象属性赋值模式

```javascript
var {x:x, y:y, z:z} = obj
var {x, y, z} = obj
```

{x,...}是省略掉了x:部分还是:x部分？时间上是省略了x:部分