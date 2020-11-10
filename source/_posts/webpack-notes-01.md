---
title: webpack_notes_01
date: 2018-11-09 14:57:35
updated: 2018-11-09 14:57:35
tags: webpack
categories: tech-share
keywords: webpack
description:
top_img: false
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfmcg2vm3j30ts0m11kx.jpg
---

常用功能：

- 压缩
- 打包
- 多种文件编译
- 脚手架
- 生成

安装：npm i webpack-cli -g

### webpack.config.js

1. mode:

   none：不优化

   development：输出调试信息，设置 process.evn.MODE.ENV

   production： 最高优化，启用压缩，忽略错误

2. entry 入口

   单入口--SPA

   多入口--MPA

3. output 出口

   ```
   { path：输出路径（使用绝对路径--path.resolve）
     filename：
   }
   ```

loader 翻译

plugin 插件

---

### 常见工程文件的结构：

- search.js：封装搜索框

- toast.js：挑出 toast 提示

- http：
  - xhr.js 低版本网络请求，用原生 Ajax
  - fetch.js 高版本网络请求
  - other.js 其他请求
  - index.js 多个文件中的模块功能 （作导出用）

---

### 作用域 ：

决定了代码中变量和其他资源的**<u>可见性</u>** （全局；局部）

↓

命名空间：

解决全局作用域中，不同模块引用相同名字的问题

```js
Var SusanModule = (function(){
	var name = 'Susan';
	var sex = 'female';
	return {
    tell: function(){
      console.log('my name is' + this.name)
      console.log('my sex is' + this.sex)
    }
  }
})();


//早期标准模块的写法：↓

(function(window){
  var name = 'Susan';
	var sex = 'female';
  function tell(){
    console.log('my name is' + this.name)
    console.log('my sex is' + this.sex)
  }
  window.SusanModule={tell}
})(window);
```

上述代码中，我们只能拿到 return 里的返回值--tell 方法，而无法访问其他数据，如 name 和 sex，→ 这样能隐藏数据，只暴露出该有的部分。

↓

函数作用域会帮助我们保护在函数里（模块里）被定义的变量，这样的小范围作用域，可看成是模块作用域~

↓

这样一来，里面的变量就是安全的，且这个功能是可重用的

---

### 模块化的优点：

- 作用域封装
- 重用性
- 接触耦合

模块化进化史：

- AMP （异步模块定义）

  ↓

- COMMON JS

  ↓

- ES6 MODULE

AMD 定义模块：

```js
      id         依赖        函数/对象
define('getSum', ['math'], function(math){
return function(a,b) {
 console.log('sum'+math.sum(a,b))
}
})
```

显式地表达出模块的依赖

COMMON JS 定义模块：

```js
const math = require("./math");
exports.getSum = function (a, b) {
  return a + b;
};
```

每个文件就是一个模块，有自己的作用域和 context

模块依赖通过 require 函数引入

限时引入，强调依赖，不用担心引入顺序问题

ES6 MODULE 定义模块：

```js
import math from "./math";
export function sum(a, b) {
  return a + b;
}
```

---

### webpack 打包机制

立即执行函数：

大体结构：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjgbqr3cvj30kg05a0t6.jpg" style="zoom:150%;" />

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjg83n2rrj30lb023dfx.jpg" style="zoom:150%;" />

核心方法：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjgi2w54vj30me0eztas.jpg" style="zoom:150%;" />

---

### 打包过程

从入口文件开始，分析整个应用的依赖树 🌲

​ ↓

将每个依赖模块包装，放到数组中等待调用

​ ↓

实现模块加载方法，并把它放到模块执行函数中

确保模块之间可以相互调用

​ ↓

把执行入口文件的逻辑放在一个函数表达式中，立即执行

​

---

#### script 脚本命令

- npm 自己的生命周期命令：可发着写的钩子（如 preinstall, postinstall, prepublish, postpublish 等……）
- 自定义的命令：
  - "dev": "webpack-dev-server" 在 npm run dev 时原地启动， 这是个开发服务器
  - "build": "eslint ./src && webpack" 在 npm run build 是，对代码格式化校验，且执行 webpack

---

#### node_modules 先删除再重建的隐患

执行 rm -rf node_modules && npm install

↓ 隐患

npm install の过程：

1. 寻找包版本信息文件（package.json）,依赖它进行安装
2. 查 package 中的依赖，并检查项目中其他版本文件
3. 如果发现新的包，更新版本信息文件

---
