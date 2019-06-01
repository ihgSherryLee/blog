前端知识复习or面试点

- css的盒子模型

  盒子模型由margin、border、padding、content组成.

  W3C 标准盒模型:属性width,height只包含内容content，不包含border和padding。IE 盒模型：属性width,height包含border和padding，指的是content+padding+border。

  ie8+浏览器使用css属性box-sizing控制使用哪个盒模式，默认为content-box即标准模型。

  不声明DOCTYPE类型，IE浏览器会将盒子模型解释为IE盒子模型，FireFox等会将其解释为W3C盒子模型；若在页面中声明了DOCTYPE类型，所有的浏览器都会把盒模型解释为W3C盒模型。

- js闭包

  闭包是函数和声明该函数的词法环境的组合。这个环境包含了这个闭包创建时所能访问的所有局部变量。闭包就是能够读取其他函数内部变量的函数。可以把闭包简单理解成"定义在一个函数内部的函数"。闭包最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。函数运行完毕后继续访问这个函数作用域

- 