---
title: JS高级_ReadNotes-chap5
date: 2019-05-03 20:09:27
tags: [ReadNotes, JavaScript]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfmat17tyj30u00k0jsw.jpg
---

> ECMAScript 中，`引用类型`是一种数据结构，用于将数据和功能组织在一起。--（`类`）
>
> 引用类型有时也被称为`对象定义`，---描述的是<u>一类对象所具有的属性和方法</u>。
>
> `对象`是某个特定引用类型的`实例`。new 一下~~

# I Object 类型

在应用程序中，储存和传递数据。

#### 创建 Object 实例：

1. 使用 Object 构造函数 ：var person = new Object(); = = var person = { }
2. 使用对象字面量： var person = { name : "Nicholas" , age : "21" }

`表达式上下文 expression context` 该上下文期待一个值（表达式）

`语句上下文 statement context` if 条件语句等

※ ==在通过对象字面量定义对象时，不会调用 Object 构造函数==。

※ 对象字面量是向函数传递大量参数的首选： 函数传参时，对必须值使用命名参数，用对象字面量封装多个参数。

#### 访问对象属性：

点表示法： alert(person.name);

方括号表示法： alert(person['name']);

※ 方括号优势：

- 通过变量访问属性 ---------

​ var prototypeName = "name";

​ alert(person[prototypeName]);

- 引用容易引起语法错误的字符、关键字或保留字

  ​ person["first name"] = "Nicholas"

# II Array 类型

ECMAScript 数组的每一项都可以保存任何类型的数据。

ECMAScript 数组的大小是可以动态调整的。

#### 创建数组：

1. 使用 Array 构造函数： --new 可以省略
   var colors = new Array(20); 20 是 length
   var colors = new Array("red", "blue", "green"); 直接向构造函数传递参数

2. 使用数组字面量：
   var colors = ["red", "blue", "green"]
   var colors = [ ];

#### 读取和设置数组的值：

alert(colors[0]);

colors[2]="black"; //修改第三项

colors[3] ="yellow"; //新增第四项

#### 数组的 length 属性：

可读可写-----可以移除或添加数组末尾的项。----改变数组长度

数组最后一个索引为 length-1

`colors[colors.length] = "black";` //在末尾添加 black 色

## 1. 检测数值

传统的 instanceof 操作符的问题----假定只有一个全局执行环境，不适合多框架的开发环境

↓

##### `Array.isArray( )`

--最终确定某个值是不是数组，不论他是在哪个执行环境创建的。

## 2. 转换方法

所有对象都有：`toLocalString()` , `toString()` , `valueOf()` 方法

↓

调用`valueOf()` 返回数组本身

调用 `toString()` 返回的数组中的每个值都是字符串

调用`toLocalString()` 经常返回上述相同的值，不一定。

↓

还可使用`join()` 来使用其他的分隔符，join 默认逗号分隔符。

## 3. 栈方法

`栈`----- 一种 LIFO（last-In-First-Out）的数据结构

##### `push()`

--推入 : 接受任意数量参数，添加到数组末尾，**返回新数组长度**

##### `pop()`

--弹出 ：从数组末尾移除最后一项，**返回移除的项**

## 4. 队列方法

`队列`----一种 FIFO（First-In-First-Out）的数据结构。

##### `shift()`

- 移除数组中第一项，**返回移除的项** → 与 push()相结合，就是队列的使用

##### `unshift()`

- 在数组前端添加任意项，**返回新数组长度** → 与 pop()相结合

## 5. 重排序方法

##### `reverse()`

- ---- 翻转数组

##### `sort()`

- 按升序排列数组想 ， 该方法会调用每个数组项的 toString()转型，==比较字符串==，

- 会接受一个比较函数作为参数，通过比较：-1，0，1 确定顺序

```js
function compare(value1, value2) {
  if (value1 < value2) {
    return -1;
  } else if (value1 > value2) {
    return 1;
  } else {
    return 0;
  }
}

var values = [10, 1, 15, 0, 5];
value.sort(compare);
alert(values); //  0,1,5,10,15

//按降序只要调整-1 和1 的位置就行
```

```js
//数值类型用简易比较函数
function compare(value1, value2) {
  return value2 - value1;
}
```

## 6. 操作方法

##### `concat()`

- 基于当前数据中的所有项创建一个新数组。

- 先创建一个数组的副本，将接收到的参数添加至末尾，最后返回新构建的数组。

##### `slice()`

- 基于当前数组中的一个或多个项创建新数组。
- 接受一个或两个参数，返回项的起始和结束位置，不包括结束位置；只有一个参数，到数组末尾
- 不影响原始数组
- 若参数为负数，则加上数组长度后再计算

##### `splice()`

- 删除---2 个参数：要删除的第一项 ， 要删除的项数
- 插入---3 个参数，起始位置， 0（要要删除的项数），要插入的任意数量的项
- 替换---3 个参数，起始位置，要删除的项数，要插入的任意数量的项

## 7. 位置方法

##### `indexOf()`

- 接收 2 个参数：要查找的项， 查找起点索引号
- 从数组开头向后查找
- 返回查找项在数组中的位置，没找到返回-1
- 必须是严格相等 ===

##### `lastIndextOf()`

- 接收 2 个参数：要查找的项， 查找起点索引号
- 从数组末尾向前查找
- 返回查找项在数组中的位置，没找到返回-1
- 必须是严格相等 ===

## 8. 迭代方法

每个方法都是：接收 2 个参数：要在每一项上运行的函数，(可选)运行该函数的作用域对象（影响 this 的值）；

传入的函数接收 3 个参数：数组项的值 item，该项的位置 index，数组对象本身 array

##### `every()`

- 在每一项运行传入的函数，该函数每一项都返回 true，则返回 true。

##### `filter()`

- 在每一项运行传入的函数，把该函数返回 true 的项组成数组并返回。

##### `forEach()`

- 在每一项运行传入的函数，没有返回值。

##### `map()`

- 在每一项运行传入的函数，把该函数在每一项上调用的结果组成数组并返回。

##### `some()`

- 在每一项运行传入的函数，该函数对任意项返回 true，则返回 true。

## 9. 归并方法

迭代数组中的所有项，构建一个最终返回的值。

都接收 2 个参数：在每项上调用的函数， （可选）作为归并基础的初始值

传入的函数接收 4 个参数： 前一个值 prev、当前值 cur、项的索引 index、数组对象 array

##### `reduce()`

- 从数组第一项开始遍历

##### `reduceRight()`

- 从数组最后一项开始遍历

# III Date 类型

##### `Date.parse()`

- 接收一个表示日期的字符串参数，返回相应日期**毫秒数**

- 传入字符串不能表示日期，则返回 NaN

  var someDate = new Date(Date.parse("May 25, 2004"));
  其实后台会自动调用 Date.parse()

##### `Date.UTC()`

- 接收的参数格式为： 年，月（基于 0），日，时，分，秒 --前两个必填

##### `Date.now()`

- 返回调用该方法时的毫秒数
- var start = +new Date(); 使用+操作符 可以获取时间戳，达到同样效果

## 1. 继承的方法

toLocalString() 和 toString() 的返回值问题

## 2. 日期格式化方法

##### `toDateString`

- 以特定于现实的格式显示：星期、月、日、年

##### `toTimeString`

- 以特定于现实的格式显示：时、分、秒、时区

##### `toLocalDateString`

- 以特定于地区的格式显示：星期、月、日、年

##### `toLocalTimeString`

- 以特定于地区的格式显示：时、分、秒、时区

##### `toUTCString`

- 以特定于现实的格式显示：完整的 UTC 日期

## 3. 日期/时间组件方法

# IV RegExp 类型

正则表达式的匹配模式：

- g ：全局模式
- i ：不区分大小写
- m ：多行模式

正则表达式中的元字符：

( [ { \ ^ $ | ) ? * + . } ]

--所有元字符都必须转义。

定义：

- 字面量方式： var pattern = /·······/g

- RegExp 构造函数：
  接收 2 个参数： 要匹配的字符串模式，可选的标志字符串

  var pattern = / [bc]at/i ;

  var pattern = new RegExp (" [bc]at " , " i ") ;

  接收的这 2 个参数都是字符串，在一些情况下要对其进行双重转义。

  | 字面量模式         | 等价的字符串            |
  | ------------------ | ----------------------- |
  | `/\[bc\]at/`       | `"\\[bc\\]at"`          |
  | `/\.at/`           | `"\\.at"`               |
  | `/name\/age/`      | `"name\\/age"`          |
  | `/\d.\d{1,2}/`     | `"\\d.\\d{1,2}"`        |
  | `/\w\\hello\\123/` | `"\\w\\\\hello\\\\123"` |
  | `/\n/`             | `"\\\\"`                |

在 ECMAScript3：

- 正则表达式字面量始终会共享一个 RegExp 实例
- 用构造函数创建的每个新 RegExp 实例都是一个新实例

```js
var re = null,
  i;

for (i = 0; i < 10; i++) {
  re = /cat/g;
  re.test("catastrophe"); //第二次迭代时调用test会失败，这里只有一个RegExp实例，
}

for (i = 0; i < 10; i++) {
  re = new RegExp("cat", "g");
  re.test("catastrophe"); //每次迭代都会创建新的RegExp实例，每调用都会返回true
}
```

## 1. RegExp 实例类型

`global` ----布尔值。是否设置 g 标志

`ignoreCase` ----布尔值。是否设置 i 标志

`lastIndex ` ----整数。开始搜索下一个匹配项的字符位置，从 0 算起

`multiline` ----布尔值。是否设置 m 标志

`source` ----正则表达式的字符串表示，按字面量形式，而非构造函数中的字符串模式返回

## 2. RegExp 实例方法

##### `exec( )`

RegExp 对象的主要方法---exec( )---专门为捕获组设计的。

- 接受一个参数，--要应用模式的字符串。
- 返回包含第一个匹配项信息的数组。没有则返回 null
- 返回的数组时 Array 的实例，且还包含，index 和 input 属性

## 3. RegExp 构造函数属性

## 4.模式的局限性

# V Function 类型

每个函数都是 Function 类型的实例，具有属性和方法。

==函数名 实际上是指向函数对象的指针，不会与某个函数绑定。==

==一个函数可能由多个名字。==

==使用不带括号的函数名，不是调用函数，而是访问指针==

```js
function sum(num1, num2) {
  return num1+num2;
}
alert sum(10,20);  //30

var anotherSum = sum;
alert anotherSum(10,20);  //30

sum = null;
alert anotherSum(10,20);  //30    即使让sum与函数断绝关系，仍可以正常调用函数anotherSum()
```

函数定义：

- 函数声明法： function sum (num1, num2) { return num1 + num2 }

- 函数表达式: var sum = function (num1, num2) { return num1 + num2 } ; ☞ 这里的分号就像声明其他的变量的格式一样
- Function 构造函数（不推荐）

## 1. 没有重载（深入）

后面的函数会覆盖前面的函数----函数名只是指针。

```js
function addSomeNumber (num){
  return num +100;
}
function addSomeNumber (num){
  return num +200;
}
var result = addSomeNumber(100);  //300

↓
↓
↓

var addSomeNumber =function(num){
  return num +100;
}
addSomeNumber =function(num){
  return num +200;
}
var result = addSomeNumber(100);  //300
```

## 2. 函数声明于函数表达式

预解析问题

↓

解析器会率先读取函数声明（函数声明提升--function declaration hoisting）

## 3. 作为值的函数

函数可以作为值来调用，因为函数名本身就是变量。

- 把一个函数传递给另一个函数 （可以传进去）
- 将一个函数作为另一个函数的结果返回 （也可以 return 出来）

```js
function callSomeFunction(someFUnction, someArguments) {
  return someFUnction(someArguments);
}
```

```js
function createComparisonFuncion(propertyName){
  return function(object1, object2) {
    var value1 = object1[propertyName];
    var value2 = object2[propertyName];

		if(value1 < value2){
      return -1;
    }else if (value1 > value2){
      return 1;
    }else {
      return =;
    }
  };
}

var data = [{name:"Nicholas", age:28}, {name:"Greg", age:29}];

data.sort(createComparisonFunction("name"));
alert(data[0].name);   //Nicholas

data.sort(createComparisonFunction("age"));
alert(data[1].name);   //Greg
```

## 4. 函数内部属性

函数内部有两个特殊的对象： `arguments` `this`

arguments 对象有个 `callee`属性， 他是一个指针，指向拥有这个 arguments 对象的函数

```js
//递归函数中的阶乘
function factorial(num) {
  if (num <= 1) {
    return 1;
  } else {
    return num * arguments.callee(num - 1); //消除耦合
  }
}

var trueFactorial = factorial; //获得factorial函数指针

factorial = function () {
  //factorial变为0， 但这与trueFactorial无关
  return 0;
};

alert(trueFactorial(5)); //120
alert(factorial(5)); //0

//若没有用arguments.callee消除耦合，那么trueFactorial会返回0
```

关于`this`：

==函数名字仅仅是包含指针的一个变量而已，即使在不同环境中执行，全局的 sayColor()函数和 obj.sayColor()指向的仍然是同一个函数。==

`caller`属性--其中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，他的值为 null。

## 5. 函数属性和方法

##### 每个函数都包含两个属性：

`length` --函数希望接受的命名参数的个数

`prototype ` --是保存所有实例和方法的真实存在。此属性不可枚举，用 for-in 无法发现

##### 每个函数都包含两个非继承而来的方法：

`apply()` 接受两个参数， 运行函数的作用域，参数数组（可以是 Array 实例，也可是 arguments 对象）

`call()` : 接受两个参数， 运行函数的作用域，参数的一一列举

==apply( ) 和 call( ) 能够扩充函数赖以运行的作用域== ----改变 this 指向---使对象不需要与方法有任何耦合关系

`bind()` 创建一个函数的实例，其 this 的值会被绑定到传给 bind()的值

```js
window.color = "red";
var o = { color: "blue" };

function sayColor() {
  alert(this.color);
}

var objectSayColor = sayColor.bind(o); //这里this值变为o
objectSayColor(); //blue
```

# VI 基本包装类型

没读取一个基本类型值时，后台就会相应创建一个基本包装类型的对象，让我们可以调用一些方法来操作他们。

步骤：

创建一个实例

↓

在实力上调用指定的方法

↓

销毁这个实例

- 引用类型与基本包装类型 主要区别： 对象生存期。

  - new 出来的引用实例：在执行流离开当前作用域之前一直保存在内存中。
  - 自动创建的基本包装类型对象：只存在于一行代码执行瞬间，然后被立即销毁。

- 对基本包装类型的实例调用 typeof----返回 object

- 所有基本包装类型的对象在转换为布尔值类型是值都是 true

- new 出来的构造函数和直接调用的同名转型函数是不一样滴~

  ```js
  var value = 25;
  var number = Number(value); //转型函数--保存的是值
  alert(typeof number); //number

  var obj = new Number(value); //构造函数 --保存的是对象实例
  alert(typeof obj); //object
  ```

## 1. Boolean 类型

是与布尔值对应的引用类型。不建议使用 Boolean 对象。用处不大，容易造成误解

## 2. Number 类型

是与数字值对应的引用类型。不建议使用 Number 对象

Number 类型提供了一些用于将数值格式化为字符串的方法：

`toFixed()` 按照指定小数位返回数值的字符串表示 （会四舍五入）---适用于处理货币值

`toExponential()` 返回质数表示法（e 表示法）的字符串表示

`toPrecision ()` 需要得到表示某个数值的最合适的方法，可能会返回上述两个格式的一种

## 3. String 类型

字符串的对象包装类型。

每个实例都有一个 length 属性，表示字符串中包含的字符个数。

### 3.1 字符方法

访问字符串中特定字符：

`charAt()` --接受一个参数 index，一单字字符串形式返回

`charCodeAt()` --接受一个参数 index，返回字符的编码

也可以直接用[index]来查找

### 3.2 字符串操作方法

`concat()` --拼接一个或多个字符串，返回新的字符串， 不改变原字符串。但在现实中，大多用+号

`slice()` --2 个参数，开始和结束的位置。不包括最后一位；不改变原字符串

`substr() ` -- 2 个参数，开始和的位置和要返回的字符串个数。不改变原字符串

`substring() ` --2 个参数，开始和结束的位置。不包括最后一位；不改变原字符串

```js
var stringValue = "hello world";
alert(stringValue.slice(3)); //"lo world"
alert(stringValue.substring(3)); //"lo world"
alert(stringValue.substr(3)); //"lo world"
alert(stringValue.slice(3, 7)); //"lo w"
alert(stringValue.substring(3, 7)); //"lo w"
alert(stringValue.substr(3, 7)); //"lo worl"

alert(stringValue.slice(-3)); //"rld"   =slice(8)
alert(stringValue.substring(-3)); //"hello world"    将-3转换成了0
alert(stringValue.substr(-3)); //"rld"
alert(stringValue.slice(3, -4)); //"lo w"
alert(stringValue.substring(3, -4)); //"hel"
alert(stringValue.substr(3, -4)); //"" (empty string)
```

### 3.3 字符串位置方法

`indexOf()`

`lastIndexOf()`

```js
var stringValue = "hello world";
alert(stringValue.indexOf("o")); //4
alert(stringValue.lastIndexOf("o")); //7
alert(stringValue.indexOf("o", 6)); //7   从w开始向后
alert(stringValue.lastIndexOf("o", 6)); //4    从w开始向前
```

```js
var stringValue = "Lorem ipsum dolor sit amet, consectetur adipisicing elit";
var positions = new Array();
var pos = stringValue.indexOf("e");

while (pos > -1) {
  positions.push(pos);
  pos = stringValue.indexOf("e", pos + 1);
}

alert(positions); //"3,24,32,35,52"
```

### 3.4 trim()方法

创建副本，删除两端空格， 返回结果，不改变原字符串

### 3.5 字符串大小写转换方法

`toLowerCase()`

`toUpperCase()`

`toLocalLowerCase()`

`toLocalUpperCase()`

```js
var stringValue = "hello world";
alert(stringValue.toLocaleUpperCase()); //"HELLO WORLD"
alert(stringValue.toUpperCase()); //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase()); //"hello world"
alert(stringValue.toLowerCase()); //"hello world"
```

### 3.6 字符串模式匹配方法

`match()` --接收一个正则参数，本质上与调用 RegExp 上的 exec()方法相同

`search()` --接收一个正则参数，返回第一个匹配的索引，没有则返回-1

`replace()` --接收 2 个参数，正则参数，字符串或函数，

`splite()` --基于指定的分隔符将一个字符串分割，并放到数组里

```js
var text = "cat, bat, sat, fat";
var pattern = /.at/;

var matches = text.match(pattern);
alert(matches.index); //0
alert(matches[0]); //"cat"
alert(pattern.lastIndex); //0
var pos = text.search(/at/);
alert(pos); //1
var result = text.replace("at", "ond");
alert(result); //"cond, bat, sat, fat"
result = text.replace(/at/g, "ond");
alert(result); //"cond, bond, sond, fond"
result = text.replace(/(.at)/g, "word ($1)");
alert(result); //word (cat), word (bat), word (sat), word (fat)

function htmlEscape(text) {
  return text.replace(/[<>"&]/g, function (match, pos, originalText) {
    switch (match) {
      case "<":
        return "&lt;";
      case ">":
        return "&gt;";
      case "&":
        return "&amp;";
      case '"':
        return "&quot;";
    }
  });
}

alert(htmlEscape('<p class="greeting">Hello world!</p>')); //&lt;p class=&quot;greeting&quot;&g
var colorText = "red,blue,green,yellow";
var colors1 = colorText.split(","); //["red", "blue", "green", "yellow"]
var colors2 = colorText.split(",", 2); //["red", "blue"]
var colors3 = colorText.split(/[^\,]+/); //["", ",", ",", ",", ""]
```

### 3.7 localCompare()方法

比较两个字符串，返回下列的值中的一个：

- 字母表顺序，顺序反了就返回-1，
- 等于字符串参数， 返回 0
- 字母表顺序，顺序正着的就返回 1

```js
var stringValue = "yellow";
alert(stringValue.localeCompare("brick")); //1
alert(stringValue.localeCompare("yellow")); //0
alert(stringValue.localeCompare("zoo")); //-1

//preferred technique for using localeCompare()
function determineOrder(value) {
  var result = stringValue.localeCompare(value);
  if (result < 0) {
    alert("The string 'yellow' comes before the string '" + value + "'.");
  } else if (result > 0) {
    alert("The string 'yellow' comes after the string '" + value + "'.");
  } else {
    alert("The string 'yellow' is equal to the string '" + value + "'.");
  }
}

determineOrder("brick");
determineOrder("yellow");
determineOrder("zoo");
```
