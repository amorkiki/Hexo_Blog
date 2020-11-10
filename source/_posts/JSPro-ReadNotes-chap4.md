---
title: JS高级_ReadNotes-chap4
date: 2019-05-01 14:08:38
tags: [ReadNotes, JavaScript]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/007S8ZIlly1ghb610l3zgj30hu095gmg.jpg
---

# I 基本类型 和 引用类型 的值

变量可能包含两种数据类型的值：

- 基本类型的值 ：简单的数据段
- 引用类型的值：可能由多个值构成的对像，保存在内存中

> JavaScript 不允许直接访问内存中的位置，即不能直接操作对象的内存空间。
>
> 当复制 保存着对象的某个变量时，操作的是对象的引用。
>
> 当为对象添加属性时，操作的才是实际的对象。

## 1. 动态的属性

- 定义 基本类型和引用类型的值的方式----创建一个变量，并为其赋值。
- 引用类型的值--可为其添加属性和方法，也可改变和删除属性和方法-----如果对象不被销毁，则属性将一直存在
- 基本类型的值 --不能添加属性

## 2. 复制变量值

- 复制基本类型的值 --在**变量对象**上创建一个新值，将该值复制到位新变量分配的位置上。（新旧完全独立）

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghb610l3zgj30hu095gmg.jpg)

- 复制引用类型的值--复制一份放到新变量分配的空间中，但这个副本实际上是一个指针，指向存储在堆中的一个对象。（新旧引用的是同一个对象，二者互相干涉）

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghb6pej7i6j30rq0hb76j.jpg)

## 3. 传递参数

ECMAScript 中所有参数都是**==按值传递==**。而访问变量有按值和按引用两种方式。

==把函数外部的一个值赋值给函数内部的参数== == ==把值从一个变量复制到另一个变量==

- 向参数传递基本类型的值--将值复制给一个局部变量（命名参数/arguments 对象的一个元素）
- 向参数传递引用类型的值--将值的地址复制给一个局部变量，因此<u>这个局部变量的变化会反映在函数的外部</u>。

```js
function addTen(num) {
  //num:一个局部变量
  num += 10;
  return num;
}

var count = 20; //count ：全局变量
var result = addTen(count); //调用函数后，count的20传递给形参num
alert(count); //20
alert(result); //30

//num 与 count 互不相识，互不干涉
```

```js
function setName(obj) {
  obj.name = "Nicholas"; //obj和person此时引用的是同一个对象
}

var person = new Object(); //new一个实例对象person，它是全局的
setName(person); //将这个对象作参数传递给Object，调用setName函数
alert(person.name); //"Nicholas"   函数在内部给添加的name属性，在外部也会被干涉，

//person和obj指向的是堆内存中的同一个对象， 而且是全局的
```

- ==即使在参数内部修改了参数的值，原始的引用仍然保持不变==
- 当在函数内部重写 obj 时，这个变量引用的就是一个局部对象了，在函数执行完毕后被立即销毁

```js
function setName(obj) {
  obj.name = "Nicholas";
  obj = new Object(); //为obj重新定义了一个对象，
  obj.name = "Greg"; //为该对象定义了带有不同值的name属性
}

var person = new Object();
setName(person);
alert(person.name); //"Nicholas"   说明：这里的person并不是按引用传递
```

## 4. 检测类型

- 检查基本数据类型--`typeof`
- 检查引用数据类型--`instanceof` --返回布尔值 所有引用类型的值都是 Object 实例

```js
var s = "Nicholas";
var b = true;
var i = 22;
var u;
var n = null;
var o = new Object();

alert(typeof s);   //string
alert(typeof i);   //number
alert(typeof b);   //Boolean
alert(typeof u);   //undefined
alert(typeof n);   //object
alert(typeof o);   //object

result = variable instanceof constructor
alert(person instance of Object);  //变量person是Object吗？
alert(colors instance of Array);  //变量colors是Array吗？
alert(pattern instance of RegExp);  //变量pattern是RegExp吗？
```

# II 执行环境及作用域

> `执行环境execution context` 定义了 变量或函数有权访问的其他数据，决定了他们各自的行为。
>
> `变量对象variable object` 每个执行环境都有一个与之关联的变量对象，他保存着环境中定义的所有变量和函数。
>
> 全局执行环境----window 对象
>
> 所有全局变量和函数 都是 作为 window 对象是属性 和 方法创建的。
>
> 执行环境中的代码全部执行完毕后，该环境会被销毁。
>
> 每个函数都有自己的执行环境。
>
> `执行流`
>
> `环境栈`
>
> 执行流进入函数 → 函数被推倒环境栈中 → 函数执行完毕 → 被推出环境栈 → 被交给之前的执行环境

> `作用域链` 代码在环境中执行时创建，<u>保证</u> 对执行环境有权访问的变量和函数的 <u>有序访问</u>
>
> 作用域链 从前往后的顺序： 当前执行环境（函数，活动对象，开始只包含 arguments 对象） → 外部执行环境 → 下一个外部执行环境 ···· → 全局执行环境
>
> 里面的能访问外面的，外面的不能访问里面的。---这些环境之间的联系是线性的，有序的

## 1. 延长作用域链

一些语句可以在作用域前端临时增加一个变量对象，它会在代码执行后被移除。

- try-catch 语句 的 catch 块 ：会创建一个新的变量对象，包含被抛出的错误对象的声明。23
- with 语句 ：会将指定的变量添加到作用域链中

## 2. 没有块级作用域

> if 语句中的变量声明会将变量添加到当前的执行环境（可能是全局环境）
>
> for 语句创建的变量 i 在 for 循环执行结束后，依旧会存在循环外部的执行环境之中

### 2.1 声明变量

> 使用 var 声明的变量会自动被添加到最接近的环境中，
>
> 在函数内部--就是函数的局部环境；
>
> 在 with 语句中--就是函数环境

- 若初始化变量时没有使用 var 声明，则该变量会自动被添加到全局环境

```js
function add(num1, num2) {
  var sum = num1 + num2;
  return sum;
}
var result = add(10, 20); //30
alert(sum); //Error
```

```js
function add(num1, num2) {
  sum = num1 + num2;
  return sum;
}
var result = add(10, 20); //30
alert(sum); //30
```

### 2.2 查询标识符

```js
var color = "blue"; //在外面找到了color是blue

function getColor() {
  return color; //在里面没找到color，就到到外面去找
}
alert(getColor); //blue
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghbbmodu0tj30bd0b8mxi.jpg)

# III 垃圾收集

> JavaScript 有垃圾自动收集机制：执行环境会负责**管理**<u>代码执行过程中使用</u>的**内存**。
>
> 垃圾自动收集机制の原理：找出不再继续使用的变量，释放他们占用的内存。（以固定的时间间隔，周期性执行）
>
> 函数中局部变量的生命周期-----函数执行的过程
>
> 垃圾收集器会标识无用变量。
>
> 可以使用任何方式标记变量。（翻转特殊的位；使用变量列表跟踪）

## 1. 标记清除

最常用的垃圾收集方式----标记清除 mark-and-sweep

标记为：“进入环境”（不能清除）→ “离开环境”

## 2. 引用计数

跟踪记录每个值被引用的次数。-----循环引用问题

## 3. 性能问题

## 4. 管理内存

内存限制问题

解除引用 dereferencing
