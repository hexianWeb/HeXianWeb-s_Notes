#  	JavaScript知识体系

#  	第一章 入门

> 作者：何贤

- 什么是知识体系

  高效学习三部曲：找准知识体系；刻意训练；及时反馈

  知识体系：结构化的知识范围

### 知识体系

- JS基础语法
- JS-Web-API
- 开发环境
- 运行环境



# 第二章 JS基础语法

变量类型和计算

- 题目

Q1：typeof能判断哪些类型

- 识别所有值类型
- 识别函数
- 判断是否为引用类型

Q2： === 与==

- 除了 == null之外 其余一律 === 

Q3：值类型和引用类型的区别

- 看下方详解

Q4：手写深拷贝

- 看下方详解

Q5：。。。。

- 知识点

### 值类型

#### undefined 字符串 数字 布尔 Symbol

> 占用内存小 可以存放在栈中

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-02_11-14.png)



### 引用类型

#### 对象 数组 null 函数

（特殊的引用类型 但一般用不着函数存储数据 没有拷贝、复制函数一说）

> 占用内存大 需要借助堆

````js
let a = { age: 20 };
let b = a;
b.age = 1;
//值类型 另存储 不改变引用类型
let x = a.age
x = 3
console.log(a.age); //1
````

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-02_11-13.png)



### 引用类型 **深拷贝**

```js
console.log("deep clone");

const obj = {
  age: 20,
  name: "xxx",
  address: {
    city: "beijing",
  },
  arr: ["a", "b"],
};

const obj1 = deepClone(obj);
obj1.address.city = "shanghai";
obj.age = 22;
console.log(obj);
console.log(obj1);	

/**
 * 深拷贝
 * @param {Object} obj  要拷贝的对象
 * @returns
 */
function deepClone(obj = {}) {
  // 判断为引用类型
  if (typeof obj !== "object" || obj == null) {
    // 非引用类型
    console.log(obj);
    return obj;
  }

  // 初始化返回结果
  let result;
  // 判断数组或对象
  if (obj instanceof Array) {
    result = [];
  } else {
    result = {};
  }

  for (let key in obj) {
    // 保证key不属于原型上的东西
    if (obj.hasOwnProperty(key)) {
      // 递归
      result[key] = deepClone(obj[key]);
    }
  }
  // 返回结果
  return result;
}

```

对于JSON安全(也就是说可以被序列化为一个JSON字符串并且可以根据这个字符串解析出一个结构和值完全一样的对象)的对象来说，可以采取以下复制方法

```js
var newObj = JSON.parse(JSON.stringify(someObj))
```



### typeof 运算符

- 识别所有值类型
- 识别函数
- **仅判断是否是引用类型**（不可再细分 不然就不会有isArray isObject）

typeof返回的类型
number、string、boolean、undefined、function、object共6种。
注意：其中Array与null返回的都是object7
function(){}与class{}返回的都是function

### 变量计算 - 类型转换

- 字符串拼接
-  == 与 ===

> == 会尽量使得两者相等

```js
100 == '100' //T
0 == '' //T
0 == false //T
false == '' // T
null == undefined //T
```

**除了 == null 之外 其余一律用 === **

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-03_10-37.png)



#### falsely变量与truly变量

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-03_11-05.png)

```
// 两步非运算为true的 就是 truly变量
// 反之为falsely变量
//if判断的时候 只会判断该变量属于falsely变量与truly变量
```

另外**重要**

```
Boolean([]); //true
Number([]); //0
Number({}); // NaN
Number(false); //0
```



#  第三章 **原型和原型链**

- 题目

   1. 如何判断一个变量是不是数组？

      instanceof 

   2. 手写一个简易的JQuery，考虑插件的扩展性

   3. class的原型本质，怎么理解？

### 

## 类型判断 instanceof

> instanceof 可以判断具体引用类型 

```js
class Person {
    //this 在 类 中的表现与在函数中类似，因为类本质上也是函数，但也有一些区别和注意事项。在类的构造函数中，this 是一个常规对象。类中所有非静态的方法都会被添加到 this 的原型中：
  constructor(name) {
    this.name = name;
  }
  walk() {
    console.log("I am walking here");
  }
}
class Student extends Person {
  constructor(name, number) {
    super(name);
    this.number = number;
  }
  sayHi() {
    console.log(`姓名${this.name},学号${this.number}`);
  }
}

// 通过类声明对象/实例
const student = new Student("何贤", "100");

student.sayHi();
student.walk();

```



![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-03_14-13.png)

## 原型（重要）

- 每个class都有显式原型prototype
- 每个实例都有隐式原型(__proto_ _)
- 实例的隐式原型指向对应class的显式原型

### 属性的引用

当我们视图引用对象的属性时,会触发[[GET]]操作。而对于默认的[[GET]]操作来说

- 首先会检查对象本身是否具有这个属性，有就使用它
- 如果无法在对象本身中找到这个需要的属性，就会据需访问对象的Prototype链

```js
var anotherObject = {
  a: 2,
};
var myObj = Object.create(anotherObject);

console.log(myObj.a); //a

```

- 但是如果在上一层的原型链中依然找不到这个需要的属性，就会继续向上查找
- 这个过程会持续直到找到匹配的属性名或者查找完整的原型链（最终会返回undefined）

### 属性设置和屏蔽

如果在Prototype链上存在名为a的普通访问属性，并且没有被标记为只读，那就会直接在myObj中添加一个名为a的新属性，他是屏蔽属性

```js
var anotherObject = {
  a: 2,
};
var myObj = {
};
var myObj = Object.create(anotherObject);

myObj.a = 4;
console.log(myObj.a); //4
console.log(anotherObject.a);//2
```

如果在原型链上存在某个属性，但标记为只读，那么无法修改已有属性或者在myObject上创建屏蔽属性。

```js
// "use strict";
var anotherObject = {
  a: 2,
  c: 3,
};
Object.defineProperty(anotherObject, "c", {
  writable: false,
});
var myObj = {
  // a: 3,
};
var myObj = Object.create(anotherObject);

myObj.a = 4;
myObj.c = 9; //无法创建 在严格模式下会报错
myObj.d = 6; //d没有任何限制
console.log(myObj);//{a:4,d:6}
console.log(anotherObject);//{a:2,c:3}
```



## 原型链

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-03_14-35.png)

#### jQuery手写

```js
class jQuery {
  constructor(selector) {
    const result = document.querySelectorAll(selector);
    // console.log(result);
    const length = result.length;
    for (let i = 0; i < length; i++) {
      this[i] = result[i];
    }
    this.length = length;
    this.selector = selector;
  }
  get(index) {
    return this[index];
  }
  each(fn) {
    for (let i = 0; i < this.length; i++) {
      const element = this[i];
      fn(element);
    }
  }
  on(type, fn) {
    return this.each((element) => {
      element.addEventListener(type, fn, false);
    });
  }
}

// 考虑插件和扩展性

// 插件
jQuery.prototype.dialog = function (info) {
  console.log(info);
};

// 复写
class myjQuery extends jQuery {
  constructor(selector) {
    super(selector);
    this.anthor = "hexian";
  }
  addClass(className) {
    console.log("待完善");
  }
}

```



# 第四章 作用域、自由变量和闭包

> 三级作用域 ：全局作用域 函数作用域 块级作用域
>
> 自由变量：当前作用域无目标变量就会前往父级作用域寻找



题目

- this的不同应用场景，如何取值
- 手写bind函数
- 实际开发中闭包的应用场景，举例说明
- 创建10个a标签 ，弹出不同的弹框

- 

## 作用域

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-04_11-09.png)

作用域的种类有 

1. 词法作用域
2. 函数作用域
3. 块级作用域
4. 全局作用域

### 词法作用域

```js
function foo(a) {
	var b = a * 2;
	function bar(c) {
		console.log( a, b, c );
	}
	bar( b * 3 );
}
foo( 2 ); // 2, 4, 12
```

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-07_17-46.png)

1. 包含着整个全局作用域，其中只有一个标识符： foo 。
2. 包含着 foo 所创建的作用域，其中有三个标识符： a 、 bar 和 b 。
3. 包含着 bar 所创建的作用域，其中只有一个标识符： c 。

*它首先从最内部的作用域，也就是 bar(..) 函数的作用域气泡开始查找。引擎无法在这里找到 a ，因此会去上一级到所嵌套的 foo(..) 的作用域中继续查找。*

#### 欺骗词法

>  词法作用域完全由写代码期间函数所声明的位置来定义

- eval

```bash
eval() 的参数是一个字符串。如果字符串表示的是表达式，eval() 会对表达式进行求值。如果参数表示一个或多个 JavaScript 语句，那么eval() 就会执行这些语句。不需要用 eval() 来执行一个算术表达式：因为 JavaScript 可以自动为算术表达式求值。
```

*TIPS: 虽然任何使用 [`var`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var) 声明的属性不能从全局作用域或函数的作用域中删除。以及任何let与const不能从其局部作用域中删除，但是由eval()生成的变量是可以删除的*

### 函数作用域

> 引用：https://www.cnblogs.com/shihaiying/p/12006624.html 有兴趣可以去看原作者

1. 调用函数创建函数作用域，函数执行完毕后函数作用域销毁
2. 每调用一次，函数就会创建一个新的函数作用域，它们只之间是相互独立的
3. 函数作用域中可以访问到全局作用域变量，全局作用域不能访问函数作用域变量
4. 当在作用域操作一个变量时，**它会先在自身作用域中寻找，如果有就直接使用***，**如果没有则向上一级作用域中寻找，直到找到全局作用域**，**如果全局作用域中依然没有找到，则会报错 ReferenceError**
5. **在函数作用域也有声明提前的特性**，这点我会在后续**变量提升**中再次提到
6. 在函数中，不用var let const 修饰的变量会变成全局变量
7. **定义形参就相当于在函数作用域中声明了变量**

### 块级作用域

> 当使用var声明变量时，它写在哪里都是一样的，因为它最终都会属于外部作用域,这时为了确保作用域其他地方没用意外使用重名变量只能依靠自觉性

**块作用域是一个用来对之前*最小授权原则*进行扩展的工具**，将代码从在函数隐藏信息扩展为在块中隐藏信息

常见有

- let
- with
- try/catch

## 提升（函数 + 变量）

> 在JS里 所有的声明都会在任何代码被执行前首先处理

### 变量提升

常见的一个场景就是

```js
console.log(a);//undefined
var a =2;
```

他不会像let 与 const声明一样 出现Reference异常被抛出，而是log一个undefined。原因就在于

JavaScript会将 *var a = 2*看成两个声明

```js
var a;//定义声明在编译阶段执行

a =2;//赋值声明会留在原地
```

那么 原场景过程就如下

```js
var a;
console.log(a);
a =2;
```



只有定义声明会被提升，而赋值或其他运行逻辑会留在原地，所有有时会扰乱代码执行顺序，值得注意的是**每个作用域都会进行提升操作** 

### 函数提升

*小标题取名提升而不变量提升，原因是提升不是变量的专属，函数也同样具有提升*

```js
foo()//2

function foo() {
    console.log(a)
    var a =2
}
```

这里 foo函数声明(这个例子还包含了函数的隐含值)被提升了，因此 可以正常在第一行执行。但注意*函数提升时，虽然内部对var a也同样进行了提升，但显然不是提升到函数的最上方*

#### 函数表达式

> 函数虽然会提升 但是函数表达式却不会被提升

```js
foo();//TypeError

var foo = function bar () {
	//..doSomething
}
```

此过程可以理解为

```js
var foo //==>undefined
foo(); // TypeError
bar(); //ReferenceError

var foo = function bar ()
{
    // doSomething
}

```

foo提升后未赋值便调用，等同于对undefined进行函数调用导致非法操作，因此抛出TypeError，而我们在调用bar时却会发现出现了ReferenceError。这表明**即使是具名函数，名称标识符在赋值之前也无法在函数作用域中使用**

#### 函数优先

> 函数声明与变量声明都会被提升，而函数会首先被提升，然后才是变量

```js
foo();// 1

var foo;

function foo(){
	console.log(1)
}

foo = function foo() {
	console.log(2);
}
```

以上将会被JS引擎视为

```js
function foo() {
	console.log(1)
}

var foo;

foo();

foo = function () {
    var foo = ..self
}
```



### 覆盖优先级

**值得注意的是，尽管var foo 经过提升 应该是在 function foo 后面 但是却没有将其覆盖 而是被忽略了**

导致此类原因是**JavaScript中一般变量、函数、函数参数重名时的覆盖优先级** 后序会有小节提到



> **原文链接：https://blog.csdn.net/qq_51066068/article/details/124015286**
>
> 节选原作者：[The..Fuir](https://blog.csdn.net/qq_51066068)

```bash
已初始化的一般变量 > 函数 > 函数参数 > 未初始化的一般变量
规则是：
1、对于同名的变量声明，Javascript采用的是忽略原则，后声明的会被忽略，变量声明和赋值操作可以写在一起，但是只有声明会被提升，提升后变量的值默认为undefined，结果是在赋值操作执行前变量的值必为undefined

2、对于同名的函数声明，Javascript采用的是覆盖原则，先声明的会被覆盖，因为函数在声明时会指定函数的内容，所以同一作用域下一系列同名函数声明的最终结果是调用时函数的内容和最后一次函数声明相同

3、对于同名的函数声明和变量声明，采用的是忽略原则，由于在提升时函数声明会提升到变量声明之前，变量声明一定会被忽略，所以结果是函数声明有效
```

![](https://img-blog.csdnimg.cn/5b0baed85b6f43368e8355c75483de90.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVGhlLi5GdWly,size_19,color_FFFFFF,t_70,g_se,x_16)



## 闭包

> 当函数执行时导致函数被定义并抛出

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-04_11-24.png)



### 垃圾回收机制

> 原帖出处：https://zh.javascript.info/garbage-collection#yi-ge-jian-dan-de-li-zi
>
> js中的内存管理是自动的，每当我们创建函数、对象、数组的时候会自动的分配相应的内存空间;

当我们不再需要某个东西时会发生什么？JavaScript 引擎如何发现它并清理它？

[可达性（Reachability）](https://zh.javascript.info/garbage-collection#ke-da-xing-reachability)

JavaScript 中主要的内存管理概念是 **可达性**。

简而言之，“可达”值是那些以某种方式可访问或可用的值。它们一定是存储在内存中的。

```
1. 这里列出固有的可达值的基本集合，这些值明显不能被释放。

   比方说：

   - 当前执行的函数，它的局部变量和参数。
   - 当前嵌套调用链上的其他函数、它们的局部变量和参数。
   - 全局变量。
   - （还有一些内部的）

   这些值被称作 **根（roots）**。

2. 如果一个值可以通过引用链从根访问任何其他值，则认为该值是可达的。

   比方说，如果全局变量中有一个对象，并且该对象有一个属性引用了另一个对象，则 **该** 对象被认为是可达的。而且它引用的内容也是可达的。下面是详细的例子。

在 JavaScript 引擎中有一个被称作 [垃圾回收器](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)) 的东西在后台执行。它监控着所有对象的状态，并删除掉那些已经不可达的。
```



[一个简单的例子](https://zh.javascript.info/garbage-collection#yi-ge-jian-dan-de-li-zi)

这里是一个最简单的例子：

```javascript
// user 具有对这个对象的引用
let user = {
  name: "John"
};
```

这里的箭头描述了一个对象引用。全局变量 `"user"` 引用了对象 `{name："John"}`（为简洁起见，我们称它为 John）。John 的 `"name"` 属性存储一个原始值，所以它被写在对象内部。

如果 `user` 的值被重写了，这个引用就没了：

```javascript
user = null;
```

现在 John 变成不可达的了。因为没有引用了，就不能访问到它了。垃圾回收器会认为它是垃圾数据并进行回收，然后释放内存。

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-12_11-46.png)

[两个引用](https://zh.javascript.info/garbage-collection#liang-ge-yin-yong)

现在让我们想象下，我们把 `user` 的引用复制给 `admin`：

```javascript
// user 具有对这个对象的引用
let user = {
  name: "John"
};

let admin = user;
```

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-12_11-46_1.png)

现在如果执行刚刚的那个操作：

```javascript
user = null;
```

……然后对象仍然可以被通过 `admin` 这个全局变量访问到，因此它必须被保留在内存中。如果我们又重写了 `admin`，对象就会被删除。

[相互关联的对象](https://zh.javascript.info/garbage-collection#xiang-hu-guan-lian-de-dui-xiang)

现在来看一个更复杂的例子。这是个家庭：

```javascript
function marry(man, woman) {
  woman.husband = man;
  man.wife = woman;

  return {
    father: man,
    mother: woman
  }
}

let family = marry({
  name: "John"
}, {
  name: "Ann"
});
```

`marry` 函数通过让两个对象相互引用使它们“结婚”了，并返回了一个包含这两个对象的新对象。

由此产生的内存结构：

到目前为止，所有对象都是可达的。

现在让我们移除两个引用：

```javascript
delete family.father;
delete family.mother.husband;
```

仅删除这两个引用中的一个是不够的，因为所有的对象仍然都是可达的。

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-09-12_11-58.png)

但是，如果我们把这两个都删除，那么我们可以看到再也没有对 John 的引用了：

**对外引用不重要，只有传入引用才可以使对象可达**。所以，John 现在是不可达的，并且将被从内存中删除，同时 John 的所有数据也将变得不可达。

### 标记清理

标记清理是js最常用的回收策略，2012年后所有浏览器都使用了这种策略，此后的对回收策略的改进也是基于这个策略的改进。其策略是：

```
1. 变量进入上下文，也可理解为作用域，会加上标记，证明其存在于该上下文；
2. 将所有在上下文中的变量以及上下文中被访问引用的变量标记去掉，表明这些变量活跃有用；
3. 在此之后再被加上标记的变量标记为准备删除的变量，因为上下文中的变量已经无法访问它们；
4. 执行内存清理，销毁带标记的所有非活跃值并回收之前被占用的内存；
```



![](https://liyucang-git.github.io/img/localBlog/gc_mark_sweep.gif)



了解了JS的垃圾回收机制后 我们来看以下代码

```js
function foo() {
  var a = 2;
  function bar() {
    console.log(a);
  }
  return bar;
}
var baz = foo();
baz(); //2
```

在foo执行后，理应因为垃圾回收机制而被销毁的foo内部作用域确得以保留，正是因为闭包。

**闭包的神奇之处就是可以阻止这件事情的发生，如上例。因为bar()声明的位置，他拥有覆盖了foo()内部作用域的闭包，使得作用域能够一直存活。**而这个引用关系就叫闭包。

实际上，无论通过何种手段将内部函数传递到所在词法作用域意外的地方，他都会继续持有对原始定义作用域的引用，无论在何处执行这个函数都能形成闭包。

*例如*

```js
function foo() {
  var a = 2;
  function bar() {
    console.log(a);
  }
  // return bar;
  baz(bar);
}
// var baz = foo();
function baz(fn) {
  fn();
}
foo();
```



### 闭包的实际应用场景

1. 模仿块级作用域

   ```js
   for(var i = 0; i < 5; i++) {
       (function(j){
           setTimeout(() => {
               console.log(j);
           }, j * 1000);
       })(i)
   }
   ```

   

2. 私有变量

JavaScript中没有私有成员的概念，所有属性都是公有的。但是有私有变量的概念，任何在函数中定义的变量，都可以认为是私有变量，因为在函数的外部不能访问这些变量。**私有变量包括函数的参数，局部变量和函数内部定义的其他函数。**

来看下面这个例子

```javascript
function add(a, b) {
    var sum = a + b;
    return sum;
}
```

在 `add` 函数内部，有3个私有变量，`a`, `b`, `sum`。只能在函数内部访问，函数外面是访问不到它们的。但是如果在函数内部创建一个闭包，闭包可以通过自己的作用域链就可以访问这些变量。所以利用闭包，我们就可以**创建用于访问私有变量的公有方法（也称为特权方法）**。

有两种在对象上创建特权的方法。
**第一种，在构造函数中定义特权方法**

```javascript
function MyObject() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    // 特权方法
    this.publicMethod = function() {
        privateVariable++;
        return privateFunction;
    }
}
```

这个模式在构造函数内部定义了私有变量和函数，同时创建了能够访问这些私有成员的特权方法。能够在构造函数中定义特权方法，是因为特权方法作为闭包有权访问在构造函数中定义的所有变量和函数。
上面代码中，变量 `privateVariable` 和函数 `privateFunction()` 只能通过特权方法 `publicMethod()`来访问。在创建 MyObject 实例后，只能使用 `publicMethod`来访问 变量 `privateVariable` 和函数 `privateFunction()`

**第二种，利用私有和特权成员，可以隐藏哪些不应该被直接修改的数据。**

```javascript
function Foo(name){
    this.getName = function(){
        return name;
    };
};
var foo = new Foo('luckyStar');
console.log(foo.name); //  => undefined
console.log(foo.getName()); //  => 'luckyStar'
```

上面代码的构造函数中定义了一个特权方法 `getName()`,这个方法可以在构造函数外面使用，可以通过它访问内部的私有变量`name`。因为该方法是在构造函数内部定义的，作为闭包可以通过作用域链访问name。私有变量 `name`在 `Foo`的每个实例中都不一样，因此每次调用构造函数都会重新创建该方法。

在构造函数中定义特权方法的缺点就是你必须使用构造函数模式。之前一篇文章 [JavaScript的几种创建对象的方式](https://link.segmentfault.com/?enc=CjM9R1Ex0xftdgG2pGO2Mw%3D%3D.6FQs7zg7nXs54lYR5tAvZDqgOhAS7%2FE9mnZ82ikmQ%2FpRLz%2B%2BBbFFimLYmYOWnwmmpRsotXPbB6yGrdTJTUUUYg%3D%3D) 中提到构造函数模式会针对每个实例创建同样一组新方法，使用静态私有变量实现特权可以避免这个问题。

## this(重要)

> this关键字是JS中最复杂的机制之一，通常他会被自动定义在所有函数的作用域中

### 绑定规则

> 函数执行过程中this会依据调用位置决定this的绑定对象，判断需要应用到以下的四条规则

-

### 默认绑定

> 通常为独立函数调用，无法引用到其他规则时的默认规则,该绑定的优先级最低（后续会提到四种绑定的优先级）

```js
function foo () {
	console.log(this.a)
}
var a =2;
foo(); //2
```

*TIPS:需啊哟注意的是，如果使用严格模式(strict mode)，则不会出现全局对象被用于默认绑定，因此this会绑定到undefined；*

### 隐式绑定

> 隐式绑定规则会把函数调用中this绑定到这个上下文对象上(context),且对象属性引用链中只有最后一层在调用的位置中起作用

例:

```js
function foo() {
    console.log(this.a)
}

var obj = {
    a:2,
    foo:foo//引用
}
obj.foo();//2
```

此时的this指向了obj对象，但是无论直接在obj中定义还是如本例先定义再添加为引用属性而言，这个函数严格来说都不属于obj对象**然而调用位置会使用obj上下文来引用函数**

#### **隐式丢失**

> 隐式绑定的函数会丢失绑定对象，从而使用默认绑定的规则，将this绑定到全局对象或者undefined上(严格模式)

```js
function foo() {
	//doSomething
}
var obj = {
    a:2,
    foo:foo
}
var bar = obj.foo
bar() // 内部this 应用默认绑定
```



这里虽然bar是对obj.foo的一个引用，但它实际上引用的是foo函数本身，因此当我们执行bar()时，等同于默认绑定规则下的独立函数调用。

这里还有一种更加隐晦的隐式丢失

```js
function foo () 
{
    //doSomething
}
function doFoo (fn) 
{
    fn();
}
var obj = {
    //params
    foo:foo
}
doFoo(obj.foo)// this依然应用默认绑定规则

```

- 原因在于，函数的参数传递实际上是一种隐式赋值。因此

```js
function doFoo (fn)
{
	//赋值过程 可以看做
	let fn = fn;
	fn()
}
```

这里赋值完成后调用就满足独立函数调用的情形，符合默认绑定



2. 隐式绑定规则（弃用，写的不是很好）

其中 foo函数内部的（立即执行环境this指向全局对象）

```js
    function test() {
      console.log(this); 
    }
    test();//window	

//等效于 以下写法 函数声明后执行 == 函数立即执行
  (function(){
      console.log(this) //window
  })();
// 什么都不借助 独立调用 this指向window 
```



需要改变这个 this 的指向，是因为原来的 this 被污染了，需要重新再进行 this 指向，因为，this 指向的是被调用的父级作用域，而如果函数在另一个函数里面执行的时候，那么，这个 this 的指向的就是这个函数，而不是那个被执行函数原来的那个作用域。

```js
const fighter = {
  model: '700',
  fire: function (res) {
    console.log(this.model);
    console.log(res);
  },
};
setTimeout(function () {
  fighter.fire();
}, 1000);

// undefined
```



- 通过对象.方法()调用，this就指向这个对象

```js
let Test = {
	name:"Fan",
	say:function(){
		console.log(this.name)
	}
}
Test.say()	//Fan
复制代码此时，this就指向Test
不过，匿名函数(比如：定时器)具有全局性，因此this对象通常也指向window

```

- 函数作为数组的一个元素，通过数组下标调用的，this指向这个数组

```js
function Test() {
	console.log(this)
}
let arr = [Test,1,2]
arr[0]();
this指向arr	
```



- **函数作为window内置函数的回调函数调用时，this就指向window，比如：setInterval setTimeout 等）**



- **函数作为构造函数，用new关键字调用时，this指向新new出的对象**(重要)

> new到底做了什么

*在MDN官方文当中写到**`new` 运算符**创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。*

### **new绑定**

1. 创建一个空的简单JavaScript对象
2. 为上述对象添加属性**_ _ proto _ _**，将该属性链接至构造函数的原型对象
3. 将刚才创建的实体对象作为this的context 上下文
4. 如果该函数没有返回对象，则返回**this**

常见一个用户自定义的对象需要两部：

1. 通过编写函数来定义对象类型

2. 通过new来创建对象实例

   

   当new Student被执行时 会发生以下事情

3. 一个继承自Student.prototype的新对象被创建

4. 使用指定参数调用constructor，并将this绑定到新创建的对象new Student 等同于 new Student（）在没有指定参数列表的情况下

5. 由构造函数返回的对象就是 `new` 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤 1 创建的对象。（一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤）

### 显式绑定

- **如果使用 call、apply、bind绑定对象，那么this就指向那个绑定的对象**

首先我们看一下显式绑定的前身，硬绑定

- 硬绑定

```js
function foo()
{
	console.log(this.a)
}
var obj = {
	a:2
}
var bar = function() {
	foo.call(obj)
}
bar();//此时bar的this永远都不可能更改
bar.call(window)// this依然指向bar

```

在这里我们创建了函数bar()，它在内部手动调用了foo.call(obj)。因此强制将foo的this绑定到了obj上。无论之后如何调用函数bar。他都会手动在obj上调用function，这是一种强制绑定，我们称之为硬绑定。

**简述**：

- **call()接收的是参数列表** call可以调用函数 call可以改变函数中的this
- **apply()接收参数数组**

#### **call&&apply**

```js
function fun() {
	console.log(this.name)
}
let cat = {
	name="暹罗"
}
let dog = {
	name="中华田园"
	sayName() {
		console.log("I am "+this.name)
	}
	eat(food1,food2) {
		console.log('I like eating ' + food)
	}
} 
fun.call(cat) // 暹罗
dog.sayName.call(cat) //I am 暹罗

//阐述传递
dog.eat("骨头",'肉')// I like eating 骨头 肉
// call传递参数（参数列表形式）
dog.eat.call(cat,'鱼','肉')// I like eating 鱼 肉
dog.eat.apply(cat,['鱼','肉'])
```

#### **bind**

> bind不会立即执行

```
let fun = dog.eat.bind(cat,'鱼',‘肉’)；
fun()
```

#### 手写call、apply、bind等函数

```js
Function.prototype.call1 = function (context) {
  var context = context || window; //获取调用对象
  let symbol = Symbol(); // 使用一个独一无二的值作为添加的新属性
  context[symbol] = this; //给context添加一个方法 让它指向this
  let args = [...arguments].slice(1); // 分割类数组 获得纯参数
  let result = context[symbol](...args); // 执行方法并获取返回对象 call 接受参数形式不是数组
  delete context[symbol]; // 删除添加函数
  return result;
};
```

```js
Function.prototype.apply1 = function (context) {
  var context = context || window; // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  //使用symbol 选择一个独一无二的值作为新添加的属性
  let symbol = Symbol();
  context[symbol] = this;
  let args = [...arguments].slice(1);
  let result = context[symbol](args);
  // 删除添加的函数
  delete context[symbol];
  return result;
};
```



```js
Function.prototype.myBind = function (context) {
  if (typeof this !== "function") {
    throw new TypeError("Error");
  }
  var _this = this;
  var args = [...arguments].slice(1);
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments);
    }
    return _this.apply(context, args.concat(...arguments));
  };
};

```

```js
const obj = {
  name: "xiaoxiao",
  getName: function (arg) {
    console.log(`我是${this.name}里面的,我里面有${arg}`);
  },
};
obj.getName([0, 0, 0, 0, 0]); // 我是xiaoxiao里面的
const obj2 = {
  name: "huahua",
};
//传值不一样

obj.getName.call1(obj2, 1, 1, 1, 1, 1, 1);
obj.getName.myBind(obj2)(2, 2, 2, 2, 2, 2);
obj.getName.apply1(obj2, [3, 3, 3, 3, 3, 3]);
```



- ### **箭头函数**

> 箭头函数比较特殊，因为箭头函数内部没有this，this指向外层最近的调用者，简单的说，就是需要向上找一级
>
> - 箭头函数在调用时，不会生成自身作用域下的this和arguments
> - 不像普通函数一样在调用时自动获取this，而是沿着作用域链向上查找，找到最近的外部一层作用域的this，并获取
> - 在定义对象的方法/具有动态上下文的回调函数/构造函数中都不适用



# 第五章 异步

> JS总是先同步 再异步。

## 5.1 Event Loop

### 5.1.1关于JavaScript

javascript是一门**单线程**语言，在最新的HTML5中提出了Web-Worker，但javascript是单线程这一核心仍未改变。所以一切javascript版的"多线程"都是用单线程模拟出来的，一切javascript多线程都是纸老虎！

### 5.1.2 javaScript事件循环

> 作者：ssssyoki
> 链接：https://juejin.cn/post/6844903512845860872
> 来源：稀土掘金其中事件任务分为同步任务与异步任务

当我们打开网站时，网页的渲染过程就是一大堆同步任务，比如页面骨架和页面元素的渲染。而像加载图片音乐之类占用资源大耗时久的任务，就是异步任务。关于这部分有严格的文字定义，但本文的目的是用最小的学习成本彻底弄懂执行机制，所以我们用导图来说明：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/11/21/15fdd88994142347~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

同步和异步任务分别进入不同的执行"场所"，**同步的进入主线程，异步的进入Event Table并注册函数。**

**当指定的事情完成时，Event Table会将这个函数移入Event Queue。**

**主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。**

上述过程会不断重复，也就是常说的Event Loop(事件循环)。

```js
let data = [];
$.ajax({
    url:www.javascript.com,
    data:data,
    success:() => {
        console.log('发送成功!');
    }
})
console.log('代码执行结束');

```

上面是一段简易的`ajax`请求代码：

- ajax进入Event Table，注册回调函数`success`。
- 执行`console.log('代码执行结束')`。
- ajax事件完成，回调函数`success`进入Event Queue。
- 主线程从Event Queue读取回调函数`success`并执行。

### 5.1.3要注意的一些pendingTimer

要注意setTimeout的一些**pendingTimer**并没有把回调函数挂载事件循环队列中。他所做的是设定一个定时器。当定时器到时后，环境会把你的回调函数放在事件循环中，这样在未来某个时刻的tick会摘下并执行这个回调。（这里说某个时刻，是因为当回调函数放入Event Queue时可能前面有未执行的任务，不能立马轮到他）

### 5.1.4 宏微任务

- macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
- micro-task(微任务)：Promise，process.nextTick

不同类型的任务会进入对应的Event Queue，比如`setTimeout`和`setInterval`会进入相同的Event Queue。

事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。听起来有点绕，我们用文章最开始的一段代码说明：

```javascript
setTimeout(function() {
    console.log('setTimeout');
})

new Promise(function(resolve) {
    console.log('promise');
}).then(function() {
    console.log('then');
})

console.log('console');
复制代码
```

- 这段代码作为宏任务，进入主线程。
- 先遇到`setTimeout`，**那么将其回调函数注册后分发到宏任务Event Queue**。(**注意这里仅仅是注册**注册过程与上同，下文不再描述)
- 接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务Event Queue。
- 遇到`console.log()`，立即执行。
- 好啦，整体代码script作为第一个宏任务执行结束，看看有哪些微任务？我们发现了`then`在微任务Event Queue里面，执行。
- ok，第一轮事件循环结束了，我们开始第二轮循环，当然要从宏任务Event Queue开始。我们发现了宏任务Event Queue中`setTimeout`对应的回调函数，立即执行。
- 结束。

事件循环，宏任务，微任务的关系如图所示：

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/11/21/15fdcea13361a1ec~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



### 5.5 例子

```js
function foo(){ //会全部先输出 first 再输出second
    console.log('first');
    setTimeout(function (){
        console.log('second');
    },5);
}
for(var i=0;i< 4399999999;i++) {
    foo();
}
```



## 5.2 Promise

# JavaScript Web API 知识总汇

## DOM

### document对象的内置方法

#### 获取元素节点的方法（元素节点）

> 可以通过document的方法来调用

|        方法名称        |                             作用                             |
| :--------------------: | :----------------------------------------------------------: |
|     getElementById     |      返回一个与那个有着给定id属性值的元素节点对应的对象      |
|  getElementsByTagName  | 返回一个对象数组，每个对象分别对应着文档里有着给定标签的一个元素。 |
| getElementsByClassName |             能够通过class属性中的类名来访问元素              |



#### 获取和设置属性（属性节点）

> 只能通过元素节点的方法来调用

| 方法名称     | 作用                                                     |
| ------------ | -------------------------------------------------------- |
| getAttribute | 输入打算查询的属性名字获取对应属性的值                   |
| setAttribute | 以object.setAttribute(attribute,value)来设置属性节点的值 |

### Node对象的内置属性&方法

#### childNodes属性

> **Node.childNodes** 返回包含指定节点的子节点的集合，该集合为即时更新的集合（live collection）。

#### nodyType属性

> **`nodeType`** 属性可用来区分不同类型的节点，比如 [`元素`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element), [`文本`](https://developer.mozilla.org/zh-CN/docs/Web/API/Text) 和 [`注释`](https://developer.mozilla.org/zh-CN/docs/Web/API/Comment)。

[语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType#语法)

```
var type = node.nodeType;
```

返回一个整数，其代表的是节点类型。其所有可能的值请参考 [节点类型常量](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeType#节点类型常量).

1是元素节点，2是属性节点，3是文本节点。

#### nodeValue属性

[`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node) 的 **`nodeValue`** 属性返回或设置当前节点的值。

[语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/nodeValue#语法)

```
str = node.nodeValue;
node.nodeValue = str;
```

Copy to Clipboard

value 是一个包含当前节点的值的字符串（如果有的话）

#### firstChild属性

**Node.firstChild** 只读属性返回树中节点的第一个子节点，如果节点是无子节点，则返回 `null。`

[语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/firstChild#语法)

```
var childNode = node.firstChild;
```

### lastChild属性

[概述](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/lastChild#概述)

**`Node.lastChild`** 是一个只读属性，返回当前节点的最后一个子节点。如果父节点为一个元素节点，则子节点通常为一个元素节点，或一个文本节点，或一个注释节点。如果没有子节点，则返回 `null`。

[语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/lastChild#语法)

```
var last_child = element.lastChild
```

### 	



## BOM

## 事件绑定

## AJAX

## 存储