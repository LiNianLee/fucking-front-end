# fucking-front-end
一些自己找到的好的知识点学习链接

# javascript

### 原型与原型链
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










