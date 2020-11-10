---
title: JS高级_ReadNotes-chap7
date: 2018-12-15 23:05:13
tags: [ReadNotes, JavaScript]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/007S8ZIlly1ghactp3t19j30o60h10vu.jpg
---

> 函数声明提升---预解析
>
> 匿名函数作用---能创建函数再赋值给变量，就能把函数作为其他函数的值返回（回调函数）--把函数当成值来使用

# I 递归

> 递归函数： 一个函数通过名字调用自身
>
> 消除耦合： arguments.callee

```js
//阶乘函数封装
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * factorial(num - 1);
  }
}
```

用 `arguments.callee`将指针指向当前正在执行的函数，以此实现对函数递归的调用 ⤵️

```js
var anotherFactorial = factorial;
factorial = null;
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1);
  }
}
```

用命名函数表达式实现 ⤵️

```js
var factorial = function f(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * f(num - 1);
  }
};
```

# II 闭包

> 闭包---有权访问另一个函数作用域中的变量 の 函数
>
> 创建闭包---在一个函数内部创建另一个函数

### #函数被调用时发生了什么？

```js
function compare(value1, value2) {
  if (value1 < value2) {
    return -1;
  }else if (value 1 > value2) {
    return 1;
  }else {
    return 0
  }
}
var result = compare(5, 10); //在全局作用域中调用
```

① 创建一个执行环境 execution context

（🌰：全局执行环境）

​ ↓

② 创建相应的作用域链

​ ↓

② 使用 arguments 和其他命名参数的值，初始化函数的活动对象 activation object
（🌰：创建包含 arguments 和 value1 和 val 的活动对象）

​ ↓

③ 函数执行时，为读取和写入 变量 而在 作用域链中查找变量

​ ↓

④ 除了闭包情况以外：函数执行完毕后，局部活动对象会被销毁，内存中仅保存全局作用域。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghactp3t19j30o60h10vu.jpg)

### #变量对象

每个执行环境中都有一个表示变量的对象----变量对象。

> 全局环境中：变量对象始终存在
>
> 局部环境中（compare()函数）：只在函数执行过程中存在。

---

**==闭包会将包含着它的外部函数的活动对象也添加到自己的作用域链中。==**

==并且，在函数执行完毕后，该活动对象也不会被销毁，只会销毁作用域链，==

==因为：匿名函数（闭包）的作用域仍然在引用这个活动对象，直到匿名函数也被销毁，活动对象才会被销毁。==

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghafp8vrjkj30m70hawgn.jpg)

## 1. 闭包与变量

==闭包只能取得外部函数中任何变量的最后一个值==

因为：闭包保存的是整个变量对象，而不是某个特殊变量，所以他们引用的都是同一个变量 i。P181

可以创建匿名函数，强制让闭包符合预期

```js
function createFunctions() {
  var result = new Array();
  for (var i = 0; i < 10; i++) {
    return (function () {
      //将闭包直接赋值给匿名函数，并立即执行改函数，将结果赋值给数组
      return num;
    })(i); //每个函数都会返回各自的索引值
  }
  return result;
}

//思路：将原来闭包返回的结果当做参数传入一个 立即执行的匿名函数，
// 在这个匿名函数内部，又创建并返回了一个访问num的闭包，因此，result数组中的每个函数都有自己的num变量的一个副本，可以返回各自不同的数值了
```

## 2. 关于 this 对象

> this 对象：在函数调用时，基于函数的执行环境进行绑定的。
>
> ==匿名函数执行环境具有全局性==----此时 this 对象通常指 window
>
> ==每个函数在被调用时，都会自动取得两个特殊变量，this 和 arguments==
>
> ==**内部函数在搜索这两个变量是，只会搜索到其活动对象为止，因此永远不可能访问外部函数中的这两个变量**==
>
> ==把外部作用域中的 this，赋值到一个内部函数（闭包）可以访问到的变量里，就可以让闭包访问该对象了==

```js
var name = "the window";

var obj = {
  name: "My object",
  getNamefunc: function () {
    return function () {
      //此匿名函数直接访问不到外部（obj里的）this，匿名函数具有全局性执行环境
      return this.name; //this 指的是getNamefunc，
    };
  },
};
alert(object.getNameFunc()()); //the window
//object是在全局作用下的，this指的是全局作用域
```

```js
var name = "the window";

var obj = {
  name: "My object",
  getNamefunc: function () {
    var that = this; //把外部作用域中的this，赋值到一个闭包可以访问到的变量里，这里的this指My object
    return function () {
      return that.name; //闭包可以访问到外面的that
    };
  },
};
alert(object.getNameFunc()()); //My object
```

```js
var name = "the window";

var obj = {
  name: "My object",
  getName: function () {
    return that.name;
  },
};

object.getName(); //My object
object.getName(); //My object
(object.getName = object.getName)(); //the window  这里赋的值是一个匿名函数object.getName，函数本身不能维持this，匿名函数在全局下呗调用。this指向window
```

## 3. 内存泄漏

闭包在一些 IE 浏览器中，----若闭包的作用域链中保存着一个 HTML 元素，那么该元素将无法被销毁。

# III 模仿块级作用域

```js
(function(){
	//这里是块级作用域
})();

var someFunction = function(){
  //这里是块级作用域
}；
someFunction();
```

> 无论在什么地方，只要临时需要一些变量，就可以使用私有作用域
>
> 在匿名函数中定义的变量，都会在执行结束后被销毁

```js
function outputNumbers(count) {
  (function () {
    for (var i = 0; i < count; i++) {
      //i在执行后会被立即销毁
      alert(i);
    }
  })();
}
```

# IV 私有变量

> 任何在函数中定义的变量，都可以认为是私有变量，在函数外部不能访问。
>
> 私有变量包含：函数参数、局部变量、在函数内部定义的其他函数。
>
> ==特权方法：有权访问私有变量和私有函数的 公有方法。==
>
> ==利用私有和特权成员，可以隐藏那些不想被直接修改的数据。==

```js
function MyObject() {
  //私有变量
  var privateVariable = 10;
  //私有函数
  function privateFunction() {
    return false;
  }

  //特权方法
  this.publicMethod = function () {
    //是一个闭包，把变量和方法的执行全部封在里面，publicMethod()来访问
    privateVariable++;
    return privateFunction();
  };
}
```

```js
function Person(name) {
  //name 是私有变量
  //特权方法1
  this.getName = function () {
    return name;
  };
  //特权方法2
  this.setName = function () {
    name = value;
  };
}

var person = new Person("Nicholas"); //创建一个实例对象
alert(person.getName); //Nicholas
person.setName("Greg");
alert(person.getName); //Greg

//在构造函数中定义特权方法的缺点：每个实例都会创建同样一组新方法，---使用静态私有变量解决（在原型上）
```

## 1. 静态私有变量

```js
(function () {
  //利用函数表达式，因为函数声明只会创建局部函数
  //私有变量
  var privateVariable = 10;
  //私有函数
  function privateFunction() {
    return false;
  }

  //构造函数
  MyObject = function () {
    //这里没用var:因为初始化未经声明的变量，总会创建一个全局变量，MyObject这里是全局变量，能够在私有作用域外被访问
  };

  //公有/特权方法---在原型上定义的
  MyObject.prototype.publicMethod = function () {
    privateVariable++;
    return privateFunction();
  };
})();
```

```js
(function () {
  var name = ""; //name变成了一个静态的、由所有实例共享的属性

  Person = function (value) {
    name = value;
  };

  Person.prototype.getName = function () {
    return name;
  };

  Person.prototype.setName = function (value) {
    name = value;
  };
})();

var person1 = new Person("Nicholas");
alert(person1.getName()); //"Nicholas"
person1.setName("Greg");
alert(person1.getName()); //"Greg"

var person2 = new Person("Michael");
alert(person1.getName()); //"Michael"
alert(person2.getName()); //"Michael"   在一个实例上调用会影响所有实例
//此时每个实例都没有属于自己的私有变量了
```

## 2. 模块模式 module pattern

> module pattern----为单例 singleton 创建私有变量和特权方法
>
> 单例 singleton----只有一个实例的对象，在 JS 中以对象字面量的形式创建

```js
var singleton = {
  name: value,
  method: function () {
    //这里是方法的代码
  },
};
```

模块模式为单例添加私有变量和特权方法，来使其得到增强。

```js
//一个返回对象的匿名函数
var singleton = (function () {
  //私有变量
  var privateVariable = 10;
  //私有函数
  function privateFunction() {
    return false;
  }

  //公有/特权方法和属性----将一个字面量作为函数的返回值，是一个对象字面量定义的--单例公共接口
  return {
    publicProperty: true,

    publicMethod: function () {
      privateVariable++;
      return privateFunction();
    },
  };
})();
```

```js
function BaseComponent() {}

function OtherComponent() {}

//创建一个用于管理组件的application对象
var application = (function () {
  //private variables and functions
  var components = new Array(); //私有数组

  //initialization初始化
  components.push(new BaseComponent()); //添加新实例

  //public interface 公共接口----返回两个特权方法
  return {
    getComponentCount: function () {
      return components.length;
    },

    registerComponent: function (component) {
      if (typeof component == "object") {
        components.push(component);
      }
    },
  };
})();
application.registerComponent(new OtherComponent());
alert(application.getComponentCount()); //2
```

## 3. 增强的模块模式

```js
function BaseComponent() {}

function OtherComponent() {}
var application = (function () {
  //private variables and functions
  var components = new Array();

  //initialization
  components.push(new BaseComponent());

  //create a local copy of application
  var app = new BaseComponent();

  //public interface
  app.getComponentCount = function () {
    return components.length;
  };

  app.registerComponent = function (component) {
    if (typeof component == "object") {
      components.push(component);
    }
  };

  //return it
  return app;
})();
alert(application instanceof BaseComponent);
application.registerComponent(new OtherComponent());
alert(application.getComponentCount()); //2
```
