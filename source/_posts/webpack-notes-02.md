---
title: webpack_notes_02
date: 2018-11-13 17:10:46
tags: webpack
categories: tech-share
keywords: webpack
description:
top_img: false
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfmcweqxlj317q0u0n2f.jpg
---

### webpack 的核心特性

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84451wj30fh0att9j.jpg)

若在 src 文件夹下添加自定义入口文件 app.js，则需要手动创建 webpack.config.js 文件

👇

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjhfeyhjaj30kh0h30u2.jpg" style="zoom:150%;" />

---

注⁉️‼️：

- webpack 安装后，要在 modules 文件夹下检查是否有 webpack 和 webpack-cli ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko88khn9j305o01pwea.jpg)

- 如果执行 webpack 命令是没反应，可考虑在 script 中添加脚本： "\_dev": "webpack"![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko875v27j30ey03274e.jpg)

- 或者重新做一下初始化 npm init -y 看下 package.json 里的显示有 webpack 和 webpack-cli 之后再 npm run dev

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko85bfukj30bk04iaae.jpg)

---

webpack.config.js 文件代码展示：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjhs6wkekj30kd0hn0uj.jpg" style="zoom:150%;" />

---

### babel--编译 ES6 代码

当需要的代码和写出的代码不一致时，需要 babel 对代码进行编译，高版本 → 低版本

安装：sudo npm install @babel/core @babel/cli -g

↓

安装转换规则 npm install @babel/preset-env (用来把高版本 ES 转成低版本的 ES，专门用来编译 ES6 的)

↓

写入规则：babel test.js --preset=@babel/preset-env

(若报错，可能是 @babel/core 版本过高，卸载重装，npm uninstall @babel-core)

↓

@babel 设置配置文件：（在 package.json 里 :

"babel" : {

​ "presets" : ["@babel/preset-env"]

}

↓

还可通过 .babelrc 这个专用的来配置文件（优先级最高）

{

​ "presets" : ["@babel/preset-env"]

}

---

## webpack 构建 react 工程の案例

> - 掌握 babel 用法，理解其原理
> - 掌握高频 loader 和 plugin 用法
> - 生产级别的 webpack 配置方法
> - webpack 性能调优

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko85ud8aj305m0973yn.jpg)

在新建项目夹下：利用 npm init -y 初始化

↓

安装依赖 ：react 和 react dom

↓

安装 webpack

（npm install webpack webpack-cli -g 在全局下安装

​ tips：若没有安装全局，那么 webpack 只存在于 modules 那个文件夹内，只有在该文件夹下的命令行内才能调用，所以必须安装全局先，无法安装 加 sudo）

↓

新建 src 文件夹下的 app.jsx （他使用 ES6 语法，不能直接放 webpack）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84bwz3j30es06twer.jpg)

↓

新建 src 文件夹下的 index.html，将 app.jsx 引入![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko886i6aj30a402z748.jpg)

↓

以 loader 形式运用 babel 编译

（在 webpack.config.js 里写入：module.exports ={ }

↓

安装 babel-loader： npm install babel-loader

↓

设置 babel 规则： 需要 ES6 转换的 preset-env 和 jsx 转换的 preset-react

（npm install @babel/preset-env @babel/preset-react)

↓

在 module.exports ={ }里的 module 对象中的 rules 数组中的 use 对象里的 options 中写入规则<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjikfskr3j30uj0c8gne.jpg"  />

↓

安装 plugin 来处理 index.html (这是一个节点纬度的处理)

↓

将 plugins 下载 module 下面：new 一下这个构造函数，使用绝对路径

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjitfvipgj30lv07bdgl.jpg" style="zoom:150%;" />

↓

（resolves 是专门用来声明棉鞋文件后缀的关键字，他是一个数组，里面是指定文件的后缀）

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjiuifcfnj30ft022q2w.jpg" style="zoom:150%;" />

↓

plugin 往往通过构造函数存在，用之前 const 引入一下，之后再 module。exportsxia d plgins 里 new 一下

（他的两个入参： template（路径） 和 filename（文件名）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko87r7vjj30m0035mxp.jpg)

↓

再回到 app.jsx 有文件内，发现只有 export 没有 import

↓

建立 index.jsx 引入 app.jsx

（前面已经在 modules.export 里的 resolve 字段下的 extensions 指定几个后缀，这里引入的文件不用写后缀了）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko869ug9j30f70aiwf3.jpg)

↓

在命令行执行打包，webpack --mode development

↓

查看打包效果，是否生产 dist 文件夹下的 main.js 和 index.html

↓

webpack-dev-server --config --config 可以指定路径

webpack-dev-server --open --open 可以直接打开浏览器

这些命令都可在 script 中自定义，然后用 npm run 来执行

↓

注： dev-server 只能在浏览器刷新后再跟新页面

↓

匀后 HMR 热跟新（热替换）：不刷新页面就能跟新

（引入 webpack，在 plugins 里面写 new webpack.HotModuleReplacementPlugin()

​ 再写入 DevServer 字段下 hot: true )

​ devServer: {

​ //HMR

​ hot: true

​ }

↓

在 index.jsx 里写一个判断热替换是否存在的命令

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84xnbqj30b104m3yk.jpg)

↓

最后执行 npm run start , 更新 jsx 文件后，发现打包成功

---

## webpack 性能调优

> 1. 打包结果优化 （空间纬度：体积小，速度快）
> 2. 构建过程优化 （时间维度：迅速）
> 3. tree-shaking 优化思想

1. webpack 定制压缩工具：

   引入进行插件定制：const TerserPlugin = require('terser-webpack-plugin');

   ↓

   module.exports 下有个 optimization 专门用来存放压缩工具，在其中进行配置

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjd8pkp5j30hn09waaz.jpg" style="zoom:150%;" />

   ↓

打包结果可视化方法： webpack 分析器

（webpack bundle Analyzer 插件）

↓

用 npm 下载后，在 plugins 里面 new 一下

const BundleAnalyZerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

new BundleAnalyZerPlugin(),

↓

npm run build 一下 看到新页面的展示

- 构建过程与构建体积的优化往往耦合在一起

  思路： 找出耗时间的步骤-----→==解析==

  ↓

  可以在 module 里配个 noParse

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjgxu352j30ku020t8t.jpg" style="zoom:150%;" />

  ↓

  -----→==查找== ：在 rules 里除了可以用 exclude 外，还可运用 include，进一步提高针对性

  ( 优先级：exclude > inclue >test )

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjjgx1vpj30ey03zdg4.jpg" style="zoom:150%;" />

  ↓

  -----→==利用多线程== ：HappyPack

  （nodejs 是单线程，基于此的 webpack 也是，所以可利用：

                  - HappyPack插件

  - thread-loader

  ↓

  <u>HappyPack 插件:</u>

  手动引入 happypack 和线程池 happyThreadPool →
  const HappyPack = require('happypack');
  const happyThreadPool = HappyPack.ThreadPool({ size: OscillatorNode.cups().length })

  ​ → 在 plugins 里 new 一下

  new HappyPack({
  id: 'jsx',
  threads: happyThreadPool,
  loaders: ['babel-loaders'] //给 babel 配置个 happypack，前提是这个 loader 支持 happypack
  })

  注：url-loader 和 file-loader 都不支持

  ↓

  <u>thread-loader</u>：是针对 loader 进行的，他讲提放在一个 worker 的运行池里，达到多线程构件的目的。

  使用时，放在多有 loader 而之前

3. tree-shaking 思想：消除无用的 js 代码（DCE)
   - ①webpack 会分析 modules 引入的情况，去除不实用的 import 引入
   - ② 借助工具，对无用模块进行删除
     实例：util.js 工具库中导出函数
     结论：在 mode development 下 webpack 只是在结果中删除了无用函数，其他部分依然保留
     在 mode production 下，webpack 会完全删除无用函数
