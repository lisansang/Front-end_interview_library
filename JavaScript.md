# JavaScript 部分

## JavaScript基础

### 1. 介绍下JavaScript 数据类型

JavaScript 共有八种数据类型，分别是 Undefined、Null、Boolean、Number、String、Object、Symbol、BigInt。
其中 Symbol 和 BigInt 是 ES6 中新增的数据类型：

- Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
- BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了Number 能够表示的安全整数范围。
这些数据可以分为原始数据类型和引用数据类型：
- 栈：原始数据类型（Undefined、Null、Boolean、Number、String）
- 堆：引用数据类型（对象、数组和函数）
两种类型的区别在于存储位置的不同：
- 原始数据类型直接存储在栈（stack）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
- 引用数据类型存储在堆（heap）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：
- 在数据结构中，栈中数据的存取方式为先进后出。
- 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。
在操作系统中，内存被分为栈区和堆区：
- 栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
- 堆区内存一般由开发着分配释放，若开发者不释放，程序结束时可能由垃圾回收机制回收。

### 2. 数据类型检测的方式有哪些

-  typeof
其中数组、对象、null 都会被判断为 object，其他判断都正确。 
-  instanceof
instanceof 可以正确判断对象的类型，其内部运行机制是判断在其原型链中能否找到该类型的原型。
可以看到，instanceof 只能正确判断引用数据类型，而不能判断基本数据类型。instanceof 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。 
-  constructor
constructor 有两个作用，一是判断数据的类型，二是对象实例通过constrcutor 对象访问它的构造函数。需要注意，如果创建一个对象来改变它的原型，constructor 就不能用来判断数据类型了。 
-  Object.prototype.toString.call()
Object.prototype.toString.call() 使用 Object 对象的原型方法 toString 来判断数据类型。
同样是检测对象 obj 调用 toString 方法，obj.toString()的结果和Object.prototype.toString.call(obj)的结果不一样，这是为什么？
这是因为 toString 是 Object 的原型方法，而 Array、function 等类型作为 Object 的实例，都重写了 toString 方法。不同的对象类型用 toString 方法时，根据原型链的知识，调用的是对应的重写之后的 toString 方法（function 类型返回内容为函数体的字符串，Array类型返回元素组成的字符串），而不会去调用 Object 原型上的 toString 方法（返回对象的具体类型），所以采用 obj.toString()不能得到其对象类型，只能将 obj 转换为字符串类型。因此，在想要得到对象的具体类型时，应该调用 Object 原型上的 toString 方法。 

### 3.  null 和 undefined 区别

首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。
undefined 代表的含义是未定义，null 代表的含义是空对象。一般变量声明了但还没有定义的时候会返回 undefined，null 主要用于赋值给一些可能会返回对象的变量，作为初始化。
undefined 在 JavaScript 中不是一个保留字，这意味着可以使用undefined 来作为一个变量名，但是这样的做法是非常危险的，它会影响对 undefined 值的判断。我们可以通过一些方法获得安全的undefined 值，比如说 void 0。
当对这两种类型使用 typeof 进行判断时，Null 类型化会返回“object”，这是一个历史遗留的问题。当使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

### 4.  intanceof 操作符的实现原理及实现

instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。

```javascript
function myInstanceof(left, right) {
  // 获取对象的原型
  let proto = Object.getPrototypeOf(left)
  // 获取构造函数的 prototype 对象
  let prototype = right.prototype; 

  while (true) {
    if (!proto) return false;
    if (proto === prototype) return true;
    // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
    proto = Object.getPrototypeOf(proto);
  }
}
```

原理：
instanceof 是一个运算符，它可以用来判断某一个对象的类型，具体原理就是利用了原型和原型链（判断构造函数的prototype属性是否出现在对象的原型链中）。
instanceof =>通过 原型和原型链 判断一个实例对象与一个构造函数之间的关系的.
instanceof 左侧是一个实例对象，右侧是一个构造函数。

更简单的说法：
我们拿到 instanceof 左侧对象的原型链
再拿到 instanceof 右侧构造函数的显式原型 prototype
如果原型链中存在显式原型 prototype，instanceof 返回 true，否则返回 false

### 5. 如何获取安全的 undefined 值？

因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。表达式 void ___ 没有返回值，因此返回结果是 undefined。void 并不改变表达式的结果，只是让表达式不返回值。因此可以用 void 0 来获得 undefined。

### 6. Object.is() 与比较操作符 “=”、“” 的区别？

使用双等号（）进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。使用三等号（=）进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。
使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN是相等的。

### 7. 什么是 JavaScript 中的包装类型？

在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，如：
在访问 'abc'.length 时 ，JavaScript 将 'abc' 在 后 台 转 换 成String('abc')，然后再访问其 length 属性。
JavaScript 也可以使用 Object 函数显式地将基本类型转换为包装类型；也可以使用 valueOf 方法将包装类型倒转成基本类型。

### 8. 为什么会有 BigInt 的提案？

JavaScript 中 Number.MAX_SAFE_INTEGER 表示最⼤安全数字，计算结果是 9007199254740991，即在这个数范围内不会出现精度丢失（⼩数除外）。但是⼀旦超过这个范围，js 就会出现计算不准确的情况，这在⼤数计算的时候不得不依靠⼀些第三⽅库进⾏解决，因此官⽅提出了 BigInt 来解决此问题。

### 9. 如何判断一个对象是空对象

使用 JSON 自带的.stringify 方法来判断：

```javascript
var a={};
var b=new Object();
console.log(JSON.stringify(a)=="{}") //true
console.log(JSON.stringify(b)=="{}") //true
```

使用 ES6 新增的方法 Object.keys()来判断

```javascript
var data = {};
var arr = Object.keys(data);
console.log(arr.length == 0);//true
```

### 10.  const 对象的属性可以修改吗

const 保证的并不是变量的值不能改动，而是变量指向的那个内存地址不能改动。对于基本类型的数据（数值、字符串、布尔值），其值就保存在变量指向的那个内存地址，因此等同于常量。
但对于引用类型的数据（主要是对象和数组）来说，变量指向数据的内存地址，保存的只是一个指针，const 只能保证这个指针是固定不变的，至于它指向的数据结构是不是可变的，就完全不能控制了。

### 11.  如果 new 一个箭头函数的会怎么样

箭头函数是 ES6 中的提出来的，它没有 prototype，也没有自己的 this指向，更不可以使用 arguments 参数，所以不能 New 一个箭头函数。
new 操作符的实现步骤如下：
(1) 创建一个对象
(2) 将构造函数的作用域赋给新对象（也就是将对象的proto属性指向构造函数的 prototype 属性）
(3) 指向构造函数中的代码，构造函数中的 this 指向该对象（也就是为这个对象添加属性和方法）
(4) 返回新的对象
所以，上面的第二、三步，箭头函数都是没有办法执行的。

### 12. 箭头函数的 this 指向哪⾥？

箭头函数不同于传统 JavaScript 中的函数，箭头函数并没有属于⾃⼰的 this，它所谓的 this 是捕获其所在上下⽂的 this 值，作为⾃
⼰的 this 值，并且由于没有属于⾃⼰的 this，所以是不会被 new
调⽤的，这个所谓的 this 也不会被改变。
可以⽤Babel 理解⼀下箭头函数:

```javascript
// ES6 
const obj = { 
  getArrow() { 
    return () => { 
      console.log(this === obj); 
    }; 
  } 
}
```

转化后：

```javascript
// ES5，由 Babel 转译
var obj = { 
  getArrow: function getArrow() { 
    var _this = this; 
    return function () { 
        console.log(_this === obj); 
    }; 
  } 
};
```

### 13. 扩展运算符的作用及使用场景

1. 对象的扩展运算符(…)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中。

```javascript
let bar = { a: 1, b: 2 };
let baz = { ...bar }; // { a: 1, b: 2 }
```

上述方法实际上等价于:

```javascript
let bar = { a: 1, b: 2 };
let baz = Object.assign({}, bar); // { a: 1, b: 2 }
```

Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。(如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性)。

同样，如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。

```javascript
let bar = {a: 1, b: 2};
let baz = {...bar, ...{a:2, b: 4}};  // {a: 2, b: 4}
```

利用上述特性就可以很方便的修改对象的部分属性。在redux中的reducer函数规定必须是一个纯函数，reducer中的state对象要求不能直接修改，可以通过扩展运算符把修改路径的对象都复制一遍，然后产生一个新的对象返回。

需要注意：扩展运算符对对象实例的拷贝属于浅拷贝。

2. 数组扩展运算符

- 数组的扩展运算符可以将一个数组转为用逗号分隔的参数序列，且每次只能展开一层数组。

```javascript
console.log(...[1, 2, 3])
// 1 2 3
console.log(...[1, [2, 3, 4], 5])
// 1 [2, 3, 4] 5
```

下面是数组的扩展运算符的应用：

将数组转换为参数序列

```javascript
function add(x, y) {
  return x + y;
}
const numbers = [1, 2];
add(...numbers) // 3
```

- 复制数组

```javascript
const arr1 = [1, 2];
const arr2 = [...arr1];
```

要记住：扩展运算符(…)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中，这里参数对象是个数组，数组里面的所有对象都是基础数据类型，将所有基础数据类型重新拷贝到新的数组中。

- 合并数组
如果想在数组内合并数组，可以这样：

```javascript
const arr1 = ['two', 'three'];const arr2 = ['one', ...arr1, 'four', 'five'];// ["one", "two", "three", "four", "five"]
```

- 扩展运算符与解构赋值结合起来，用于生成数组

```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];first // 1rest  // [2, 3, 4, 5]
```

需要注意：如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

```javascript
const [...rest, last] = [1, 2, 3, 4, 5];         // 报错const [first, ...rest, last] = [1, 2, 3, 4, 5];  // 报错
```

- 将字符串转为真正的数组

```javascript
[...'hello']    // [ "h", "e", "l", "l", "o" ]
```

- 任何 Iterator 接口的对象，都可以用扩展运算符转为真正的数组
比较常见的应用是可以将某些数据结构转为数组：

```javascript
// arguments对象
function foo() {
  const args = [...arguments];
}
```

用于替换es5中的Array.prototype.slice.call(arguments)写法。

- 使用Math函数获取数组中特定的值

```javascript
const numbers = [9, 4, 7, 1];
Math.min(...numbers); // 1
Math.max(...numbers); // 9
```
### 14. Proxy 可以实现什么功能？

在 Vue3.0 中通过 Proxy 来替换原本的 Object.defineProperty来实现数据响应式。
Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作。

```javascript
let p = new Proxy(target, handler)
```

代表需要添加代理的对象，handler 用来自定义对象中的操作，比如可以用来自定义 set 或者 get 函数。
下面来通过 Proxy 来实现一个数据响应式：

```javascript
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
      setBind(value, property)
      return Reflect.set(target, property, value)
    }
  }
  return new Proxy(obj, handler)
}
let obj = { a: 1 }
let p = onWatch(
  obj,
  (v, property) => {
    console.log(`监听到属性{property}改变为{v}`)
  },
  (target, property) => {
    console.log(`'{property}' ={target[property]}`)
  }
)
p.a = 2 // 监听到属性a改变
p.a // 'a' = 2
```

在上述代码中，通过自定义 set 和 get 函数的方式，在原本的逻辑中插入了我们的函数逻辑，实现了在对对象任何属性进行读写时发出通知。
当然这是简单版的响应式实现，如果需要实现一个 Vue 中的响应式，需要在 get 中收集依赖，在 set 派发更新，之所以 Vue3.0 要使用Proxy 替换原本的 API 原因在于 Proxy 无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到，但是 Proxy 可以完美监听到任何方式的数据改变，唯一缺陷就是浏览器的兼容性不好。

### 15.  new操作符的原理

- 在内存中创建一个新对象
- 这个新对象内部的_proto_指针被赋值给构造函数的prototype属性
- 构造函数内部的this指向新对象
- 执行构造函数内部的代码
- 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象
实现：

```javascript
function myNew() {
  let Constructor = Array.prototype.shift.call(arguments) //1、通过参数shift方法取到Constructor
  let obj = {} ///2、在内存中定义一个新对象
  obj._proto_ = Constructor.prototype // 3、新对象的_proto_指针指向构造函数的prototype属性
  let r = Constructor.apply(obj,arguments); // 4、this指向新对象，并执行构造函数代码
}
let ada = myNew(People, 'ada')
console.log(ada)
```

### 16. JSON 是什么

JSON 是一种基于文本的轻量级的数据交换格式。它可以被任何的编程语言读取和作为数据格式来传递。
在项目开发中，使用 JSON 作为前后端数据交换的方式。在前端通过将一个符合 JSON 格式的数据结构序列化为JSON 字符串，然后将它传递到后端，后端通过 JSON 格式的字符串解析后生成对应的数据结构，以此来实现前后端数据的一个传递。
因为 JSON 的语法是基于 js 的，因此很容易将 JSON 和 js 中的对象弄混，但是应该注意的是 JSON 和 js 中的对象不是一回事，JSON中对象格式更加严格，比如说在 JSON 中属性值不能为函数，不能出现 NaN 这样的属性值等，因此大多数的 js 对象是不符合 JSON 对象的格式的。

在 js 中提供了两个函数来实现 js 数据结构和 JSON 格式的转换处理：

JSON.stringify() 函数，通过传入一个符合 JSON 格式的数据结构，将其转换为一个 JSON 字符串。如果传入的数据结构不符合 JSON 格式，那么在序列化的时候会对这些值进行对应的特殊处理，使其符合规范。在前端向后端发送数据时，可以调用这个函数将数据对象转化为 JSON 格式的字符串。

JSON.parse() 函数，这个函数用来将 JSON 格式的字符串转换为一个 js 数据结构，如果传入的字符串不是标准的 JSON 格式的字符串的话，将会抛出错误。当从后端接收到 JSON 格式的字符串时，可以通过这个方法来将其解析为一个 js 数据结构，以此来进行数据的访问。

### 17. JavaScript 脚本延迟加载的方式有哪些？

延迟加载就是等页面加载完成之后再加载 JavaScript 文件。 js 延迟加载有助于提高页面加载速度。
一般有以下几种方式：
**defer 属性**：给 js 脚本添加 defer 属性，这个属性会让脚本的加载与文档的解析同步解析，然后在文档解析完成后再执行这个脚本文件，这样的话就能使页面的渲染不被阻塞。多个设置了 defer 属性的脚本按规范来说最后是顺序执行的，但是在一些浏览器中可能不是这样。
**async 属性**：给 js 脚本添加 async 属性，这个属性会使脚本异步加载，不会阻塞页面的解析过程，但是当脚本加载完成后立即执行 js脚本，这个时候如果文档没有解析完成的话同样会阻塞。多个 async属性的脚本的执行顺序是不可预测的，一般不会按照代码的顺序依次执行。
**动态创建 DOM 方式**：动态创建 DOM 标签的方式，可以对文档的加载事件进行监听，当文档加载完成后再动态的创建 script 标签来引入js 脚本。使用 **setTimeout 延迟方法**：设置一个定时器来延迟加载 js 脚本文件
**将js脚本放在文档的底部**：将js脚本放在文档的底部，来使js脚本尽可能的在最后来加载执行。

### 18. 什么是 DOM 和 BOM？

DOM 指的是文档对象模型，它指的是把文档当做一个对象，这个对象主要定义了处理网页内容的方法和接口。

BOM 指的是浏览器对象模型，它指的是把浏览器当做一个对象来对待，这个对象主要定义了与浏览器进行交互的法和接口。
BOM 的核心是window，而 window 对象具有双重角色，它既是通过 js 访问浏览器窗口的一个接口，又是一个 Global（全局）对象。这意味着在网页中定义的任何对象，变量和函数，都作为全局对象的一个属性或者方法存在。window 对象含有 location 对象、navigator 对象、screen对象等子对象，并且 DOM 的最根本的对象 document 对象也是 BOM的 window 对象的子对象。

### 19.  escape、encodeURI、encodeURIComponent 的区别

encodeURI 是对整个 URI 进行转义，将 URI 中的非法字符转换为合法字符，所以对于一些在 URI 中有特殊意义的字符不会进行转义。
encodeURIComponent 是对 URI 的组成部分进行转义，所以一些特殊字符也会得到转义。
escape 和 encodeURI 的作用相同，不过它们对于 unicode 编码为0xff 之外字符的时候会有区别，escape 是直接在字符的 unicode编码前加上 %u，而encodeURI 首先会将字符转换为 UTF-8 的格式，再在每个字节前加上 %。

### 20. for...in 和 for...of 的区别

for…of 是 ES6 新增的遍历方式，允许遍历一个含有 iterator 接口的数据结构（数组、对象等）并且返回各项的值，和 ES3 中的 for…in 的区别如下:

- for…of 遍历获取的是对象的键值，for…in 获取的是对象的键名；
- for… in 会遍历对象的整个原型链，性能非常差不推荐使用，而for … of 只遍历当前对象不会遍历原型链；
- 对于数组的遍历，for…in 会返回数组中所有可枚举的属性(包括原型链上可枚举的属性)，for…of 只返回数组的下标对应的属性值；
总结：for...in 循环主要是为了遍历对象而生，不适用于遍历数组；for...of 循环可以用来遍历数组、类数组对象，字符串、Set、Map 以及 Generator 对象。

## JavaScript进阶
### 21. 对原型、原型链的理解

在 JavaScript 中是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性，它的属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。
当使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。
一般来说不应该能够获取到这个值的，但是现在浏览器中都实现了**proto**属性来访问这个属性，但是最好不要使用这个属性，因为它不是规范中规定的。ES5中新增的Object.getPrototypeOf() 方法，可以用来获取对象的原型。
当访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就是新建的对象为什么能够使用 toString() 等方法的原因。

特点：JavaScript 创建的每个新对象实体中并没有一份属于自己的原型副本，而是指向原型对象的指针。所以当修改原型时，就是修改了与之相关的原型对象。

### 22. 原型链的终点是什么？如何打印出原型链的终点？

由于 Object 是构造函数，原型链终点 Object.prototype.proto，而 `Object.prototype.proto=== null // true`，所以，原型链的终点是 null。
原型链上的所有原型都是对象，所有的对象最终都是由 Object 构造的，而 Object.prototype 的下一级是Object.prototype.proto。

### 23. 对作用域、作用域链的理解
**作用域**

1.  全局作用域和函数作用域
全局作用域
最外层函数和最外层函数外面定义的变量拥有全局作用域所有未定义直接赋值的变量自动声明为全局作用域所有 window 对象的属性拥有全局作用域全局作用域有很大的弊端，过多的全局作用域变量会污染全局命名空间，容易引起命名冲突。
函数作用域
函数作用域声明在函数内部的变量，一般只有固定的代码片段可以访问到.
作用域是分层的，内层作用域可以访问外层作用域，反之不行。 
2.  块级作用域 

使用 ES6 中新增的 let 和 const 指令可以声明块级作用域，块级作用域可以在函数中创建也可以在一个代码块中的创建（由{ }包裹的代码片段）。
let 和 const 声明的变量不会有变量提升，也不可以重复声明在循环中比较适合绑定块级作用域，这样就可以把声明的计数器变量限制在循环内部。

**作用域链**
在当前作用域中查找所需变量，但是该作用域没有这个变量，那这个变量就是自由变量。如果在自己作用域找不到该变量就去父级作用域查找，依次向上级作用域查找，直到访问到 window 对象就被终止，这一层层的关系就是作用域链。
作用域链的作用是保证对执行环境有权访问的所有变量和函数的有序访问，通过作用域链，可以访问到外层环境的变量和函数。
作用域链的本质上是一个指向变量对象的指针列表。变量对象是一个包含了执行环境中所有变量和函数的对象。作用域链的前端始终都是当前执行上下文的变量对象。全局执行上下文的变量对象（也就是全局对象）始终是作用域链的最后一个对象。当查找一个变量时，如果当前执行环境中没有找到，可以沿着作用域
链向后查找。

### 24. 对 this 对象的理解

this 是执行上下文中的一个属性，它指向最后一次调用这个方法的对象。在实际开发中，this 的指向可以通过四种调用模式来判断。
第一种是函数调用模式，当一个函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象。
第二种是方法调用模式，如果一个函数作为一个对象的方法来调用时，this 指向这个对象。
第三种是构造器调用模式，如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
第四种是 apply 、 call 和 bind 调用模式，这三个方法都可以显示的指定调用函数的 this 指向。其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。这个函数的 this 指向除了使用 new 时会被改变，其他情况下都不会改变。
这四种方式，使用构造器调用模式的优先级最高，然后是 apply、call和 bind 调用模式，然后是方法调用模式，然后是函数调用模式。

### 25.  call() 和 apply() 的区别？
它们的作用一模一样，区别仅在于传入参数的形式的不同。
apply 接受两个参数，第一个参数指定了函数体内 this 对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组，也可以为类数组，apply 方法把这个集合中的元素作为参数传递给被调用的函数。
call 传入的参数数量不固定，跟 apply 相同的是，第一个参数也是代表函数体内的 this 指向，从第二个参数开始往后，每个参数被依次传入函数。

### 26.  什么是尾调用，使用尾调用有什么好处？
尾调用指的是函数的最后一步调用另一个函数。代码执行是基于执行栈的，所以当在一个函数里调用另一个函数时，会保留当前的执行上下文，然后再新建另外一个执行上下文加入栈中。使用尾调用的话，因为已经是函数的最后一步，所以这时可以不必再保留当前的执行上下文，从而节省了内存，这就是尾调用优化。但是 ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

### 27.  ES6 模块与 CommonJS 模块有什么异同？
ES6 Module 和 CommonJS 模块的区别：
CommonJS 是对模块的浅拷⻉，ES6 Module 是对模块的引⽤，即 ES6 Module 只存只读，不能改变其值，也就是指针指向不能变，类似 const；import 的接⼝是 read-only（只读状态），不能修改其变量值。 即不能修改其变量的指针指向，但可以改变变量内部指针指向，可以对commonJS 对重新赋值（改变指针指向），但是对 ES6 Module 赋值会编译报错。

ES6 Module 和 CommonJS 模块的共同点：
CommonJS 和 ES6 Module 都可以对引⼊的对象进⾏赋值，即对对象内部属性的值进⾏改变。

### 28. 对 AJAX 的理解，实现一个 AJAX 请求
AJAX 是 Asynchronous JavaScript and XML 的缩写，指的是通过JavaScript 的 异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。

创建 AJAX 请求的步骤：
创建一个 XMLHttpRequest 对象。
在这个对象上使用 open 方法创建一个 HTTP 请求，open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。
在发起请求前，可以为这个对象添加一些信息和监听函数。比如说可以通过 setRequestHeader 方法来为请求添加头信息。还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发 onreadystatechange 事件，可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState变为 4 的时候，代表服务器返回的数据接收完成，这个时候可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。
这个时候就可以通过 response 中的数据来对页面进行更新了。
当对象的属性和监听函数设置完成后，最后调用 sent 方法来向服务器发起请求，可以传入参数作为发送的数据体。

使用 Promise 封装 AJAX：

```javascript
// Promise封装Ajax请求
function ajax(method, url, data) {
    var xhr = new XMLHttpRequest();
    return new Promise(function (resolve, reject) {
        xhr.onreadystatechange = function () {
            if (xhr.readyState !== 4) return;
            if (xhr.status === 200) {
                resolve(xhr.responseText);
            } else {
                reject(xhr.statusText);
            }

        };
        xhr.open(method, url);
        xhr.send(data);
    });
}
```

### 29. ajax、axios、fetch 的区别

1.  AJAX
Ajax 即“AsynchronousJavascriptAndXML”（异步 JavaScript 和XML），是指一种创建交互式网页应用的网页开发技术。它是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。其缺点如下：
基于原生 XHR 开发，XHR 本身的架构不清晰。
不符合关注分离（Separation of Concerns）的原则。
配置和调用方式非常混乱，而且基于事件的异步模型不友好。 

2.  Fetch
fetch 号称是 AJAX 的替代品，是在 ES6 出现的，使用了 ES6 中的promise 对象。Fetch 是基于 promise 设计的。Fetch 的代码结构比起 Ajax 简单多。fetch 不是 Ajax 的进一步封装，而是原生 js，没有使用 XMLHttpRequest 对象。
fetch 的优点：
语法简洁，更加语义化。
基于标准 Promise 实现，支持 async/await。
更加底层，提供的 API 丰富（request, response）。
脱离了 XHR，是 ES 规范里新的实现方式。 

fetch 的缺点：
fetch 只对网络请求报错，对 400，500 都当做成功的请求，服务器返回 400，500 错误码时并不会 。reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
fetch 默 认 不 会 带 cookie ， 需 要 添 加 配 置 项 ： `fetch(url,{credentials:'include'})`。
fetch 不 支 持 abort，不支持超时控制，使用 setTimeout 及Promise.reject 的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费。
fetch 没有办法原生监测请求的进度，而 XHR 可以。

3. Axios
Axios 是一种基于 Promise 对原生XHR进行封装的 HTTP 客户端，其特点如下：
浏览器端发起 XMLHttpRequests 请求。
node 端可发起 http 请求。
支持 Promise API监听请求和返回对请求和返回进行转化。
支持取消请求。
自动转换 json 数据。
客户端支持抵御 XSRF 攻击。

### 30.  异步编程的实现方式？
JavaScript 中的异步机制可以分为以下几种：
回调函数 的方式，使用回调函数的方式有一个缺点是，多个回调函数嵌套的时候会造成回调函数地狱，上下两层的回调函数间的代码耦合度太高，不利于代码的可维护。
Promise 的方式，使用 Promise 的方式可以将嵌套的回调函数作为链式调用。但是使用这种方法，有时造成多个 then 的链式调用，可能会造成代码的语义不够明确。
generator 的方式，它可以在函数的执行过程中，将函数的执行权转移出去，在函数外部还可以将执行权转移回来。当遇到异步函数执行的时候，将函数执行权转移出去，当异步函数执行完毕时再将执行权给转移回来。因此在 generator 内部对于异步操作的方式，可以用同步的顺序来书写。使用这种方式需要考虑的问题是何时将函数的控制权转移回来，因此需要有一个自动执行 generator 的机制，比如 co 模块等方式来实现 generator 的自动执行。
async 函数 的方式，async 函数是 generator 和 promise 实现的一个自动执行的语法糖，它内部自带执行器，当函数内部执行到一个await 语句的时候，如果语句返回一个 promise 对象，那么函数将会等待 promise 对象的状态变为 resolve 后再继续向下执行。因此可以将异步逻辑，转化为同步的顺序来书写，并且这个函数可以自动执行。

### 31.  对 Promise 的理解
Promise 是异步编程的一种解决方案，它是一个对象，可以获取异步操作的消息，他的出现大大改善了异步编程的困境，避免了地狱回调，它比传统的解决方案回调函数和事件更合理和更强大。
所谓 Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

1. Promise 的实例有三个状态:
Pending（进行中）
Resolved（已完成）
Rejected（已拒绝）
当把一件事情交给 promise 时，它的状态就是 Pending，任务完成了状态就变成了 Resolved、没有完成失败了就变成了 Rejected。
2. Promise 的实例有两个过程：
pending -> fulfilled : Resolved（已完成）
pending -> rejected：Rejected（已拒绝）
注意：一旦从进行状态变成为其他状态就永远不能更改状态了。

Promise 的特点：
对象的状态不受外界影响。promise 对象代表一个异步操作，有三种状态，pending（进行中）、fulfilled（已成功）、rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何他操作都无法改变这个状态，这也是 promise 这个名字的由来——“承诺”；
一旦状态改变就不会再变，任何时候都可以得到这个结果。promise对象的状态改变，只有两种可能：从 pending 变为 fulfilled，从pending 变为 rejected。这时就称为 resolved（已定型）。如果改变已经发生了，你再对 promise 对象添加回调函数，也会立即得到这个结果。这与事件（event）完全不同，事件的特点是：如果你错过了它，再去监听是得不到结果的。

Promise 的缺点：
无法取消 Promise，一旦新建它就会立即执行，无法中途取消。如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

总结：
Promise 对象是异步编程的一种解决方案，最早由社区提出。Promise是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个Promise 实例有三种状态，分别是pending、resolved和rejected，分别代表了进行中、已成功和已失败。实例的状态只能由pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。
状态的改变是通过 resolve() 和 reject() 函数来实现的，可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

注意：在构造 Promise 的时候，构造函数内部的代码是立即执行的。

### 32. Promise 解决了什么问题
主要解决了回调地狱问题
什么是回调地狱呢？异步的嵌套可称之为回调地狱，示例：

```javascript
//queryData向后端请求数据
queryData('data1', function(ret) {
    console.log(ret);　　
    queryData('data2', function(ret) {
        console.log(ret);
        queryData('data3', function(ret) {
            console.log(ret);
        });
    });
});
```

然而回调地狱本身会产生很典型的两个问题就是**可读性问题**和**信任问题**。

1. 可读性问题：
没有promise之前可能会出现这个样子的代码：

```javascript
    queryData('data1', function(ret) {
        console.log(ret);　　
        queryData('data2', function(ret) {
            console.log(ret);
            queryData('data3', function(ret) {
                console.log(ret);
                queryData('data4', function(ret) {
                    console.log(ret);　　
                    queryData('data5', function(ret) {
                        console.log(ret);
                        //此处省略无数嵌套
                    });
                });
            });
        });
    });
```

但是使用promise之后，可读性就上升的一个档次：

```javascript
    queryData('data1')
        .then((ret) => {
            console.log(ret);
            return queryData('data2');
        })
        .then((ret) => {
            console.log(ret);
            return queryData('data3');
        })
        .then((ret) => {
            console.log(ret);
            return queryData('data4');
        })
```

2. 信任问题
为什么会产生信任问题？

很多时候，我们在开发中都需要依赖大量的三方库，在使用三方库的某些方法时，我们会给三方库传入回调函数，但这些三方库是否百分之百可靠？ 或者说，这些三方库是否能够按照我们的设想，在正确的时间正确地调用回调？ 这些问题我们不得而知。类似还会出现一些问题:
回调过早/过晚/没有回调
回调次数过多/过少
……

其产生信任问题的根源在于 - 依赖注入，也就是说，我们需要将回调函数传入给第三方库，由第三方库去决定回调函数的执行时机、如何执行，举例：

```javascript
ajax( "/rest/user/1", function response(res){ 
  if (res) { 
    userHandler(); 
  } else { 
    nouserHandler(); 
  } 
}); 
// 当前repsonse传入ajax这个库中，执行完全由它来决定
```

promise的出现很好地解决了这个问题，promise并没有取消控制反转，而是把反转出去的控制再次反转，将最终控制权放在自己手中。
promise与普通回调函数的区别在于，普通回调函数将回调完完全全交给三方，控制权在第三方，而在promise中，回调只负责通知决议, 而决议后的操作则放在then中,由promise精确控制。

```javascript
    Promise((rs, rj) => {
        ajax( "/rest/user/1", function response(res){ 
            if (res) { 
                rs(res); 
            } else { 
                rj();
            } 
        }); 
    })
    .then((res) => {
        userHandler(res); 
    },()=>{
        nouserHandler(); 
    });
    // userHandler()和nouserHandler()的调用由promise控制，回调函数仅仅进行了决议
```

总结：Promise的出现主要是为了防止回调地狱的出现，同时也就解决了由回调地狱衍生出的两个问题
1、可读性问题。2、信任问题

### 33. 对 async/await 的理解

async/await 其实是 Generator 的语法糖，它能实现的效果都能用then 链来实现，它是为优化 then 链而开发出来的。
从字面上来看，async 是“异步”的简写，await 则为等待，所以很好理解 async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。当然语法上强制规定 await 只能出现在 asnyc 函数中，先来看看 async 函数返回了什么：

```javascript
async function asyncFun(){
   return 'hello world';
}
let result = asyncFun(); 

console.log(result)
```

![](https://cdn.nlark.com/yuque/0/2023/png/2127527/1678070474612-2406816d-a170-473b-b17a-cee9dfebbd94.png#averageHue=%23fcf9f9&id=kYhLk&originHeight=150&originWidth=732&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
所以，async 函数返回的是一个 Promise 对象。async 函数（包含函数语句、函数表达式、Lambda 表达式）会返回一个 Promise 对象，如果在函数中 return 一个直接量，async 会把这个直接量通过Promise.resolve() 封装成 Promise 对象。
async 函数返回的是一个 Promise 对象，所以在最外层不能用await 获取其返回值的情况下，当然应该用原来的方式：then() 链来处理这个 Promise 对象，就像这样：

```javascript
async function asyncFun(){
   return 'hello world'
}
let result = asyncFun() 
console.log(result)
result.then(e=>{
    console.log(e)   // hello world
})
```

那如果 async 函数没有返回值，又该如何？很容易想到，它会返回`Promise.resolve(undefined)`。
联想一下 Promise 的特点——无等待，所以在没有 await 的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数一致。
注意：`Promise.resolve(x)` 可以看作是 `new Promise(resolve => resolve(x))` 的简写，可以用于快速封装字面量对象或其他对象，将其封装成 Promise 实例。

### 34. async/await 的优势
单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，Promise 通过 then 链来解决多层回调的问题，现在
又用 async/await 来进一步优化它）。
假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。仍然用 `setTimeout` 来模拟异步操作：
```javascript
/**
 * 传入参数 n，表示这个函数执行的时间
 * 执行的结果是 n + 100，这个值将用于下一步骤
 */
function takeTime(n) {
    return new Promise(resolve => {
        setTimeout(() => resolve(n + 100), n);
    });
}
function step1(n) {
    console.log(`step1 ${n}ms`);
    return takeTime(n);
}
function step2(n) {
    console.log(`step2 ${n}ms`);
    return takeTime(n);
}
function step3(n) {
    console.log(`step3 ${n}ms`);
    return takeTime(n);
}
```
现在用 Promise 方式来实现这三个步骤的处理：
```javascript
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}
doIt();

// VM189:7 step1 300ms
// VM189:11 step2 400ms
// VM189:15 step3 500ms
// VM193:8 result is 600
// VM193:9 doIt: 1227.702880859375 ms
```
如果用 async/await 来实现呢，会是这样：
```javascript
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const result = await step3(time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}
doIt();
```
结果和之前的 Promise 实现是一样的，但是这个代码看起来是不是清晰得多，几乎跟同步代码一样
**总结**
async/await 对比 Promise 的优势如下：

- 代码读起来更加同步，Promise 虽然摆脱了回调地狱，但是 then 的链式调⽤也会带来额外的阅读负担
- Promise 传递中间值⾮常麻烦，⽽async/await⼏乎是同步的写法，⾮常优雅
- 错误处理友好，async/await 可以⽤成熟的 try/catch，Promise 的错误捕获⾮常冗余
- 调试友好，Promise 的调试很差，由于没有代码块，你不能在⼀个返回表达式的箭头函数中设置断点，如果你在⼀个.then 代码块中使⽤调试器的步进(step-over)功能，调试器并不会进⼊后续的.then 代码块，因为调试器只能跟踪同步代码的每⼀步。

### 36.  对象创建的方式有哪些？

一般使用字面量的形式直接创建对象，但是这种创建方式对于创建大量相似对象的时候，会产生大量的重复代码。但 js 和一般的面向对象的语言不同，在 ES6 之前它没有类的概念。但是可以使用函数来进行模拟，从而产生出可复用的对象创建方式，常见的有以下几种：

1. 第一种是工厂模式，工厂模式的主要工作原理是用函数来封装创建对象的细节，从而通过调用函数来达到复用的目的。但是它有一个很大的问题就是创建出来的对象无法和某个类型联系起来，它只是简单的封装了复用代码，而没有建立起对象和类型间的关系。
2. 第二种是构造函数模式。js 中每一个函数都可以作为构造函数，只要一个函数是通过 new 来调用的，那么就可以把它称为构造函数。执行构造函数首先会创建一个对象，然后将对象的原型指向构造函数的 prototype 属性，然后将执行上下文中的 this 指向这个对象，最后再执行整个函数，如果返回值不是对象，则返回新建的对象。因为 this 的值指向了新建的对象，因此可以使用 this 给对象赋值。构造函数模式相对于工厂模式的优点是，所创建的对象和构造函数建立起了联系，因此可以通过原型来识别对象的类型。
但是构造函数存在一个缺点就是，造成了不必要的函数对象的创建，因为在 js 中函数也是一个对象，因此如果对象属性中如果包含函数的话，那么每次都会新建一个函数对象，浪费了不必要的内存空间，因为函数是所有的实例都可以通用的。
3. 三种模式是原型模式，因为每一个函数都有一个 prototype属性，这个属性是一个对象，它包含了通过构造函数创建的所有实例都能共享的属性和方法。因此可以使用原型对象来添加公用属性和方法，从而实现代码的复用。这种方式相对于构造函数模式来说，解决了函数对象的复用问题。但是这种模式也存在一些问题，一个是没有办法通过传入参数来初始化值，另一个是如果存在一个引用类型如Array 这样的值，那么所有的实例将共享一个对象，一个实例对引用类型值的改变会影响所有的实例。
4. 第四种模式是组合使用构造函数模式和原型模式，这是创建自定义类型的最常见方式。因为构造函数模式和原型模式分开使用都存在一些问题，因此可以组合使用这两种模式，通过构造函数来初始化对象的属性，通过原型对象来实现函数方法的复用。这种方法很好的解决了两种模式单独使用时的缺点，但是有一点不足的就是，因为使用了两种不同的模式，所以对于代码的封装性不够好。
5. 第五种模式是动态原型模式，这一种模式将原型方法赋值的创建过程移动到了构造函数的内部，通过对属性是否存在的判断，可以实现仅在第一次调用函数时对原型对象赋值一次的效果。这一种方式很好地对上面的混合模式进行了封装。
6. 第六种模式是寄生构造函数模式，这一种模式和工厂模式的实现基本相同，我对这个模式的理解是，它主要是基于一个已有的类型，在实例化时对实例化的对象进行扩展。这样既不用修改原来的构造函数，也达到了扩展对象的目的。它的一个缺点和工厂模式一样，无法实现对象的识别。

### 37. 对象继承的方式有哪些？

1. 第一种是以原型链的方式来实现继承，但是这种实现方式存在的缺点是，在包含有引用类型的数据时，会被所有的实例对象所共享，容易造成修改的混乱。还有就是在创建子类型的时候不能向超类型传递参数。
2. 第二种方式是使用借用构造函数的方式，这种方式是通过在子类型的函数中调用超类型的构造函数来实现的，这一种方法解决了不能向超类型传递参数的缺点，但是它存在的一个问题就是无法实现函数方法的复用，并且超类型原型定义的方法子类型也没有办法访问到。
3. 三种方式是组合继承，组合继承是将原型链和借用构造函数组合起来使用的一种方式。通过借用构造函数的方式来实现类型的属性的继承，通过将子类型的原型设置为超类型的实例来实现方法的继承。这种方式解决了上面的两种模式单独使用时的问题，但是由于我们是以超类型的实例来作为子类型的原型，所以调用了两次超类的构造函数，造成了子类型的原型中多了很多不必要的属性。
4. 第四种方式是原型式继承，原型式继承的主要思路就是基于已有的对象来创建新的对象，实现的原理是，向函数中传入一个对象，然后返回一个以这个对象为原型的对象。这种继承的思路主要不是为了实现创造一种新的类型，只是对某个对象实现一种简单继承，ES5中定义的 `Object.create()` 方法就是原型式继承的实现。缺点与原型链方式相同。
5. 第五种方式是寄生式继承，寄生式继承的思路是创建一个用于封装继承过程的函数，通过传入一个对象，然后复制一个对象的副本，然后对象进行扩展，最后返回这个对象。这个扩展的过程就可以理解是一种继承。这种继承的优点就是对一个简单对象实现继承，如果这个对象不是自定义类型时。缺点是没有办法实现函数的复用。
6. 第六种方式是寄生式组合继承，组合继承的缺点就是使用超类型的实例做为子类型的原型，导致添加了不必要的原型属性。寄生式组合继承的方式是使用超类型的原型的副本来作为子类型的原型，这样就避免了创建不必要的属性。

### 38. 哪些情况会导致内存泄漏

以下四种情况会造成内存的泄漏：

1. 意外的全局变量：由于使用未声明的变量，而意外的创建了一个全局变量，而使这个变量一直留在内存中无法被回收。
2. 被遗忘的计时器或回调函数：设置了`setInterval`定时器，而忘记取消它，如果循环函数有对外部变量的引用的话，那么这个变量会被一直留在内存中，而无法被回收。
3. 脱离 DOM 的引用：获取一个 DOM 元素的引用，而后面这个元素被删除，由于一直保留了对这个元素的引用，所以它也无法被回收。
4. 闭包：不合理的使用闭包，从而导致某些变量一直被留在内存当中。
