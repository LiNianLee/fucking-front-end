# fucking-front-end
一些自己找到的好的知识点学习链接

# javascript

### 原型与原型链
每个函数都有一个prototype属性，这个属性实际指向一个对象，如果用这个函数创建一个实例，那么那个对象就称为这个实例的原型。当无法直接从实例上读取到想要的方法/变量时，就会从实例的原型上面查找，若还是无法找到，就会去找原型的原型，这样依次往上就可以构成一条原型链。
这一个部分主要是记住这一张图  
<img width="548" alt="截屏2021-11-17 下午2 21 44" src="https://user-images.githubusercontent.com/37279552/142145786-32f6d788-d0eb-4b69-ba76-56956b59d9ad.png">  
以及另外一个重要的知识点：Function.__proto__ = Function.prototype.  
对于这个点的理解是：Function作为一个内置对象，是本身就已经有的，因为它本身又是一个对象，为了与其他对象保持一致，所以就有了上述的关系，但是不能说因为有上述关系，就说Function调用自己，自己生成自己。

### 词法作用域与动态作用域
javascript采用的是此法作用域，这是一种静态作用域，**静态作用域是指函数的作用域在函数定义时就决定了**。与之对应的动态作用域是指**函数的作用域在函数调用时决定**。  
下面这段代码可以很好地用来诠释静态作用域特点所起到的作用
```
  var value = 1;
  function foo() {
    console.log(value);
  }
  function bar() {
    var value = 2;
    foo();
  }
  bar();
```
因为javascript的静态作用域特性使函数的作用域在定义时就决定了，那foo这个函数是在什么时候被定义的呢？那就是在全局下面，所以对于foo来说，他如果要查找value这个变量，首先会查找函数内部，如果没有这个变量，就会到函数的外部环境中查找，也就是全局作用域中进行查找，所以找到的value为1.而如果是动态作用域，在调用时决定其作用域，此时打印的value为2.

### 模块化
[搞懂javascript模块化](https://juejin.cn/post/6844903636108066830#comment)  
一些关键点：  

演变过程：无模块化--commonJS--AMD--CMD--ES6 Module.     

无模块化的时候，这些变量都是直接挂载在window上，容易污染全局作用域，而且模块与模块之间的依赖并不清晰，如果一个文件需要引入A模块，而A模块又引入了B模块，那就必须要先引入B模块，再引入A模块才能正常。

commonJS是一种服务端的模块化标准。采用module.exports方式导出模块，require的方式导入模块。exports是module.exports的一个引用，或者说exports是一个指针，和module.exports指向同一块地址，因此如果直接给exports赋值，相当于使exports指向了一块其他的地址。commonJS采用**同步的方式加载模块**,因为他本身是一种服务端的规范，而服务端的数据都是存储在本地的，读取都非常快，所以该规范应用在服务端的话会很顺畅。但是应用在客户端，由于实时性并不好，所以其不能支持异步加载也成为了其缺陷。  

AMD和CMD使用require引入模块，使用define定义模块，他们使用**异步方式加载模块**。AMD与CMD的区别在于CMD实现了按需加载，AMD提倡的是**依赖前置**，require和define的第一个参数可以是数组，里面是他们的依赖，所以在AMD中会在执行模块内容之前预先加载好模块依赖的其他模块，但是在CMD中，会在执行模块代码的过程中，遇到其需要加载其他模块的时候，再去加载对应模块，实现**按需加载**。 

ES6module的加载分成三个部分，他首先会在程序开始前根据模块关系找到所有模块，然后形成一个无环的模块关系表，然后把所有模块实例建好，这样其实就已经能够避免循环引用了，同时在ES6module里面也是存在cache的，即重复import同一个模块，只执行一次代码。在第二步中，会在内存中腾出来一块空间，给那些即将要export出来的东西，import和export就指向这部分内存空间，这部分过程称为连接。在第三部分运行模块时会把生成的值写到第二部分开辟的空间中。而且在ESModle中，如果被export的值被修改了，是会被反映在import里面的，这个功能的实现就是living bound。

ES6module与commonJS的区别在于，ES6module是在编译时加载，比如在文件A中对模块K进行了引入，那在对文件A进行编译时，就会去加载这个模块K，加载的结果是对这个模块的一个引用，而之后在文件中针对这个模块K中的内容进行使用时，会实时地通过这个引用去拿模块内的数据。而commonJS是在运行时加载，且**会在运行之前就被写入cache**，所以如果同一个模块如果被require多次，模块内部只会执行一次，module.exports出来的是同一个对这个模块的引用。同时这种运行之前被写入cache有一个好处是用来解决循环引用，比如文件A引入了模块K，模块K引入了模块M，而模块M又引入了模块K，因为commonJS是同步执行的，所以按顺序在执行到模块M的时候，发现要引入模块K，模块K虽然暂时还没有执行，但是已经能够在内存中查找到，所以模块M在使用模块K中的内容时，拿到的是undefined。而在ESModule中，由于在第二步已经为模块占了坑，但是并没有为这个坑填上值，所以当出现了循环引用时，采用import * as s fron '../M.js'的方式不会报错，打印s会显示模块里面有的东西，但这些东西是没有值的，但是如果使用import {a1, a2} from '../M.js'就会报错，因为不可以在赋值之前使用这些东西。另一个不同点在于commonJS是对整个模块整体进行引入，但是ESModule可以实现按需加载，你需要什么，就引出什么。
[深度好文](https://blog.csdn.net/xgangzai/article/details/106935104)

### 关于模块化过程中的循环加载
[循环引用](https://es6.ruanyifeng.com/#docs/module-loader#%E5%BE%AA%E7%8E%AF%E5%8A%A0%E8%BD%BD)  
简单来讲就是，commonJS这种是在加载时执行，且只导出已经执行的部分，对于没有执行的部分就不会导出。加载时执行是说如果一个文件引入了模块M，那么在那个文件引入M的时候，就会去加载M，同时执行M文件中的内容，最终生成一个对象，对象示例如下
```
{
  id: '....',
  exports: {....},
  loaded: true
}
```  
其中，id用来标识模块名称，exports中包含了导出的接口，loaded标识这个模块是否执行完毕。如果有另外一个文件在后面的流程中也引用了模块M，引入时不会再重新执行M，而是直接从这个对象中取。  
ES6Module则是采用动态引入，比如从M中引入一个函数foo，他其实是生成了一个指向这个模块的引用，等到真正需要使用这个函数/变量的时候，再去模块中拿这个函数/变量。同时因为ES6Module中对于函数声明形式的函数定义具有变量提升的作用，也可以在一定程度上避免循环引用的报错。


### 执行上下文
[深度好文](https://github.com/kuitos/kuitos.github.io/issues/18)
执行上下文的代码会分成两个部分进行处理：进入执行上下文，代码执行  

在进入执行上下文的时候，还没有执行代码，这个时候的变量对象包括：函数形参，函数声明和变量声明，其中针对函数声明，如果已经有变量存在完全相同的变量名称，会对那个变量的值进行替换。对于变量声明，如果变量名称与形参或者函数相同，这个变量声明是不会干扰已经存在的形参和函数的。  

在代码执行阶段，会顺序执行代码，根据代码修改变量对象的值。  

下面两段代码可以比较好地诠释这种关系。  
```
console.log(a);
var a = 1;
function a () {
  // ...
}
```
上面这段代码的打印结果会是一个函数，刚开始进入函数上下文中，首先会去处理函数声明，所以此时a就会是一个函数，然后去处理变量声明，但是发现已经存在a这个函数变量了，所以并不会影响a原来的函数声明。然后到了代码执行阶段，顺序执行代码，此时的a就是一个函数。  

再对比下下面这段代码  
```
var a = 1;
function a () {
  // ...
}
console.log(a);
```
这段代码执行结果为1，前面函数上下文部分与第一段代码保持一致，也就是说在执行上下文阶段，a还是一个函数，但是到了代码执行阶段，会从上至下执行代码，此时执行到a = 1的时候，就会给变量a赋值，变量对象的值改变了，所以最终打印出来的结果也发生了变化。**但这个变化是在代码执行阶段发生的。**  

**每一个执行上下文都会分配一个变量对象，变量对象的属性由 变量 和 函数声明构成。在函数上下文情况下，参数列表也会加入到变量对象中作为属性。不同作用域的变量对象互不相同，它保存了当前作用域的所有函数和变量。**  

**这里需要注意的一点是，只有函数声明会被加入到对变量对象中，函数表达式不会。**  

下面用一个例子来说明一下**执行上下文，变量对象，作用域链**之间的关系。  
```
  var scope = 'global scope';
  function checkScope() {
    var scope = 'local Scope';
    function f() {
      return scope;
    }
    return f();
  }
  checkScope();
  
  var scope = 'global scope';
  function checkScope() {
    var scope = 'local Scope';
    function f() {
      return scope;
    }
    return f;
  }
  checkScope()();
```  
首先需要明确的是，上面两段代码他的执行结果都是local scope，因为javascript中使用的是静态作用域，静态作用域就是函数的作用域在被定义的时候决定，f函数中使用了scope变量，而f函数是在checkScope函数中被定义的，所以f函数在顺着作用域链查找scope变量时，最先找到的是定义在checkScope内部scope变量，所以两者的打印结果一致。但是上面两段代码的区别在哪里？  

首先看第一段代码，进入全局上下文，所以这个时候全局环境被压入执行上下文栈中，然后对全局上下文环境进行初始化，此时作用域链中保存的是全局作用域。 
同时checkScope函数被创建，此时作用域链会被保存到函数的[[scope]]属性中。  
然后开始执行checkScope函数，进入到checkScope函数的上下文中，此时该函数就会被压入执行上下文栈中，然后这个函数被初始化，他会复制上面[[scope]]属性中的作用域链，在自己拥有了变量对象之后，就会将这个变量对象压入作用域链的前端。如此，就到了函数f,所以也是会先创建函数f,同样，这个函数也会复制已有的作用域链到自己的scope属性中。然后开始执行f函数，把f函数压入执行栈中中，创建变量对象，并且将变量对象压入到作用域链的最前端。所以这也解释了为什么javascript的函数作用域是根据函数定义的位置来决定，因为函数在查找变量的时候，是根据作用域链一层一层向上查找的，就算他的外层函数被销毁了，也就是说从执行栈中被弹出了，但这个外层函数仍然存在于内部函数的作用域链中，因此，内部函数也就依然能够访问到外部函数所定义的一些变量。  

**所以总结一下就是，我们最开始遇到的是全局作用域，作用域链中有的也是全局对象。然后遇到函数，就会创建这个函数，并且会把当前的作用域链保存在函数的[[scope]]属性中，等到函数被执行的时候，进入到函数的上下文中，函数就会被压入到执行上下文栈，并且这个函数的变量对象也会被压入到作用域链的前端。**


### 作用域链
当查找变量的时候，会先在当前执行上下文的变量对象中进行查找，如果找不到就会到父级执行上下文的变量对象中找，一直层层往上，直到查找到全局作用域的执行上下文，这样由多个执行上下文的变量对象组合成的链就叫做作用域链。  


### this  
**this的指向完全取决于他在什么地方以什么方式进行调用，而不是创建的时候**  
首先要搞清楚关于this的四种绑定原则  

**如果函数被new修饰**，则this指向新创建的对象  
**如果函数被call、apply、bind等调用**，这是一种显式绑定，this指向的是那个绑定的对象  
**如果函数是在 某个上下文对象下 调用**，这是一种隐性绑定，this指向的就是这个上下文对象  
**如果定义了一个函数，直接调用，这种就是默认绑定，this指向window对象**  
**箭头函数**，箭头函数的指向与外层父函数的this指向保持一致，所以这里有一个要点就是，箭头函数外层必须要有一个父函数，否则箭头函数的this指向的就是window对象。且箭头函数的this指向一旦确定，就不会再更改了  

### 手写call
手写call的过程中有下面几个关键点需要注意：  
1、用什么方法获取外面传进来的参数，因为arguments并不是一个严格的数组类型，这里首先是用args数组存储了各个arguments参数，然后向eval传递一个拼接的字符串，eval会执行这个字符串。  
2、第二点需要注意的是，如果给call的第一个参数传null的话，this会默认指向window，但是在node中没有window，所以需要多加一重判断，如果没有window的话，就传递一个空对象    
3、call函数执行是可能存在返回值的，所以需要把func的执行结果return出去
```
Function.prototype.myCall = function (context) {
  var context = context || (typeof window === 'undefined' ? {} : window);
  context.func = this; // 目标函数
  var args = [];
  for (let i = 1; i < arguments.length; i++) {
    args.push('arguments[' + i + ']'); // 最终拼接的效果是：args = [arguments[1], arguments[2], arguments[3]]
  }
  var obj = eval('context.func(' + args + ')');
  delete context.func;
  return obj;
}

var obj = {
  value: 1
}

function print (p1, p2, p3) {
  console.log(this.value);
  console.log(p1 + p2 + p3);
}

print.myCall(null, 'a', 'b', 'c');
```

### 手写apply
```
Function.prototype.myApply = function (context, args) {
  var context = context || (typeof context === 'undefined' ? {} : window);
  context.fn = this;

  // 下面这种方式对外面参数的处理采用的是ES6的结构赋值
  // var obj = context.fn(...(args || [])); 
  var result;
  if (!args || !args.length) {
    result = context.fn();
  } else {
    var argsArr = [];
    for (let i = 0; i < args.length; i++) {
      argsArr.push('args[' + i + ']');
    }
    result = eval('context.fn(' + argsArr + ')');
  }
  delete context.fn;
  return result;
}

var obj = {
  value: 1
}

function print (p1, p2, p3) {
  console.log(this.value);
  console.log(p1 + p2 + p3);
}

print.myApply(obj, [1, 2, 3]);
```

### 手写bind
写bind要注意下面这几个关键点：  
1、调用bind之后返回的是一个函数，函数可以直接调用，也可以作为构造函数使用，当作为构造函数使用时，会新建一个对象，这个对象才是最终会指向的this，这一步在生成函数绑定this的时候要进行兼容  
2、生成的新函数应该与外面的函数的原型对象保持一致，但是为了防止对新函数的原型对象进行修改时也修改了外面函数的原型对象，所以可以利用一个空函数来进行转接 这个空函数就是下面的tempFunc。  
```
  Function.prototype.myBind = function (context) {
  var self = this;
  var args = Array.prototype.slice.call(arguments, 1);

  var tempFunc = function () {};

  var func = function () {
    var args2 = Array.prototype.slice.call(arguments);
    // 这里需要注意，self最终要绑定的 真的是context吗？因为返回的函数是可以作为构造函数来创建对象的
    // 这个时候绑定的对象应该是刚刚创建的新对象
    return self.apply(this instanceof func ? this : context, (args.concat(args2)));
  }

  // 这一步是为了避免 修改func的prototype也影响了原来函数的prototype
  tempFunc.prototype = self.prototype;
  func.prototype = new tempFunc();
  return func;
}

var obj = {
  value: 1
}

function print (a, b, c, d, e, f) {
  this.menu = '下滑';
  console.log(this.value);
  console.log(a + b + c);
  console.log(d + '' + e + '' + f);
}

const func = print.bind(obj, 1, 2, 3);
const newObj = new func(4, 5, 6);
console.log(newObj.menu);
```

### 手写new  
new这个修饰符主要做了下面四件事：  
1、新创建了一个对象。  
2、修改这个对象的__prop__属性。  
3、修改构造函数的this指针。 
4、返回一个对象。 
注意看第四点，这里写的是返回一个对象，并没有说一定返回的就是那个刚刚新创建的对象，因为构造函数在执行的时候是可能存在返回值的。如果返回值是一个对象，那么就把这个对象原样进行返回，如果构造函数执行后没有返回值，或者返回值是一个基本数据类型，才返回那个刚刚新建的对象。  
```
function myNew() {
  var obj = new Object();
  c = [].shift.call(arguments);
  obj.__proto__ = Constructor.prototype;
  var ret = Constructor.apply(obj, arguments);
  return typeof ret === 'object' ? ret : obj;
}
```  

### 创建对象的多种方式
[创建对象的多种方式](https://github.com/mqyqingfeng/Blog/issues/15)

### 继承的多种方式
[继承的多种方式](https://github.com/mqyqingfeng/Blog/issues/16)  
继承的终极解决方案：寄生组合式继承，既只用调用一次父构造函数，从而避免了在Parent.prototype上创建多余的不必要的属性，同时还能保持原型链的正确性，并且能够正常使用instanceof和isPrototypeOf。  
```
function Parent () {
  this.name = 'Kelvin',
  this.colors = ['red', 'blue', 'black']
}

Parent.prototype.sayName = function () {
  console.log(this.name);
}

function Child (age) {
  Parent.call(this);
  this.age = age;
}

function createPrototype (obj) {
  function f () {};
  f.prototype = obj;
  return new f();
}

// 这样就完成了子类继承父类的操作
function createExtend (Child, Parent) {
  var proto = createPrototype(Parent.prototype);
  proto.constructor = Child;
  Child.prototype = proto;
}

createExtend(Child, Parent);

Child.prototype.sayAge = function () {
  console.log(this.age);
}

const child1 = new Child(18);

console.log(child1.name);
console.log(child1.age);
console.log(child1.colors);
child1.sayName();
child1.sayAge();
```

### 防抖  
防抖的意思是你尽管触发，我一定要在最后一次触发之后等待wait的时间再执行代码
```
function debounce (callback, wait, immediate) {
  var context, args;
  var timer = null;
  return function () {
    context = this;
    args = arguments;

    if (timer) {
      clearTimeout(timer);
    }

    if (immediate) {
      var tag = !timer;
      timer = setTimeout(() => {
        timer = null;
      }, wait);
      if (tag) {
        callback.apply(context, args);
      }
    } else {
      timer = setTimeout(() => {
        callback.apply(context, args);
      }, wait)
    }

  }
}
```  
上面的代码中支持传递immediate参数，用来控制功能是否立即执行，如果immediate为true的话，其实他只会反复执行if(immediate)里面的代码，怎么执行呢，那肯定是要有一个tag,只有当这个tag为true的时候才能去执行回调函数，那tag什么时候为true呢，那就是当timer为空，也就是一轮倒计时结束之后，就表示可以执行下一个immediate了  

### 节流
节流的意思是说，每隔一段时间，触发一次功能。节流有两种实现方式，一种是时间戳，一种是定时器，也可以两种结合，通过传递可配置参数来决定功能是在一开始就执行，还是最后一次操作之后执行还是两者都执行。首先看下终极版本的代码。  
```
function throttleV4 (callback, wait, options) {
  var context, args;
  var timer = null;
  var previous = 0;
  var opt = {};
  if (options) {
    opt = options;
  }

  var later = function () {
    previous = opt.isHeading ? +new Date() : 0; // 这里是为了和下面throttled函数里面对previous的复制保持一致，如果isHeading为false，previous才为0，下次throttled的时候才能走到
    // if (!previous && !options.isHeading)这个判断里面去
    timer = null;
    callback.apply(context, args);
  }

  var throttled =  function () {
    context = this;
    args = arguments;
    var now = +new Date();
    if (!previous && !options.isHeading) {
      previous = now;
    }
    var remain = wait - (now - previous);
    // 表示需要立即执行
    if (remain <=0 || remain > wait) {
      if (timer) {
        clearTimeout(timer);
        timer = null;
      }
      previous = now;
      callback.apply(context, args);
    } else if (!timer && opt.isTail) {
      timer = setTimeout(later, remain);
    }
  }

  throttled.cancel = function () {
    clearTimeout(timer);
    timer = null;
    previous = 0;
  }
  return throttled;
}
```
### defer和async 
defer和async是用来加在script脚本上的标签，在不加这些标签的时候，script脚本的下载，解析和执行是会阻塞整个document的解析的，也就是说在解析文档的过程中如果遇到了script脚本，会暂停document的解析，转而去下载执行脚本。而在加了这两个标签之后会产生一些变化。
如果增加的是defer标签，他不会阻塞document的解析，这个属性会在解析document的同时进行下载，并且在DOMContentLoaded事件(原始的HTML文档被全部加载和解析完成)之前顺序执行这些defer脚本，顺序执行脚本就是说如果有多个脚本加了defer，他们的执行顺序是按照他们在文档中的顺序。 
async同样不会阻塞document的解析，他也会在document解析的同时去下载，只是文件在下载完成之后就会立即执行，没有固定的先后顺序，多个家乐async标签的脚本，谁先下载完了谁就会先执行。注意，脚本下载的时候是不会打断document的解析的，但是脚本在执行的时候是会打断document的解析的。

### React组件通信  
[参考文章](https://zhuanlan.zhihu.com/p/326254966)

父子组件之间的通信  
1、props  
2、父组件在子组件上安装ref={foo => {this.foo = foo}} 父组件给子组件传递了一个ref属性，父组件能通过这个ref属性拿到子组件实例，从而调用子组件中方法  

子组件向父组件通信 
1、回调函数，父组件中定义一个函数，并且向子组件传递这个函数，在子组件中调用该函数时传递具体的参数，这样父组件就能拿到子组件的参数。  

兄弟组件：  
1、通过公共父组件传值  
2、Context：用来解决组件层级嵌套过深的问题，比如一个数据在许多组件中都要被用到，而组件之间的层层嵌套很深，如果通过props一层一层进行传递的话就会很麻烦。这个时候使用React.crateContext就创建了一个上下文容器，这个容器它本身也是一个组件，所以对于想要传递的数据，利用context.Provider包裹一下，在这个组件之下的所有其他组件就都可以拿到。对于class之类的有状态组件，可以通过this.context拿到，对于function等无状态组件，则通过context.consumer包裹子组件来获取数据。  


### 创建对象的方式

1、工厂模式(无法识别对象类型)  
2、构造函数模式(每创建一个对象，方法都要被重新创建一次)  
3、原型模式(共享所有的属性和方法，无法初始化数据，且对于一些引用类型的数据，一个对象对它的更改会导致所有对象都受到影响) 
4、组合模式：构造模式和组合模式的结合，该共享的共享，该私有的私有，并且将原型对象的constructor手动指向构造函数  

### 继承的多种方式  

1、原型链继承:子类的原型链继承父类实例。(共享了父类的所有属性和方法，一个子类实例对引用类型数据的修改会影响其他子类实例)  
2、构造函数继承：子类构造函数利用Parent.call(this, params),实现对父类的继承，并且可以对父类传递参数。缺点是父类的所有东西，无论是属性还是方法，都要写在构造函数里面(因为这样子类才能继承啊喂！),那每创建一个子类实例，都会要创建新的方法  
3、组合继承，构造函数继承和原型链继承的结合  
```
function Parent(name){
  this.name = name;
}
Parent.prototype.getName = function () {
  ///
}
function Child (name) {
  Parent.call(this, name);
}
Child.protoype = new Parent();
Child.prototype.constructor = Child;
```  
为什么这种组合继承的方式能有效规避前两种继承方式的缺点呢？首先在父类中是实现了私有/共享数据的分离的，该私有的，放到构造函数里面，该共享的，放到原型链上。但是Child的原型对象是Parent的实例，不是意味着Child的原型链上其实也是存在那些私有属性的吗？的确是存在的，但是因为Child的构造函数中也存在这些属性，所以在真正使用子类创建实例，并且访问实例中的对象的时候，会先去访问构造函数中变量，找不到再顺着原型链找，所以原型链上的那些私有属性虽然是存在的，但是由于构造函数中也有一模一样的一份，原型链上的是不会被访问到的。这种方法的缺点是需要调用两次父类构造函数。  
4、原型继承  
```
function createObj (o) {
  function F(){};
  F.prototype = o;
  return new F();
}
``` 
这个其实就是ES5的Object.create的实现，他的缺点和原型继承一样  
5、寄生继承（一直无法理解这种方式好在哪儿。。。）
```
function createObj (o) {
  var clone = Object.create(o);
  clone.sayName = function () {};
  return clone;
}
```  
缺点是每次调用里面的sayName函数都会重新创建  
6、寄生组合继承  
```
function Parent (name) {this.name = name;}
Parent.prototype.getName = function () {};

function Child (name) {
  Parent.call(this, name);
}
function F () {};
F.prototype = Parent.prototype;
Child.prototype = new F();
Child.prototype.constructor = Child;
```
### React context和Mobx context

### React 事件机制
首先，当我们写下一段JSX：在一个button元素上，绑定了onClick事件，会发生什么？Babel会转译这段JSX，最终转成一个fiber对象，其中的memoizedProps和pendingProps属性会保存我们这次的onClick事件。  

然后通过控制台可以看到，button元素上绑定了document和button两个事件监听，在button的事件监听内，handler并不是我们传入的函数，而是一个noop(空函数)，真正的事件是被绑定在document上面的。  

到这一步，有几个结论需要记住：  
1、我们在JSX上注册的事件，并不会被绑定到真实的DOM上，而是会统一绑定到document上面。  
2、真实DOM上的事件，会被替换成noop空函数。  
3、我们绑定在DOM上的某些事件，比如onChange,绑定在document上面时，可能会有多个事件与之对应。  
4、注意，这种绑定是一种按需绑定，也就是说，我发现需要绑定click事件才会去绑定。  

为什么要采取这种合成事件的模式呢？  
1、避免了多个事件直接绑定在原生DOM上而产生一些无法预料的冲突。  
2、形成统一的事件体系来抹平浏览器之间的差异性。  

#### 事件如何合成的
**namesToPlugin**装事件名和事件模块插件的映射
```
namesToPlugin = {
  SimpleEventPlugin,  // 处理事件的插件
  EnterLeaveEventPlugin,  // 处理事件的插件
  ChangeEventPlugin,  // 处理事件的插件
  SelectEventPlugin,  // 处理事件的插件
  BeforeInputEventPlugin,  // 处理事件的插件
}
```  
**registrationNameModules**记录**合成事件**与事件插件的关系
```
registrationNameModules = {
  onBlur: SimpleEventPlugin,
  onClick: SimpleEventPlugin,
  onClickCapture: SimpleEventPlugin,
  onChange: ChangeEventPlugin,
  onChangeCapture: ChangeEventPlugin,
  onMouseEnter: EnterLeaveEventPlugin,
  onMouseLeave: EnterLeaveEventPlugin,
}
```  
**事件插件里面有什么？**  
```
const SimpleEventPlugin = {
    eventTypes:{ 
        'click':{ /* 处理点击事件  */
            phasedRegistrationNames:{
                bubbled: 'onClick',       // 对应的事件冒泡 - onClick 
                captured:'onClickCapture' //对应事件捕获阶段 - onClickCapture
            },
            dependencies: ['click'], //事件依赖
            ...
        },
        'blur':{ /* 处理失去焦点事件 */ },
        ...
    }
    extractEvents:function(topLevelType,targetInst,){ /* eventTypes 里面的事件对应的统一事件处理函数，接下来会重点讲到 */ }
}
```  
首先，extractEvents是一个事件统一处理函数，eventTypes里面保存了原生事件名与对应的配置项之间的关系。  

**registrationnameDependencies**记录合成事件与原生事件之间的关系  
```
{
  onBlur: ['blur'],
  onClick: ['click'],
  onClickCapture: ['click'],
  onChange: ['blur', 'change', 'click', 'focus', 'input', 'keydown', 'keyup', 'selectionchange'],
  onMouseEnter: ['mouseout', 'mouseover'],
  onMouseLeave: ['mouseout', 'mouseover'],
  ...
}
```  
**上面所提到的合成事件与事件插件，合成事件与原生事件之间的映射关系，是在事件初始化的时候完成的。**  


#### 事件如何绑定的  
当我们在JSX中给一个元素绑定了一个事件，React在diff阶段，发现是**HostComponent**类型的fiber，就会用diffProperties进行单独处理。在接下来的一系列流程里面，会利用前面事件初始化阶段形成的合成事件-事件插件和合成事件-原生事件之间的映射关系，将这个绑定在fiber元素上的合成事件，拆分成其对应的原生事件，然后判断原生事件的类型，大部分事件按冒泡逻辑处理，少部分如scroll，focus，blur等按照捕获逻辑进行处理(无论是onClick还是onClickCapture都是发生在冒泡阶段)。这样在这个部分中，就拿到了我绑定在这个元素上的合成事件所对应的所有原生事件依赖。然后遍历这个原生事件依赖，逐个进行下面的处理：先绑定一下统一的事件处理函数dispatchEvent，dispatchEvent.bind(null, topLevelType, eventSystemFlags, targetContainer)，其实这一步绑定是为了把container，原生事件名称，冒泡等属性传到事件处理函数中去。最后再将处理好的统一的事件处理函数dispatchEvent(也就是说，这时候所有原生事件的listener都是dispatchEvent)，绑定在document上(当然前提是这个原生事件之前没有被绑定过)。React并没有将我们业务逻辑里的listener绑定在原生事件上。  
1、我们将我们需要的事件最终都注册到了document上面。  
2、所有原生事件的listener都是dispatchEvent，他并没有和我们业务逻辑里的listener扯上什么关系  
3、同一个类型的事件只会绑定一次，最终反映在DOM事件上的只有dispatchEvent这一个listener,就算后来我们的页面发生了重大变化，React什么都不需要做，不用去removeEventListener之类  


#### 事件如何触发的  
**一次点击事件，在react底层发生了什么？**  

首先点击发生的时候，上面为原生事件绑定的统一的listener：dispatchListener会被率先执行，在这个函数里面，会最终调用batchedEventUpdates批量更新函数，而batchedEventUpdates函数中最终会执行handleTopLevel函数，在这个函数里面会做什么事情呢？
```
  function handleTopLevel(bookKeeping){
    const { topLevelType,targetInst,nativeEvent,eventTarget, eventSystemFlags} = bookKeeping
    for(let i=0; i < plugins.length;i++ ){
        const possiblePlugin = plugins[i];
        /* 找到对应的事件插件，形成对应的合成event，形成事件执行队列  */
        const  extractedEvents = possiblePlugin.extractEvents(topLevelType,targetInst,nativeEvent,eventTarget,eventSystemFlags)  
    }
    if (extractedEvents) {
        events = accumulateInto(events, extractedEvents);
    }
    /* 执行事件处理函数 */
    runEventsInBatch(events);
}
```  
可以看到，我们可以从前一步生成的bookkeeping中拿到此次触发所对应的原生事件，以及相关的fiber节点，然后我会去遍历事件插件plugin数组，判断这个事件插件是不是需要处理这个类型的事件。这一步的具体内容是在possiblePlugin.extractEvents这个函数里。他首先会问一下，我这个事件插件是处理这个原生事件的插件吗？不是，那就直接return，如果是的话，那我拿着这个事件插件，去生成一个事件源对象，怎么生成事件源对象？首先我根据原生事件，决定我要生成的是哪一种合成事件类型(原生event的合成类型)，然后拿着这个合成事件类型去事件池里面找，如果事件池里面有这个合成事件类型的实例，那我复用这个实例，如果没有我就生成一个这种事件类型实例。  

所以extractEvents这个函数里面做的事情可以概括成下面四个点：  
1、首先产生一个事件源对象，就是上面说的合成事件类型实例(里面封装了比如stopPropagation和preventDefault等方法，这样我们就不用跨浏览器单独处理兼容问题，而是统一交给react底层进行处理了)  
2、然后从事件源开始逐渐往上，查找DOM元素类型hostComponent对应的fiber，收集上面的react合成事件  
3、这个时候会维护一个函数执行队列，如果是捕获阶段的处理函数，会使用unshift函数将这个处理函数添加到函数执行队列的前面，如果是冒泡阶段的处理函数，则是通过push添加到函数执行队列的末尾
4、最后将函数执行队列挂载到事件源对象上  

这个地方还有一个点要注意的是：React的冒泡和捕获并不是真正的DOM级别的，而是在fiber上面，可以看到上面extractEvents中传入的是fiber节点，但我们的点击是发生在DOM上面啊，这是怎么做到的呢，这里就是点击发生的事件源对象与其对应的fiber节点之间的关系了：  
首先根据真实的**事件源对象**，找到e.target的真实DOM，然后根据DOM元素，找到其对应的fiber节点，然后进入legacy模式的事件处理系统，进行**批量更新**。  
知识点：**dom节点与其相应的fiber节点之间的关系是怎么样的？**  
1、react在初始化真实DOM的时候，用了一个随机key指针指向当前DOM节点对应的fiber节点  
2、fiber节点可以通过stateNode找到与之对应的DOM节点  

至此，我们拥有了事件源对象，并且事件源对象上已经挂在了函数执行队列了(**都是在extractEvents函数中完成的！**)。那现在就runEventsInBatch(events)，传入这个完备的事件源对象，执行我们真正的事件函数吧！ 
在这个函数里面。他会把刚刚产生的事件执行队列中的函数，逐个取出，依次执行，并且有这么一个逻辑  
```
if (Array.isArray(dispatchListeners)) {
  for (let i = 0; i < dispatchListeners.length; i++) {
    if (event.isPropagationStopped) {
      break;
    }
    dispatchListeners[i](event); // 这一步就是在执行单个的事件处理函数
  }
}
```  
从上面的代码中可以看到，如果我们在一个事件处理函数中写下return false之类的代码，是并不能阻止浏览器的默认事件的，如果想要阻止，必须是e.preventDefault,或者是更具体的，e.stopPropagation,这样，react底层会把isPropagationStopped置为true，从而阻止冒泡。  

注意这里还有一个事件池的概念。看下面的代码  
```
handleClick = (e) => {
   console.log(e.target); // 某个DOM
   setTimeout(() => {
      console.log(e.target); // null
   }, 0)
}
```  
在第一个console的时候，当然是能够获取到事件源的，但是当handleClick执行完毕之后，也就是进入到setTimeout中，这个时候事件源对象已经被释放到了事件池中，这样做的好处是我们可以不用再次创建这个事件源对象，而是在需要的时候对其进行复用。(当然如果在setTimeout的回调中传入了e，这个时候因为闭包的存在，里面的console是可以访问到这个事件源的，但是这样也会导致事件源对象不能被及时放回事件池中)  

现在把react事件系统串一下：  
首先是进行初始化，这个时候会有一些合成事件---事件插件，与合成事件---原生事件之间的映射关系
然后，在JSX中，我们为一个DOM元素绑定了事件，其实并没有真正绑定在这个元素上面，那么到底发生了什么呢？在react的reconcile过程中，如果发现这是一个dom元素类型的fiber，会进行一些操作，这些操作就是注册事件监听。比如在一个button元素上添加了onClick事件，那么就可以根据前面初始化步骤中形成的两个映射关系，拿到onClick合成事件对应的原生事件的依赖。然后统一处理函数dispatchEvent就上场了，首先会对这个函数.bind一下，主要是为了注入container、原生事件、冒泡之类的信息，然后就是将dispatchEvent绑定在document上面，这个时候，document上面就监听了我们的合成事件对应的那些原生事件。但是这里的处理函数是dispatchEvent，不是我们自己写的handle。 
到了触发阶段，那自然就会触发前面的dispatchEvent。在这个dispatchEvent里面就会进行所谓的批量更新。批量更新在我的理解力就是在一次事件循环之内将所要做的修改全部修改完毕，那他怎么控制是在一次事件循环内呢？有一个变量叫isBatchEventUpdates,为true是表示批量更新正在开启中。更近一步地，dispatchEvent中做了些什么事情呢？最重要的就是plugin.extractEvents，在这个方法中，他会根据传入的原生事件从事件池中复用/产生新的事件源对象，并且维护一个事件处理队列。更近一步地，我们可以根据e.target拿到触发事件的DOM节点，然后可以根据这个DOM节点找到最近的DOM元素对应的fiber节点，然后从下至上遍历fiber节点，查找fiber节点上绑定的真实的处理函数，并且根据他是冒泡还是捕获，使用push/unshift的方式将真实的事件处理函数添加到事件处理队列中。待fiber节点遍历完毕之后，会将事件处理队列保存给事件源对象上，等待执行。  
最后就是取出这个事件处理队列，逐个执行其中的事件处理函数。需要注意的是，react在dispatchEvent内部自己封了stopPropogation和preventDefault，在遍历执行事件处理函数的时候，会执行event.isPropagationStopped来查询是否阻止冒泡，所以只是单纯在事件处理函数中return false是不能阻止冒泡和默认行为的，事件处理函数还是会执行，只有调用了e.preventDefault或者e.stopPropagation才能真正改变react中内置的阻止冒泡和默认行为的变量。  


#### React17中所进行的更改
1、取消了事件池，这样就不会出现上面setTimeout中获取不到事件源的情况  
2、事件不再统一绑定到document上面，而是绑定在React.render(app, container)的container上面，这样当存在有多个应用的时候，是比较方便的，因为你是绑定在单一的应用上的  
3、react17中对原生的捕获事件进行了支持，并且onScroll事件不再进行冒泡，onFocus 和 onBlur 使用原生 focusin， focusout 合成。  

### React Hooks原理  
function组件是一种无状态组件，他不像class组件，有state可以去记录组件的状态，有生命周期，所以就有了hooks被应用在function组件中，用来解决上面这些问题。  

首先用一个例子来区分一下class组件和function组件之间的区别  
```
class Index extends React.Component {
  constructor () {
    // ....
    this.state = {
      num: 0
    }
  }
  
  handleClick = () => {
    for (let i = 0; i < 5; i++) {
      setTimeout(() => {
        this.setState({ num: this.state.num + 1; })
        console.log(this.state.num); // 1 2 3 4 5
      }, 0)
    }
  }
  
  render () {
    return (
      <div onClick={handleClick} />
    )
  }
}

function Index () {
  const [num, setNum] = useState(0);
  
  const handleClick = () => {
    for (let i = 0; i < 5; i++) {
      setTimeout(() => {
        setNum(num + 1);
        console.log(num); // 0 0 0 0 0
      }, 0)
    }
  }
  
  return (
    <div onClick={handleClick} />
  )
}
```  
上面两个例子，在class组件中的输出是1 2 3 4 5，因为他的输出是写在setTimeout里面的，打破了react批量更新的原则，他的更新会放在下一次事件循环进行，而这个时候批量更新已经结束，所以每次setState之后，都能获取到最新的state值。但是在function组件中，由于它没有一个class实例可以去保存这些状态，所以每一次去setNum其实都是在重新执行一次函数组件，在重新走到useState的时候，react内部其实是走了updateState的，是可以拿到最新的状态。然后接着往下，这个时候的handleClick是一个被重新创建的新函数，也就是说，handleClick指向了一块新的内存空间，但是在顺着for循环往下的时候，num所在的上下文应该是第一次初始化时的上下文，因为通过闭包的，把初始化时的值保存在了num的执行上下文里面(典型的闭包啊：虽然外层函数销毁了，但是内层函数还是能对外层函数的变量进行访问)，所以在第二个例子中打印出来的num都是0，即初始值。

useState 负责组件更新  
useRef 负责保存数据  
useEffect 负责执行副作用  
useMemo 负责缓存优化  

为什么hooks不可以写在条件语句中，react是怎么记录hooks的顺序的？  
在workInProgress fiber中，有一个memoizedState，在函数组件中，他用来存储对应fiber中的hooks链表，也就是说，这个fiber中包含的hooks，会根据hooks初始化的顺序，链接成一个链表，保存在memoizedState中。如果出现了条件语句，就可能存在更新前后fiber的memoizedState中保存的hooks链表不一致，这个时候如果需要更新状态，就可能会报错。**Attention:**在单个的hooks里面也有memoizedState这个变量，用来保存单个hooks的相关信息，不同种类的hooks，memoizedState的值也不同，这里需要注意与workInProgress fiber中的memoizedState进行区分。  

workInProgress fiber中还有updateQueue，用来保存useEffect链表，这是一个环形链表，workInProgress.updateQueue.lastEffect = lastEffect(fiber中最后一个声明的useEffect)，lastEffect.next = firstEffect。所以在react里面，有一个effect list，他是采用深度优先遍历的方法，在render阶段，找出所有那些有副作用的fiber，最后构建成一个只带有副作用的effect list链表。在commit阶段，遍历这个effect list，根据每一个effect节点的effect tag类型，执行effect，并且最终导致DOM树的更改。**关于effect list，存储的到底是effect还是fiber节点？还有effect tag是什么？**   

**总结一下**  
1、在初始化阶段，workInProgress fiber中的每一个hooks都会进行初始化，生成一个hook对象，并且按照他们的初始化顺序构建成一条链表，这条链表会被保存在workInProgress fiber的memoizedState中。  2、单个hooks内部也有memoizedState属性，里面保存了这个hooks的相关状态，不同类型的hooks保存的内容也不同。  
3、对于useEffect，他会在render阶段的深度遍历中，形成一条effect list(是首尾相连的)。在commit阶段，等dom树构建完成之后，会逐个遍历effect list中的元素并且执行，从而改变DOM。  

在useState中，如果前后两次传入的state相同，是不会触发组件渲染的。  
这是因为在useState和useReducer的底层，有一个dispatchAction，在这个函数里面，会通过lastRenderedReducer获取最新的state和上一次更新时的state，并且将这两个值进行**浅比较**，如果浅比较之后发现值没有变化，就不会触发更新。这是函数式组件与状态组件的不同之处。  
注意，这个地方因为是浅比较，所以如果state是一个对象obj，只要是这个引用类型obj的内存地址发生了变化，也是会触发组件重新渲染的，就算这个obj中的各个属性的值没有发生变化。  

**shouldComponentUpdate**  
有几个点需要注意：  
1、这个方法在组件初始化和forceUpdate的时候不会执行。  
2、这个钩子函数对props和state的比较是浅比较，也就是说如果state中有一个对象obj，子组件通过父组件的props使用了这个obj，如果只是obj中的某一个属性发生了变化，子组件是不会发生重新渲染的  

**pureComponent**
使用需要注意的几个点：  
1、子组件是pureComponent，父组件在调用子组件的时候，不要使用箭头函数
```
class Child extends React.PureComponent {...}
class Father extends React.PureComponent {
  render = () => {<Child />}
}
```  
因为父元素使用箭头函数的形式来调用子函数的时候，父元素每次渲染实际上都会生成一个新的子函数，那子元素虽然是PureComponent也失去了它的作用。  
2、子组件是PureComponent,子组件的父组件是函数组件，父组件在给子组件传递函数类型的props的时候，一定要用useCallback或者useMemo包一下。不然父组件每次渲染，里面的函数其实都是新函数，那传递到子组件下面的也都是新函数，也会导致PureComponent失效。

### React.lazy和Suspense  
Suspense是一种用同步的方法解决异步操作的方案，Suspense等待异步操作的结果，可以传入一个fallback参数，用来在异步操作结束之前展示，异步操作结束之后才展示正常的子组件。  
**原理**  
Suspense搭配React.lazy使用可以实现动态加载功能。使用方式如下：
```
const LazyComponent = React.lazy(() => import('./test'));

<Suspense fallback={<div>isLoading</div>}>
  <LazyComponent />
</Suspense>
```  
首先，React.lazy接受传入一个函数，函数中用import引入一个组件，这样整个React.lazy返回的是一个Promise。具体的流程是这样：
首先，用React.lazy包裹的组件，在React中都会被特殊处理。而他在首次被渲染的时候，会执行init函数，这个init函数里面做的事情就是把Promise的状态设置成pending，并且绑定then函数。（then函数里面是promise执行完成之后的操作，主要是将promise的状态变成resolved）。  
然后外层的Suspense相当是一个try...catch,他接收到了来自里面的promise，就会执行这个promise。执行完成之后就会触发刚刚绑定好的then函数。这个时候，Suspense会再触发一次渲染，再走到React.lazy里面，这个promise的状态已经被置成了resolved，所以就会把加载好的组件return出来。  
所以总结起来Suspense的工作流程就是：发起渲染---发现异步请求---渲染悬停----异步操作完毕---二次渲染  

### commonJS和ESModules  
1、commonJS可以直接运行在node中，但是如果想在浏览器中运行，需要webpack进行编译，而esModule在node和浏览器中都可以直接使用  
2、commonJS是动态加载，ESModule是静态加载，动态加载是指可以根据用户需要，根据实际的if条件决定要require哪些文件，而ESModule中对于模块的引用必须在文件的开头进行声明，因此ESModule可以在编译的时候利用tree shaking减少文件体积  
3、commonJS是在运行时执行，ESModule是在编译时执行  
3、commonJS返回的是对内容的拷贝，所以如果模块一旦运行完毕，已经生成了固定的结果，后续别的文件对这个模块中的内容进行更改，引用了这个模块的文件中引用的内容也不会发生变更。而ESModule返回的是对内容的引用，在每次使用的时候都会根据这个引用再去拿一下内容  

### npm run XXX之后发生了什么  
我们在npm run XXX的时候，实际上执行的是另外的一个指令，比如说npm run server实际上执行的是vue-cli-service serve。但是为什么直接执行vue-cli-service指令会报错，但是通过npm run xxx就不会呢？因为我们的操作系统中是没有vue-cli-service这个指令的，但是通过npm run 命令，实际上执行的是node_modules/.bin下面对应的软链接。那这个软链接是怎么形成的呢？就是我们在npm install的时候，我们用的那个工具的库源代码中package.json里面有bin字段，这个字段可以用来在安装时创建软链接，这样就可以在执行的命令和node_modules/bin之间形成一种映射。

### Rollup打包原理  
rollup使用了acorn和magic-string这两个库，前者用来将代码转成抽象语法树，后者是一个字符串操作的库。  
rollup的打包流程大致就是：每个文件都是一个模块，每个模块都会根据文件中的代码生成一棵抽象语法树AST，分析AST中的每一个节点，看看这个节点里面有没有调用什么函数或者引用什么变量，然后看这个函数/变量能否在当前节点的作用域中查找到，如果找不到就往上找一直找到模块的顶级作用域，如果还找不到就表示这是一个需要从外部引入的东西。  
rollup的打包从rollup()开始，这个方法会生成一个bundle实例，用来收集其他模块的代码，并且最终将各模块打包到一起。  
rollup()先从入口模块开始，调用new Module()，在这个过程中，会使用acorn将代码转成AST，在每个module中，都会分别维护一个import和export对象，将该模块引入/导出的相关信息存储到这两个对象中。  
然后分析该模块中的每一个AST节点，分析他们的作用域，找出这个AST节点定义的变量；然后是分析变量名，函数名，属性名一类的标识符号，rollup每分析一个这样的标识符，就会去遍历自己的作用域，看自己的作用域里面是否对这个标识符有定义，找不到就往上层找，到该模块顶级作用域也找不到的话就认为这个标识符是需要从外部引入的。然后就会把这个需要引入的标识符添加到module的_dependsOn里面。  
根据_dependsOn可以知道这个模块依赖外部的哪些模块，于是会以_dependsOn中的值为key,从上一步维护的import对象里面，根据这个key去找对应的需要引入的文件，然后读取这个文件生成一个新的Module实例。  
上面的步骤就可以真正引入那些我们需要使用的文件，而对于那些我们引入了却并未使用到的文件，并未作出任何处理。这就是tree-shaking的原理。  
在引入了所有需要的函数之后，就可以调用bundle的generate()来生成代码，在这个过程中，rollup还会做一些处理，比如移除额外代码：一个模块文件中export了若干个方法，而实际只有一个方法在A文件中被使用到，那么rollup会移除掉这些没有被使用到的其他方法，而把那个唯一被引用的方法和A打包到一起。比如解决重命名的问题。  
那么在generate()里面其实就是将AST节点对应的代码传进去，添加到magic-string实例中，最后将拼在一起的代码进行返回。  
以上就是rollup的打包和tree-shaking过程。  

### React中与写法有关的一些优化策略  
在React的业务中，现在基本有三种组件之间数据共享的方式，第一种是使用createContext和useContext，第二种是使用mobx进行数据管理，第三种是在顶层父元素里定义需要使用到的变量的方法，然后逐级向下传递。  
在第三种方式中，设想这样一个场景，父组件向子组件A传递变量num，向子组件B传递改变变量的方法updateNum，这是一个不会变化的函数，不变的函数是指useState这种产生的函数，因为hook是保存在fiber上的，每次都是取的fiber上同一个改变的方式，所以这个是一个不变的函数。且父组件自身也使用到了num，那么在B中每次修改num值，都会造成父组件的重新渲染。  
在这种场景下，从组件B的角度上来看，他接受了一个不变的函数，按道理来说应该不会每次都次重新渲染。但由于我们没有组件的渲染进行任何优化，所以每次父组件重新渲染，传到组件B的props每次都是一个新的props，因此每次都会造成组件B也重新渲染。解决方法就是：如果使用mobx管理数据的话，用observer包裹组件B(内部已经使用了React.memo)，或者直接用Reat.memo包裹组件B。  
但我们经常会有一种操作是将这个setState放在handleClick中，然后进行了一些其他操作，最后将这个handleClick传给子组件B，那么父组件的重新渲染是会导致B重新渲染的，就算B包裹了memo或者observer都没有用，因为父组件每次重新渲染都的确会产生一个新的handleClick，对B来说这是一个新的props。所以推测出memo和observer应该是一种深比较，对于同样物理地址的方法会把它们看成相等，而不同地址的方法，就算他们写法完全一样也是不同的props。

### 疑惑  
为什么webpack需要打包才能用？他打包是为了什么？而vite直接通过劫持HTTP分析文件之间的关系，把文件整合之后返回给用户，webpack为什么不能也这样？所以打包的目的是什么？




### 开发中的小tips  
1、如果需要改变元素的位置，比如left,top，可以使用transform: translate(...),transform是由GPU直接控制渲染的，并不会造成页面重排。 
2、如果页面中同时有两个元素使用了同一个id,使用document.getElementById获取的是更外层的那个DOM节点

### 项目中遇到的坑集/难点  
1、在构建弹窗体系时，我将弹窗组件用comp这个属性保存在一个observable的对象中，然后在渲染的时候利用这个对象的comp属性来获取对应的组件，再渲染到页面上。这个时候渲染会报错，大概就是修改observable的值必须用action来包裹，但其实我是都包裹了的。解决方法就是保存弹窗的对象不用observable的值就好了，就直接用一个静态的对象，key-value分别是弹窗名和弹窗内容，要用的时候去取就ok  

2、还是在弹窗体系构建时候发生的问题，我在constructor里面去init了一个observable的对象，但是init的时候并不是直接重写这个对象(也就是让这个对象指向一个新的内存地址)，而是改写了对象中属性的值，这样会导致这个对象的变更最终无法被深度监听到  


### 疑惑  
1、按道理父组件每次渲染，子组件在没有任何优化手段的情况下，也会触发子组件的重新渲染，可是事实上并没有这样，这是为什么？


















