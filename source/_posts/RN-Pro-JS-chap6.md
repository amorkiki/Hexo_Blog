---
title: RN-Pro_JS-chap6
date: 2020-08-10 02:21:10
tags: [ReadNotes,JavaScript]
---
# I. 理解对象

> 早期：创建自变量对象 = 创建一个object实例，为它添加 属性 和 方法。

## 1. 属性类型

属性 `property`

特性 `attribute`

### 1.1 数据属性

> 包含一个数据值的位置，在此位置上可以读取和写入。

有4个描述其行为的特性attribute：

- [[Configurable]]： 可以用delete删除；修改属性的特性；修改为访问器属性。（default：true）--不可逆
- [[Enumerable]]：用for in 循环；被枚举。直接在对象上定义的属性：（default：true）
- [[Writable]]：修改属性的值。直接在对象上定义的属性：（default：true）
- [[Value]]：包含此属性的数据值。读写通过此。（default：undefined）
- 用Object.defineProperty()创建的新属性，其Configurable、Enumerable、Writable都是默认为false

修改默认的特性：`Object.defineProperty(对象名，属性名，描述符对象)`方法 

```js
var person = {};
Object.defineProperty(person, "name",{
  writable:false,
  value:"Nicholas"
});
alert(person.name); //"Nicholas"
person.name = "Gerg";
alert(person.name); //"Nicholas"   因为name已经设置为只读，不能更改

--------------
var person = {};
Object.defineProperty(person, "name",{
  configurable:false,
  value:"Nicholas"
});
alert(person.name); //"Nicholas"
delete person.name;
alert(person.name); //"Nicholas"  此时不能删除name

//再调用Object.defineProperty()
Object.defineProperty(person, "name",{
  configurable:false,
  value:"Nicholas"
}); //会抛出错误    ----configurable不可逆
```

 

### 1.2 访问器属性

> 不包含数值，包含一对`getter` 和 `setter` 函数（非必需）
>
> 用法：设置一个属性的值（这个值通过对象方法访问）会导致其他属性变化(P141)

有4个描述其行为的特性attribute：

- [[Configurable]]： 可以用delete删除；修改属性的特性；修改为访问器属性。（default：true）--不可逆
- [[Enumerable]]：用for in 循环；被枚举。直接在对象上定义的属性：（default：true）
- [[Get]] ：读取时调用的函数。（default：undefined）
- [[Set]]：写入时调用的函数。（default：undefined）
- 访问器必须使用Object.defineProperty()来定义

```js
var book = {
	_year:2018,
  edition:1
};
Object.defineProperty(book, "year",{
  get:function(){
    return this.year;
  },
  set:function(newValue){
    if (newValue > 2018) {
      this._year = newValue;
      this.edition += newValue - 2018;
    }
  }
});
book.year = 2020;
alert(book.edition); //3
```



## 2. 定义多个属性

`Object.defineProperties(对象名，描述符)`方法

```js
var book = {};
Object.defineProperties(book, {
  _year:{  //数据属性
    writable: true,
    value:2004
  },
  edition:{  //数据属性
    writable: true,
    value:1
  },
  year: {//访问器属性
    get: function(){  
    return this._year;
  },
  	set:function(newValue){
   		if (newValue > 2018) {
      	this._year = newValue;
      	this.edition += newValue - 2018;
   		}
 		}
  }
});
```



## 3. 读取属性の特性

`Object.getOwn PropertyDescriptor(对象名，属性名)` 方法：取得给定属性的描述符

```js
var book = {};
Object.defineProperties(book, {
  _year:{  //数据属性
    writable: true,
    value:2004
  },
  edition:{  //数据属性
    writable: true,
    value:1
  },
  year: {//访问器属性
    get: function(){  
    return this._year;
  },
  	set:function(newValue){
   		if (newValue > 2018) {
      	this._year = newValue;
      	this.edition += newValue - 2018;
   		}
 		}
  }
});

var descriptor = Object.getOwnPropertyDescriptor(boook, "_year");
alert(dexcriptor.value); //2004
alert(descriptor.configurable); //false  因为book是用Object.defineProperties创建的
alert(typeof dexcriptor.get); // "undefined"  数据属性没有get

var descriptor = Object.getOwnPropertyDescriptor(boook, "year");
alert(dexcriptor.value); // undefined 访问器属性没有value
alert(descriptor.enumerable); //false  因为book是用Object.defineProperties创建的
alert(typeof dexcriptor.get); // "function"  数据属性的get是函数

```



# II. 创建对象

## 1. 工厂模式

> 避免用new Object 或对象字面量这种单一接口创建多个对象时产生大量重复代码
>
> 工厂模式：抽象了创建具体对象的过程
>
> 没有解决对象识别问题（获取一个对象的类型）---没有constructor属性

```js
function createPerson (name, age, job){
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function(){
    alert(this.name);
  }
  return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```



## 2. 构造函数模式

> 与工厂模式的区别：
>
> 1. 没有显式地创建对象
> 2. 直接将属性和方法赋值给this对象
> 3. 没有return
> 4. ==创建构造函数类型能便于将来用**它的实例标识为一种特定的类型**==

==构造函数始终都应该用大写字母开头==

```js
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function(){
    alert(this.name);
  } //这里没有return
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

- person1和person2分别保存着Person的一个实例，**<u>同时他们也是Object实例（所有对象均继承自Object）</u>**，分别有一个`constructor属性`，该属性`指向Person`
- constructor属性识别对象类型，但检测对象类型还是`instanceof`靠谱些

### 2.1 将构造函数当做函数

> 构造函数与其他函数的唯一区别： <u>**调用方式不同**</u>
>
> 任何函数如果通过new调用，就可以作为构造函数

```js
//当做构造函数使用
var person1 = new Person("Nicholas", 29, "Software Engineer");
Person.sayName();    //this = person

//当做普通函数
Person("Greg", 27, "Doctor"); //添加到window
window.sayName();   //this = window

//在另一个对象o的作用域调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");   //this = o
o.sayname();

```



### 2.2 构造函数的问题

- 每个方法都要在每个实例上重新创建一遍
- 会导致 不同的作用域和标识符解析，但创建的新实例机制是相同的。
- so，不同实例上的同名函数是不相等的。

可通过把函数定义转移到构造函数外部来解决:

```js
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = sayName;
}
function sayName(){  //设置成全局的sayName 函数，但会破坏封装的隐秘性特点，这个可以用原型链解决 
  alert(this.name);   
}
```



## 3. 原型模式

### 3.1 理解原型对象 

> 每个函数都有一个`prototype`属性，他是一个`指针`，指向一个`对象`，对象包含`共享的属性和方法`。
>
> 可以通过`isPrototype()`方法确定对象间是否存在这种关系，返回布尔值
>
> `Object.getPrototype()`方法，返回[[prototype]]的值， 方便的取得一个对象的原型
>
> 不能通过对象实例重写原型中的值，只能访问。
>
> 可以通过直接将、属性添加在实例中来屏蔽（≠修改）原型对象中的属性（属性访问顺序：就近原则）
>
> 使用`delete`操作符可以完全删除实例中的属性，继而暴露出原型中的属性
>
> 使用`hasOwnProperty()`方法检测一个属性是存在于<u>实例中（返回true</u>），还是存在于原型中（返回false）。



- 对象属性读取的顺序：

1. 对象实例本身
2. 指针指向的原型对象



![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggyslg9kerj30ia0e7wfy.jpg)



![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggyt65lec1j30km0i476q.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggyt69x8n3j30kf0b9t9x.jpg)



### 3.2 原型与in操作符

- 单独使用

  通过对象访问给定属性（实例中或原型中的属性），返回布尔值

  配合hasOwnProperty()，确定属性是在原型中，还是实例中

  ```js
  function hasPrototypeProperty(){
    return !object.hasOwnProperty(name) && (name in object);
  }
  //返回true --属性在原型中
  //返回false-- 属性在实例中
  ```

  

- 在 for in 循环中使用

  返回所有（实例和原型中）能够通过对象访问的、可枚举的属性，即使enumerable标记为false的也能用for in遍历到。

  `Object.keys()方法`--取的对象上所有<u>可枚举的</u>==实例属性==； 返回字符串数组

  `Object.getOwnPropertyNames()`方法--得到<u>所有</u>==实例属性==，无论是否可枚举，**其中包含constructor属性**
  以上两个方法可以代替for in 循环

  

### 3.3 更简单的原型语法

> 用一个包含所有属性和方法的对象字面量重写整个原型对象

```js
function Person(){
  .....
}
Person.prototype = {
  name:"Monika",
  age:20,
  job:"Teacher",
  sayName: function(){
    alert(this.name);
  }
}
var friend = new Person();

alert(friend instanceof Object); //true
alert(friend instanceof Person); //true
alert(friend constructor == Person); //false
alert(friend constructor == Object); //true

```

经过上述操作（本质上完全重写了prototype对象）：

==此时constructor属性就不在指向Person了==， 

因为：==每创建一个函数，就会同时创建它的prototype对象，该对象也会自动获得constructor属性==

所以：这里constructor属性变成了新对象的constructor属性（指向Object构造函数）

需要手动设置constructor的值,调回原有指向

```js
function Person(){
  .....
}
Person.prototype = {
  constructor: Person  //方法一： 手动调回，但会改变Enumerable的值 为true
  name:"Monika",
  age:20,
  job:"Teacher",
  sayName: function(){
    alert(this.name);
  }
}
-------------
  //方法二： 重设构造函数
        Object.defineProperty(Person.prototype, "constructor", {
            enumerable: false,
            value: Person
        });
```



### 3.4 原型的动态性

我们对于原型对象的修改，能从实例上立即被反映。----动态性

原因：==实例与原型之间松散的链接关系==--指针连接

BUT：重写整个原型，会切断新原型与旧原型时实例化的对象的联系

```js
function Person(){

}
var friend = new Person(); //在旧原型之时创建的实例 friend

Person.prototype = { //重写了原型
  constructor: Person,
  name:"Monika",
  age:20,
  job:"Teacher",
  sayName: function(){
    alert(this.name);
  }
}

firend.sayName(); //error  用旧原型创建的实例去访问新原型的方法，会产生错误，因为friend始终指向旧的原型
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggzt0vfualj30ic0ic40q.jpg)

### 3.5 原生对象的原型

所有原生引用类型（Object、Array、String）等 都在其构造函数的原型上定义了方法

如：

在Array.prototype 中可以找到sort()方法

在String.prototype中可以找到substring()方法

### 3.6 原型对象的问题

- 忽略了为构造函数传递初始化参数，结果所有实例在默认情况下都讲取得相同的属性值
- 其共享本性所导致的：对于 包含引用类型值的属性，修改之后会造成不必要的共享

## 4. 组合使用构造函数和原型模式

构造函数模式用于---定义实例属性；向其中传递参数

原型模式用于---定义方法和属性



```js
function Person (name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["You", "cha"];
}
Person.prototype = {
  constructor: Person,
  sayName: function(){
    alert(this.name);
  }
}

var person1 = new Person("kiki", 27, "don'tknow");
var person2 = new Person("mimi", 3, "sleep");

person1.friends.push("mimi");
alert(person1.friends);  //"You, cha, mimi"
alert(person2.friends);  //"You, cha"
alert(person2.friends == person1.friends);  //false
alert(person2.sayName == person1.sayName);  //true
```

## 5. 动态原型模式

```js
function Person (name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  //通过检查某个应该存在的方法是否有效，来判断是否初始化原型，该段代码只执行一次
  //方法
  if(typeof this.sayName != "function"){
    person.prototype.sayName = fucntion(){
    	alert(this.name);
 		 };
  }
}
var friend = new Person("kiki", 27, "don'tknow");
friend.sayName();
```



## 6. 寄生构造函数模式

创建一个函数，仅仅作为封装创建对象的代码，再返回新创建的对象。

除了用new实例化之外， 和工厂模式一样。

构造函数在不return的情况下，默认返回新对象实例。==加一个return，可以重写调用构造函数时返回的值。==

寄生构造函数模式：==返回的对象，与构造函数或与构造函数的原型没有关系==

这个模式：在特殊情况下，为对象创建有特殊方法的构造函数；如创建具有额外方法的数组

```js
function Person(){
  var o = new Object();  //创建了一个新对象
  o.name = name;   //初始化该对象
  o.age = age;
  o.job = job;
  o.sayName = function(){
    alert(this.name);
  }
  return o;  //返回该对象
}
var friend = new Person("Nicholas", 29, "Software Engineer");  
friend.sayName();  //"Nicholas"
```



## 7. 稳妥构造函数模式

稳妥对象：没有公共属性，而且其方法也不引用this的对象。

在一些禁止使用this和new的安全环境中使用

- 稳妥构造函数模式类似寄生构造函数模式，但有2点不同：
  1. 创建对象实例方法不用this
  2. 不用new调用构造函数

# III. 继承

接口继承：只继承方法签名 （ ECMAScript里么有）

实现继承：继承实际的方法

## 1. 原型链

原型链是实现继承的主要方法： 利用原型让一个引用类型继承另一个引用类型的属性和方法。

```js
function SuperType(){
  this.property = true;
}
SuperType.prototype.getSuperValue = function(){
  return this.property;
}

function SubType(){
  this.subproperty = false;
}
//继承了SuperType
SubType.prototype = new SuperType();  // Sub的原型是Super的一个实例，会拥有SuperType的属性和方法
SubType.prototype.getSubValue = function(){
  return this.subproperty;
}


var instance = new SubType();
alert(instance.getSuperValue());  //true 能访问到
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggzvw8vvn4j30mq0d2jti.jpg)



### 1.1 别忘记默认的原型

**所有函数的默认原型都是Object实例，默认原型都会包含一个内部指针指向Object.prototype，**

**这是所有自定义类型都会继承 toString() 、valueOf()等默认方法的根本原型**

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggzwgg2te5j30ih0i640z.jpg)



### 1.2 确定原型和实例的关系

- 通过`instanceof`操作符 ：测试 实例与原型链中出现过的构造函数

  ```
  alert(实例 instanceof 构造函数);   //返回true 或者false
  ```

- 使用`isPrototypeOf()`方法 ：

  ```
  alert(构造函数.prototye.isPrototypeOf(实例));   //返回true 或者false
  ```

### 1.3 谨慎地定义方法

给原型添加方法的代码一定要放在替换原型的语句之后。

在通过原型链继承时，不能通过对象字面量创建原型的方法，这样会重写原型链，切断原型链与实例的关系

### 1.4 原型链的问题

- 原型往往会变成另一个类型的实例。
- 在创建子类型的实例中，不能向超类型的构造函数中传递参数

## 2. 借用构造函数

- 也叫 伪造对象 或  经典继承

- 解决原型中包含引用类型值所带来的的问题：
  ==在子类型的构造函数内部调用超类型的构造函数。==

- 通过使用apply() 和 call()方法 改变this指向，在将来要创建的对象上执行构造函数。
- 将this指向改为子类实例，等于创建了一个副本，达到只在当前副本进行操作而不影响超类以及其他副本的目的。

### 2.1 传递参数

可以实现：在子类构造函数中向超类构造函数传递参数。

```js
function SuperType(name){
  this.name = name;
}
function SubType(){
  //继承同时还传递了参数
  SuperType.call(this, "Nicholas");
  //添加实例属性
  this.age = 28;
}

var instance = new SubType();
alert(instance.name); //"Nicholas"
alert(instance.age); //28
```

### 2.2 借用构造函数的问题

无法进行函数复用，因为方法都在构造函数中定义。

## 3. 组合继承

- 也叫做（伪经典继承）：

  ==原型链+构造函数  组合，通过借用构造函数来实现对实例属性的继承==



```js
function SuperType(name){  //构造函数设置了属性
  this.name = name;
  this.colors=["red","blue","yellow"];
}
SuperType.prototype.sayName = function(){   //原型中添加了方法
  alert(this.name);
}
function SubType(name, age){
  //继承SuperType中的属性，并更改this指向
  SuperType.call(this name);
  this.age = age;  //添加额外自己的属性age
}
//继承方法，SubType原型是SuperType构造函数的一个实例，继承了SuperType原型中添加的方法sayName
SubType.prototype = new SuperType();
SubType.prototype.constructor = SunbType; //手东修改constructor指向，避免切断原型和构造函数间联系
SubType.prototype.sayAge = function(){ //在SubType原型中添加额外sayAge的方法
  alert(this.age);
}

var instance1 = new SubType("Nicholas",29); //创建instance1实例，传参
instance1.colors.push("black"); //在instance1实例上为colors数组追加元素
alert(instance1.colors);  // "red,blue,yellow,black"
instance1.sayName(); //"Nicholas"
instance1.sayAge();//29

var instance2 = new SubType("Greg",21);//创建instance2实例，传参
alert(instance1.colors); // "red,blue,yellow" ,直接继承了SuperType的colors数组
instance2.sayName(); //"Greg"
instance2.sayAge();//21
```

##  4. 原型式继承

借助原有的对象创建新的对象

```js
function object(o){   //普通函数object，传入参数表示原型
  function F(){  //临时性构造函数
    F.prototype = o;
    return new F();  //返回临时类型的一个新实例
  }
}

var person = {  //创建一个person对象，可以作为原型传入object
  name:"胖胖金",
  friends:["花飞飞","杜拉拉","莫小奈"]
}

var anotherPerson = object(person); //创建基于person的新实例
anotherPerson.name = "黄枪枪";   //基于person的修改
anotherPerson.friends.push("岳家枪");   //基于person的修改

var yetanotherPerson = object(person); //创建基于person的新实例
yetanotherPerson.name = "戴南瓜";   //基于person的修改
yetanotherPerson.friends.push("麻条条"); 

alert(person.friends); //"花飞飞, 杜拉拉, 莫小奈, 岳家枪, 麻条条"
```

`Object.create()` 方法

## 5. 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后返回对象。

```js
function createAnother(original){  //original就是将要作为新对象的对象基础
  var clone = object(original);  //调用函数object创建新对象clone
  clone.sayHi = function(){   //以某种方式来增强对象，为clone添加新方法sayHi
    alert("hi");
  };
  return clone; //返回这个对象
}

var person = {  //创建一个person对象，可以作为original传入object
  name:"胖胖金",
  friends:["花飞飞","杜拉拉","莫小奈"]
}

var anotherPerson = createAnother(person)；
anotherPerson.sayHi(); //hi
```



## 6. 寄生组合式继承

组合继承的问题：

调用两次超类型构造函数： ①创建子类型原型的时候 ②在子类型构造函数内部



```js
function SuperType(name){  
  this.name = name;
  this.colors=["red","blue","yellow"];
}
SuperType.prototype.sayName = function(){   
  alert(this.name);
}
function SubType(name, age){
  
  SuperType.call(this name);   //第二次调用 SuperType()，在SubType对象上创建name和colors
  this.age = age;  
}

SubType.prototype = new SuperType();  //第一次调用 SuperType() ,创建实例（实例是SubType的原型），得到name 和colors的属性
SubType.prototype.constructor = SunbType; 
SubType.prototype.sayAge = function(){ 
  alert(this.age);
}


```













