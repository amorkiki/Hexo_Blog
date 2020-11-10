---
title: 依然webpack
date: 2019-04-18 13:09:42
tags: webpack
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfm7d4mnbj30u00k041c.jpg
---

Jin 天想扣一扣 webpack 的 loader 和 plugin，以及 babel 编译的一些学习心得~
好长时间没更新啦~坚持学习 喵~

---

# loader

webpack 只能处理 js 文件，其他的文件形式，需要 loader

👇

loader：翻译、管道，会把一个东西转成另一种东西

# plugin

插件，不是负责给 webpack 提供东西的，只处理额外的工作，独立与 webpack 之外，它的成功也不用跟 webpack 反馈，如校验之类的工作，与 webpack 程序平行工作

plugin：辅助 webpack 工作的，它的结果不给 webpack

---

# loader 怎么用

> css 编译：
>
> npm i css-loader style-loader -D (既能读进来处理好，也能显示到页面)
>
> css-loader：css 打包，以及处理 css 当中的一些引入，如@import
>
> style-loader：css 打包后还需要渲染到页面

## loader 配置：

### 1.单 loader

```json
{ "test": / /, "use": "xxx-loader" }
```

### 2.多个 loader

```json
{ "test": / /, "use": ["xxx-loader", "xxx-loader"] }
```

### 3.loader+参数

```json
{
  "test": / /,
  "use": {
    "loader": "xx-loader",
    "options": {
      "a": "",
      "b": ""
    }
  }
}
```

### 4.多 loader+参数

```json
{test: / /, use: [
  {
    loader: 'xxx-loader',
    options: ...
  }, {
    loader: 'xxx2-loader',
    options: xxx
  }
]}


{test: / /, use: [
  'xxx-loader', {
    loader: 'xxx2-loader',
    options: xxx
  }
]}
```

---

> 图片编译
>
> file-loader 读取文件、不做处理、直接输出，保证通用性
> url-loader 读取文件、把文件的内容转换成 base64，打包到 bundle 里面
>
> 👆 两个东西要一起装
>
> base64： 1.优点——减少请求 2.缺点——略微增加文件体积
> 1.3K -> 1.5K
> 10%~20%
>
> ```json
> {
> test: /\.(jpg|png|gif|webp|ico)$/i,
> use: {
>  loader: "file-loader",
>  options: {
>    outputPath: "imgs/",
>  },
> },
> },
> ```
>
> ```json
> {
> test: /\.(jpg|png|gif|webp|ico)$/i,
> use: {
>  loader: "url-loader",
>  options: {
>    // 大于limit的文件会直接pass，以原样输出
>    // 加入限制是8k
>    limit: 8 * 1024,
>    outputPath: "imgs/",
>  },
> },
> },
> ```
>
> 引发问题：路径会出问题~
>
> file-loader 和 url-loader 处理同一张图片（通过改变 limit 限制），会出来不同的路径
>
> 👇
>
> publicPath：将来文件发布了之后，文件路径会以什么为基准，就是一个字符串链接，类似 baseURL
>
> ![](https://tva1.sinaimg.cn/large/007S8ZIlly1gisvz0kq8rj30fp03tgls.jpg)
>
> ![](https://tva1.sinaimg.cn/large/007S8ZIlly1gisvz83wivj30k503vjrl.jpg)
>
> <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1gisw06b3psj30c1049mx8.jpg" style="zoom:150%;" />
>
> publicPath 在开发时和发布时也不同哦~

---

压缩图片 image-loader

> less 编译
>
> ```js
> //less
> { test: /\.less$/,
> use: ['style-loader', 'css-loader', 'less-loader']
> },
> ```

---

# Vue-loader

npm i <u>vue-loader</u> <u>webpack webpack-cli</u> <u>vue-style-loader</u> <u>vue-html-loader</u> <u>vue-template-compile</u> -D

- vue-loader：编译 vue

- webpack webpack-cli：编译 vue 必备

- vue-style-loader：编译 vue 里的样式，style-loader 的一个子集

- vue-html-loader：编译 vue 文件里的 template 里的 html 的一些引用，如 scr 引入图片之类的

- vue-template-compile：编译 vue 模板的，里面有些花括号之类的

  作用：① 在编译模板的时候发现模板的问题 ② 预编译方式提高性能

**预编译**：打包时就已经编译完了----提前发现问题，提升性能~

**运行时编译**：到了浏览器之后再编译

---

```js
//webpack.config.js

const VueLoaderPlugin = require("vue-loader/lib/plugin");   //②从vue-loader引入这个插件
-------

 module: {
 	// VUE编译
      { test: /\.vue$/, use: "vue-loader" },                //①安装vue-loader后引入这个loader
 }
-------
  //VueLoaderPlugin是用来做模板编译的，做了vue-loader的一部分工作
plugins: [new VueLoaderPlugin()]                             //③new一下才能用
```

```js
import Vue from "vue";-->??这里直接引vue不行，因为vue只是浏览器运行时的库，没有ECMAScript模块ES6的库

import "./cmp1.vue";

let vm = new Vue({
  el: "#root",
});
```

引 vue，会报错 👇

![](https://tva1.sinaimg.cn/large/007S8ZIlly1giup2z0htjj30n4020glv.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gisx1noc8tj30a20bymxl.jpg)

这里的 vue.esm.js 是 ECMAScript 的 module，ES 模块的 VUE，

现在 import 一个 VUE，要保证那边有 exportVUE，所以我们这里不能引 import Vue from "vue";

```js
//index.js

import Vue from "vue/dist/vue.esm";

import "./cmp1.vue";  --->这种引入实际上等同于 cmp1 = vue.extend 这种局部写法，所以要用components
                        //👇
let vm = new Vue({
  el: "#root",          //👇
  components: {    // 👈
    cmp1,
  },
  template:`<cmp1></cmp1>`      //着这里要添加template把cmp1输出去，为了不让VUE与thml混在一起
});

```

---

此时，远远不够，如果出现 async 异步操作，就无法往下进行，

so

👇

---

# babel 编译

把特别高版本的语言编译给较低版本的浏览器~

npm i babel-loader @babel/core @babel/preset-env -D

@babel/preset-env 是一个预设，能根据需求选择配置

```js
//js
{test: /\.jsx?$/i, use: {
  loader: 'babel-loader',
  options: {
    presets: ['@babel/preset-env']
    // presets: [
    //   ['@babel/preset-env', {
    //     targets: {
    //       chrome: 58,
    //       ie: 7
    //     }
    //   }]
    // ]
   }
 }
}
```

👇

加了 babel 之后，遇到 async 会报错

![](https://tva1.sinaimg.cn/large/007S8ZIlly1giupk3hvf4j30hn06675c.jpg)

因为 babel 是向下兼容的，async 是 ES7 语言，babel 会用 regeneraterRuntime 把他编译成低版本的回调函数 --->polyfill 登场

babel -> async -> regenerator （这时候要求有 polyfill 在才能实现上述过程）

`polyfill`——是一个库，补充 js 的功能

npm i @babel/polyfill -S

他是一个 js 文件，写在 entry 里

```js
···

module.exports={
  mode: 'development',
  entry: ['@babel/polyfill', './src/index']

  ···

}
```

思路：

1.babel——降级（兼容低版本）

👇

2.async 函数

👇

3.polyfill
