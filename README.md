# fucking-front-end
一些自己找到的好的知识点学习链接

# javascript
### 模块化
[搞懂javascript模块化](https://juejin.cn/post/6844903636108066830#comment)  
一些关键点：  

演变过程：无模块化--commonJS--AMD--CMD--ES6 Module.     

无模块化的时候，这些变量都是直接挂载在window上，容易污染全局作用域，而且模块与模块之间的依赖并不清晰，如果一个文件需要引入A模块，而A模块又引入了B模块，那就必须要先引入B模块，再引入A模块才能正常。

commonJS是一种服务端的模块化标准。采用module.exports方式导出模块，require的方式导入模块。exports是module.exports的一个引用，或者说exports是一个指针，和module.exports指向同一块地址，因此如果直接给exports赋值，相当于使exports指向了一块其他的地址。commonJS采用**同步的方式加载模块**,因为他本身是一种服务端的规范，而服务端的数据都是存储在本地的，读取都非常快，所以该规范应用在服务端的话会很顺畅。但是应用在客户端，由于实时性并不好，所以其不能支持异步加载也成为了其缺陷。  

AMD和CMD使用require引入模块，使用define定义模块，他们使用**异步方式加载模块**。AMD与CMD的区别在于CMD实现了按需加载，AMD提倡的是**依赖前置**，require和define的第一个参数可以是数组，里面是他们的依赖，所以在AMD中会在执行模块内容之前预先加载好模块依赖的其他模块，但是在CMD中，会在执行模块代码的过程中，遇到其需要加载其他模块的时候，再去加载对应模块，实现**按需加载**。
