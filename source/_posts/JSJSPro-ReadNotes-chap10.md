---
title: JS高级_ReadNotes-chap8
date: 2019-01-18 22:06:25
tags: [ReadNotes, JavaScript]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfm9w5uycj31h90u0jwm.jpg
---

DOM 是一个 API（应用程序编程接口）

> DOM 描绘了一个层次化的节点 🌲，允许添加、删除、修改页面的某一部分。

# 节点层次

> DOM 可以将任何一个 HTML 和 XML 文档描绘成一个有多层节点构成的结构。

`文档元素`：在 HTML 中，文档元素始终都是<html>元素。

## 1.Node 类型

`DOM1级` 定义了一个 Node 接口，这个接口将有 DOM 中的所有节点类型实现。

<u>JavaScript 中的所有节点类型都继承自 `Node类型`，</u>so, 所有节点类型都共享着相同的基本属性和方法。

**节点类型--12 个数值常量表示：**

> Node.Element_Node(1);
>
> Node.Attribute_Node(2);
>
> Node.Text_Node(3);
>
> ....

### 1.1 nodeName 和 nodeValue 属性

> 用来了解节点基本信息。
>
> 对于元素节点，nodeName 中始终保存的都是元素的标签名，而 nodeValue 值则始终为 null。

### 1.2 节点关系

每个节点都有的属性：

- childNodes 属性

  - 其中保存着一个`NodeList对象`--类数组对象，用于保存一组有序的节点，通过位置访问这些节点。
  - 如何访问报讯在 NodeList 中的节点：

  ```javascript
  var firstChild = someNode.childNodes[0];
  var secondChild = someNode.childNodes.itme(1); // 也可使用 item()方法
  var count = someNode.childNodes.length; //length属性表示的是访问NodeList的那一刻，其中包含的节点数量
  ```

  - 将 NodeList 转换为数组 ⁉️❓❓
  - `convertToArray()函数` ⁉️❓❓

- parentNode 属性

  - 该属性指向文档中父节点。
  - 列表中的第一个节点的 previousSibling 属性值为 null， 最后一个节点的 nextSibling 属性值也为 null。
  - 父节点与其第一个和最后一个子节点之间的特殊关系： firstChild ， lastChild
  - `hasChildNodes()函数` --在节点包含一个或多个子节点的情况下返回 true；

- ownerDocument 属性

  - 指向表示整个文档的文档节点，通过它可以直接访问文档节点。

### 1.3 操作节点

#### appendChild()方法

- 向 childNodes 列表末尾添加一个节点
- 更新已有节点位置 （更新为在末尾的位置）

#### insertBefore()方法

- 把节点放在 childNodes 列表中某个特定的位置
- 接受 2 个参数： 要插入的节点 ； 作为参考的节点
- 若参照节点为 null，则此方法执行 同 appendChild()方法。
- 返回被插入的节点

#### replaceChild()方法

- 接受 2 个参数： 要插入的节点 ；要替换的节点的节点
- 返回要替换的节点，同时移除它
- 之前节点的关系指针都会复制到插入的这个节点身上
- 被替换掉的节点任然在文档中，但没了自己的位置

#### removeChild()方法

- 接受 1 个参数： 要移除的节点。
- 返回被移除了节点
- 被移除了节点任然留着文档中，只是没了自己的位置

### 1.4 其他方法

所有类型节点都有的：

#### cloneChild()方法

- 创建 调用这个方法的 完全相同的 一个副本
- 接受一个布尔参数：
  - true--执行深拷贝 复制节点 及其 整个子节点 🌲
  - false--执行浅拷贝 只复制节点本身
  - 返回的节点副本是个 **孤儿** 虽然属于文档所有，但没有为他 zhiding 父节点
  - 这个方法，只复制特性和子节点（true 的情况下），其他一切如事件处理程序等都不会复制，
  - IE 有 BUG，建议复制之前，移除事处理程序。

#### normalize()方法

- 处理文档树中的文本节点。
- 在该节点的后代节点上查找到：空白文本节点（会删除它）；相邻文本节点（会将它们合并）

## 2.Document 类型

> JavaScript 通过 DOM 类型表示文档

`document对象` ：

在浏览器中，是 HTMLDocument（继承自 Document 类型）的一个实例，表示整个 HTML 页面。

是 window 对象的一个属性，可作为全局对象来访问。

Document 节点 の **特征**：

- nodeType 的值 9
- nodeName 的值 ""#document"
- nodeValue 的值 null
- parentNode 的值 null
- ownerDocument 的值 null

### 2.1 文档的子节点

子节点可以是 `DocumentType`（最多一个），`Element`（最多一个），`ProcessingInstruction` 或 `Comment`。

- 有两个内置的访问其子节点的快捷方式：

  - documentElement 属性

  ```javascript
  var html = document.documentElement, //  取得对<html>的引用
  alert(html === document.childNodes[0]); //true
  alert(html === document.firstChild);  // true
  ```

  - 通过 childNodes 列表

- body 属性，直接指向<body> 元素

  ```javascript
  var body = document.body; //  取得对<bodyl>的引用
  ```

子节点`DocumentType`

- 通常将<!DOCTYPE>标签看成一个特殊实体，通过 doctype 属性访问

  ```javascript
  var doctype = document.doctype; //  取得对<!DOCTYPE>的引用
  ```

### 2.2 文档信息

#### title 属性

包含<title>元素中的文本

#### URL 属性

包含页面中完整的 URL

#### domain 属性

只包含页面的域名 （**只有 domain 是可设置的**）

#### referrer 属性

连接到当前页面的那个页面的 URL

### 2.3 查找元素

#### getElementById();

- 接收一个参数：要取得的元素的 ID
- 最好不让表单字段的 name 属性与其他元素的 ID 相同。

#### getElementsByTagName();

- 接收一个参数：要取得的元素的标签名

- 返回包含一个或多个元素的 NodeList

- 取得文档中的所有元素---getElementByTagName('\*')

- 在 HTML 中，返回的是一个`HTMLCollection对象`，作为一个“动态”集合

- 访问`HTMLCollection对象`中的项

  ```javascript
  alert(images.length); //输出图像的数量
  alert(images[0].src); //输出第一个图像元素的src属性
  alert(images.item(0).src); //输出第一个图像元素的src属性
  ```

`HTMLCollection对象`

- 通过 length 属性访问元素数量
- `namedItem()方法` ----通过元素 name 特性 取得集合中的项。
- 支持按名称访问项--方括号语法

#### getElementsByName();

- 返回带有给定 name 特性的所有元素

- 最常用--**取得单元按钮**  
  name 特性确保一组按钮中只有一个被发送给浏览器
  id 的作用在于将<label>元素应用到每个单选按钮

  ```js
  <input type="radio" name="color" id="redcolor">
  <input type="radio" name="color" id="greencolor">

  var radios = document.getElementsByName('color');
  ```

### 2.4 特殊集合

- document.anchors
- document.applets
- document.forms
- document.images
- document.links

### 2.5 DOM 一致性检测

documents.implementation 属性

`hasFeature()`方法

- 接受 2 个参数：要检测的 DOM 功能； 版本号

### 2.6 文档写入

> 将输入流写入到网页中的 4 个方法：
>
> write() :接受一个字符串参数，原样写入
>
> writeIn() :接受一个字符串参数，原样写入并再末尾加入换行符\n
>
> open()
>
> close()

## 3.Element 类型

提供了对元素的 标签名、子节点及特性的访问。

**特征**：

- nodeType 的值 1
- nodeName 的值 元素的标签名
- nodeValue 的值 null
- parentNode 可能是 Document 或者 Element

**子节点**可能是---`Element`、`Text`、`Comment`、`ProcessingInstruction`、`CDATASection`、`EntityReference`

**访问元素的标签名**：

- nodeName 属性
- tagName 属性

```html
<div id="myDiv"></div>

<script>
  var div = document.getElementById("myDiv");
  alert(div.tagName); //"DIV"   在HTML中 标签名始终以全部大写表示
  alert(div.tagName == div.nodeName); //true
</script>
```

### 3.1 HTML 元素

HTMLElement 类型直接继承了自 Element 并添加了一些属性。

- id : 元素在文档中的唯一标识符
- title ： 有关元素的附加说明，一般通过工具提示条显示
- lang ： 元素内容的语言代码，少用
- dir ： 语言的方向，值为“ltr” （left-to-right）或“rtl” (right-to-left), 少用
- className ：与元素的 class 特性对应，为元素指定的 CSS 类。

### 3.2 取得特性

> 操作特性的 DOM 方法：
>
> 1. getAttribute()
> 2. setAttribute()
> 3. removeAttribute()

**通常实际开发中，只有在访问自定义属性时，才会用 getAttribute()**

属性值 与 通过 getAttribute()返回的值 不相同的 2 个 特殊特性：

- style
  通过 CSS 为元素制定样式，用 getAttribute()<u>返回的值中包含 CSS 文本</u>
  通过属性来访问，<u>返回一个对象</u>。
- onclick
  通过 getAttribute()访问 返回的值 是<u>相应代码字符串</u>
- 访问属性时，返回一个<u>JavaScript 函数</u>

### 3.3 设置特性

setAttribute() ： --既可以操作 HTML 特性，也可以操作自定义特性

- 接受 2 个参数：要设置的属性名 和 值

removeAttribute()： 不仅会清除特性的值，也会从元素中完全删除该特性

### 3.4 attribute 属性

> Element 属性是使用 attribute 属性的唯一一个 DOM 节点类型。

attribute 属性中，有一个 `NamedNodeMap`， 类似 NodeList，也是一个`动态集合`。

元素的每个特性都由一个`Attr节点`表示，每个节点都保存在`NamedNodeMap对象`中。

`NamedNodeMap对象`の方法：

- getNamedItem(name) ：返回 nodeName 属性等于 name 的节点
- removeNamedItem(name) ：从列表中移除 nodeName 属性等于 name 的节点
- setNamedItem(name) ：向列表中添加节点，以节点的 nodeName 属性为索引
- item(pos) ：返回谓语数字 pos 位置处的节点

```javascript
var id = element.attributes.getNamedItem("id").nodeValue; //获取元素的ID特性
var id = element.attributes["id"].nodeValue; //简写形式

element.attributes["id"].nodeValue = "someOtherId"; // 先获取节点，再赋新值
var oldAttr = element.attributes.removeNamedItem("id"); //删除给定名称的特性，返回被删除特性的Attr节点
element.attributes.setNamedItem(NewAttr); // 添加新特性
```

### 3.5 创建元素

> document.createElement()方法
>
> 只接受 1 个参数--要创建元素标签名
>
> 创建同时，为新元素设置了 ownerDocument 属性

```javascript
var div = document.createElement("div"); //创建新元素div

div.id = "myNewDiv"; // 操作元素属性创建更多子节点
div.className = "box";

document.body.appendChild(div); // 新创建元素添加到body里面
```

### 3.6 元素的子节点

> 元素的 childNodes 属性中包含了他所有的子节点

```js
var ul = document.getElementById("myList");
var items = ulgetElementById("li");
```

## 4.Text 类型

**文本节点**

> nodeType 的值为 3
>
> nodeName 的值为“#text
>
> nodeValue 的值为子节点所包含的文本
>
> parentNode 是一个 Element
>
> 不支持（没有）子节点
>
> 通过 nodeValue 属性或 data 属性访问 Text 节点中包含的文本， 对其修改也通过 data 反映

**操作节点中的文本**

> appendData(text) ：将 text 添加到节点末尾
>
> deleteData(offset, count) ：从 offset 指定位置开始删除 count 个字符
>
> insertData(offset, text) ：在 offset 位置插入 text
>
> replaceData(offset, count, text) ：用 text 替换从 offset 位置开始的 count 个字符
>
> splitText(offset) ：从 offset 位置将当前文本节点分成两个文本节点
>
> substringData(offset, count) 提取从 offset 位置开始的 count 个字符串

```js
var textNode = div.firstchild; //或者 div.childNodes[0];
div.firstChild.nodeValue = "some other message"; //取得并修改文本节点
```

### 4.1 创建文本节点

> document.createTextNode()
>
> 接受一个参数

```js
var element = document.createElement("div"); //创建一个div元素
element.className = "message"; //添加message属性

var textNode = document.createTextNode("hello world!"); //创建一个文本元素
element.appendChild(textNode); //添加到div元素中

document.body.appendChild(element); //将创建的div添加都文档中
```

### 4.2 规范化文本节点

将相邻文本节点合并的方法： `normalize()`

```js
var element = document.createElement("div"); //创建一个div元素
element.className = "message"; //添加message属性

var textNode = document.createTextNode("hello world!"); //创建一个文本元素
element.appendChild(textNode); //添加到div元素中
var anothertextNode = document.createTextNode("Yippee!"); //创建另外一个文本元素
element.appendChild(anothertextNode);

document.body.appendChild(element);

alert(element.childeNode.length); //2

element.normolize();
alert(element.childeNode.length); //1
alert(element.firstChilde.nodeValue); //'hello world!Yippee!
```

### 4.3 分割文本节点

讲一个文本节点分成两个：`splitText()`

```js
var element = document.createElement("div"); //创建一个div元素
element.className = "message"; //添加message属性

var textNode = document.createTextNode("hello world!"); //创建一个文本元素
element.appendChild(textNode); //添加到div元素中

document.body.appendChild(element);

var newNode = element.firstChild.splitText(5);
alert(element.firstChilde.nodeValue); // hello
alert(newNode.nodeValue); // world!
alert(element.childeNode.length); //2
```

## 5.Comment 类型

注释在 DOM 中用 Comment 类型来表示

> nodeType 的值为 8
>
> nodeName 的值为“#Comment"
>
> nodeValue 的值是注释的内容
>
> parentNode 可能是 Document 或者 Element
>
> 不支持（没有）子节点
>
> 和 Text 类型继承自相同的基类，有处 splitText()之外的所有字符串操作方法

## 6.CDATASection 类型

只针对 XML 的文档，表示的是 CDATA 区域

> nodeType 的值为 4
>
> nodeName 的值为“#cdata-section"
>
> nodeValue 的值是 CDATA 区域中的内容
>
> parentNode 可能是 Document 或者 Element
>
> 不支持（没有）子节点

## 7.DocumentType 类型

包含着与文档的 doctype 有关的所有信息

> nodeType 的值为 10
>
> nodeName 的值为 doctype 名称
>
> nodeValue 的值 null
>
> parentNode 是 Document
>
> 不支持（没有）子节点

## 8.DocumentFragment 类型

文档片段类型，没有对应的标记，可以包含和控制节点，不占用额外资源

> nodeType 的值为 11
>
> nodeName 的值为“#document-fragment"
>
> nodeValue 的值 null
>
> parentNode 是 null
>
> 子节点可以是 Element、processingInstruction、Comment、Text、CDATASection、EntityReference
>
> 不能把文档片段直接添加到文档中，但可以当做仓库使用，保存将来可能会添加到文档中的节点--通过 appendChild()或 insertBefore()

```js
var fragment = document.createDocumentFragment();
var ul = document.getElementById("mylist");
var li = null;

for (var i = 0; i < 3; i++) {
  li = document.createElement("li");
  li.appendChild(document.createTextNode("Item" + (i + 1)));
  fragment.appendChild(li);
}
ul.appendChild(fragment);
```

## 9.Attract 类型

表示元素的特性

> nodeType 的值为 2
>
> nodeName 的值为特性的名称
>
> nodeValue 的值是特性的的值
>
> parentNode 是 null
>
> 不支持（没有）子节点
>
> 不被认为是 DOM 文档树的一部分
>
> 有 3 个属性： name, value, specified

# DOM 操作技术

## 1.动态脚本

在页面加载时不存在，但将来的某一时刻通过修改 DOM 动态添加的脚本。

### 插入外部文件

### 插入 JavaScript 代码

## 2.动态样式

在页面刚加载时不存在的样式；在页面加载完成后动态加到页面中

### 插入外部文件

### 嵌入式

## 3.操作表格

## 4.使用 NodeList

`NodeList` `NamedNodeMap` `HTMLCollection` ----动态的

所有 NodeList 对象都是在访问 DOM 文档是实时运行的查询。

---

DOM 扩展

# 选择符 API

## 1. querySelector( )方法

接受一个 CSS 选择符，返回与其匹配的第一个元素，如果没有，返回 null

## 2. querySelectorAll( )方法

接受一个 CSS 选择符，返回一个 NodeList 实例，如果没有，NodeList 为空

要取得返回的 NodeList 元素，可以使用 item( )方法，也可使用方括号[ ]

## 3. matchesSelector( )方法

接受一个 CSS 选择符, 若调用元素匹配该选择符，返回 true，否则返回 false

# 元素遍历

- childElementCount：返回子元素的个数（不包括文本节点和注释）
- firstElementChild：指向第一个子元素；firstChild 的元素版
- lastElementChild：指向最后一个子元素；lastChild 的元素版
- previousElementSibling: 指向前一个同辈元素，previousSibling 的元素版
- nextElementSIbling: 指向后一个同辈元素，nextSibling 的元素版

# HTML5

## 1. 与类相关的扩充

### 1.1 getElementsByClassName( )方法

接受一个参数（一个包含一或多个类名的字符串），返回相应的所有元素的 NodeList

传入多个类名是，先后顺序不重要

### 1.2 classList 属性

在操作类名时，通过 className 属性添加、删除、替换类名。因为 className 中是一个字符串，多以即使只修改字符串的一部分，也必须每次都设置整个字符串的值。

而 classList 属性是新集合类型 DOMTokenList 的事例

> add(value) ：添加字符串
>
> contains(value)：字符串是否存在于列表中，返回 true 或 false
>
> remove(value)：删除字符串
>
> toggle(value)：切换字符串，有的话删除，没有的话添加

## 2. 焦点管理

### 2.1 document.activeElement 属性

-----始终会引用 DOM 中当前获得了焦点的元素。

元素获得焦点的方式：页面加载、用户输入、调用 focus()方法

### 2.2 document.hasFocus()方法

-----确定文档是否获得了焦点，可以知道用户是否在与页面交互

## 3. HTMLDocument 的变化

### 3.1 readyState 属性

两个可能的值：

> loading：正在加载文档
>
> complete：已经加载完成文档

### 3.2 兼容模式

### 3.3 head 属性

引用文档的<head>元素

## 4. 字符集属性

### 4.1 charset 属性

表示文档中实际使用的字符集；也可以用来指定新字符集；默认 UTF-16

我们用 UTF-8

### 4.2 defaultCharset 属性

表示根据默认浏览器及操作系统的设置，当前文档默认的字符集应该是什么。

## 5. 自定义数据属性

要添加前缀 `data-`

可以通过元素的 dataset 属性访问，他是 DOMStringMap 的一个实例

## 6. 插入标记

### 6.1 innerHTM 属性

### 6.2 outerHTML 属性

### 6.3 insertAdjacentHTML( )方法

接受 2 个参数：插入的位置；要插入的 HTML 文本

> ”beforebegin“： 在前面插入一个同辈元素
>
> ”afterbegin“ ：在当前元素之下插入一个新的子元素 或 在第一个子元素之前再插入一个新元素
>
> ”beforeend“：在当前元素之下插入一个新的子元素 或 在最后一个子元素之后再插入一个新元素
>
> ”afterend“：在后面插入一个同辈元素

### 6.4 内存与性能问题

## 7. scrollIntoView()方法

# 专有扩展

## 1. 文档模式

## 2. children 属性

因处理空白符的时候有差异而来，是 HTMLCollection 的一个实例，只包含元素中同样还是元素的字节点

除此之外，与 childNodes 没啥区别

## 3. contains()方法

需要知道某个节点是不是另一个节点的后代

调用它的是祖先节点，接受一个参数，要检测的后代节点，返回 true 或 false

## 4. 插入文本

### 4.1 innerText 属性

操作元素中包含的所有文本，包括子文档树中的文本，按照由浅入深的顺序读取

### 4.2 outerText 属性

同上，但作用范围扩大到了包含调用它的节点

## 5. 滚动
