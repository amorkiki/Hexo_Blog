---
title: webpack_notes_02
date: 2020-08-09 17:10:46
tags: webpack
---
### webpack的核心特性

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84451wj30fh0att9j.jpg)



若在src文件夹下添加自定义入口文件app.js，则需要手动创建 webpack.config.js文件

👇

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjhfeyhjaj30kh0h30u2.jpg" style="zoom:150%;" />



--------

注⁉️‼️：

- webpack安装后，要在modules文件夹下检查是否有webpack和webpack-cli ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko88khn9j305o01pwea.jpg)



- 如果执行webpack命令是没反应，可考虑在script中添加脚本： "_dev": "webpack"![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko875v27j30ey03274e.jpg)

- 或者重新做一下初始化 npm init -y 看下package.json里的显示有webpack和webpack-cli之后再npm run dev

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko85bfukj30bk04iaae.jpg)

---------



 webpack.config.js文件代码展示：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjhs6wkekj30kd0hn0uj.jpg" style="zoom:150%;" />



-------------

### babel--编译ES6代码

当需要的代码和写出的代码不一致时，需要babel对代码进行编译，高版本→低版本

安装：sudo npm install @babel/core @babel/cli -g

↓

安装转换规则 npm install @babel/preset-env  (用来把高版本ES转成低版本的ES，专门用来编译ES6的)

↓

写入规则：babel test.js --preset=@babel/preset-env

(若报错，可能是 @babel/core版本过高，卸载重装，npm uninstall @babel-core)

↓

@babel设置配置文件：（在package.json里  :

"babel" : {

​	"presets" : ["@babel/preset-env"]

}

↓

还可通过 .babelrc 这个专用的来配置文件（优先级最高）

{

​	"presets" : ["@babel/preset-env"]

}



----------

## webpack构建react工程の案例

> - 掌握babel用法，理解其原理
> - 掌握高频loader和plugin用法
> - 生产级别的webpack配置方法
> - webpack性能调优

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko85ud8aj305m0973yn.jpg)

在新建项目夹下：利用 npm init -y初始化

↓

安装依赖 ：react 和react dom

↓

安装webpack

（npm install webpack webpack-cli -g    在全局下安装

​			tips：若没有安装全局，那么webpack只存在于modules那个文件夹内，只有在该文件夹下的命令行内才能调用，所以必须安装全局先，无法安装 加sudo）

↓

新建src文件夹下的 app.jsx （他使用ES6语法，不能直接放webpack）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84bwz3j30es06twer.jpg)

↓

新建src文件夹下的index.html，将 app.jsx引入![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko886i6aj30a402z748.jpg)

↓

以loader形式运用babel编译

（在webpack.config.js里写入：module.exports ={ }

↓

安装babel-loader： npm install babel-loader

↓

设置babel规则： 需要 ES6转换的preset-env 和jsx转换的preset-react

（npm install @babel/preset-env @babel/preset-react)

↓

在module.exports ={ }里的module对象中的rules数组中的use对象里的options中写入规则<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjikfskr3j30uj0c8gne.jpg"  />

↓

安装plugin来处理index.html (这是一个节点纬度的处理)

↓

将plugins下载module下面：new一下这个构造函数，使用绝对路径

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjitfvipgj30lv07bdgl.jpg" style="zoom:150%;" />

↓

（resolves是专门用来声明棉鞋文件后缀的关键字，他是一个数组，里面是指定文件的后缀）

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjiuifcfnj30ft022q2w.jpg" style="zoom:150%;" />

↓

plugin往往通过构造函数存在，用之前const引入一下，之后再module。exportsxia d plgins里new一下

（他的两个入参： template（路径） 和 filename（文件名）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko87r7vjj30m0035mxp.jpg)



↓

再回到app.jsx有文件内，发现只有export没有import

↓

建立index.jsx引入app.jsx

（前面已经在modules.export里的resolve字段下的extensions指定几个后缀，这里引入的文件不用写后缀了）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko869ug9j30f70aiwf3.jpg)

↓

在命令行执行打包，webpack --mode development

↓

查看打包效果，是否生产dist文件夹下的main.js  和 index.html

↓

webpack-dev-server  --config          --config   可以指定路径

webpack-dev-server  --open             --open可以直接打开浏览器

这些命令都可在script中自定义，然后用npm run 来执行

↓

注： dev-server只能在浏览器刷新后再跟新页面

↓

匀后HMR热跟新（热替换）：不刷新页面就能跟新

（引入webpack，在plugins里面写 new webpack.HotModuleReplacementPlugin()

​     再写入 DevServer字段下 hot: true )

​    devServer: {

​        //HMR

​        hot: true

​    }

↓

在index.jsx里写一个判断热替换是否存在的命令

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghko84xnbqj30b104m3yk.jpg)

↓

最后执行npm run start , 更新jsx文件后，发现打包成功

----------------

## webpack性能调优

> 1. 打包结果优化 （空间纬度：体积小，速度快）
> 2. 构建过程优化 （时间维度：迅速）
> 3. tree-shaking优化思想



1. webpack定制压缩工具：

   引入进行插件定制：const TerserPlugin = require('terser-webpack-plugin');

   ↓

   module.exports 下有个optimization专门用来存放压缩工具，在其中进行配置

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjd8pkp5j30hn09waaz.jpg" style="zoom:150%;" />

   

   ↓

打包结果可视化方法： webpack分析器

（webpack bundle Analyzer 插件）

↓

用npm 下载后，在plugins里面new 一下

const BundleAnalyZerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

new BundleAnalyZerPlugin(),

↓

npm run build 一下 看到新页面的展示





- 构建过程与构建体积的优化往往耦合在一起

  思路： 找出耗时间的步骤-----→==解析==

  ↓

  可以在module里配个noParse

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjgxu352j30ku020t8t.jpg" style="zoom:150%;" />

  ↓

  -----→==查找== ：在rules里除了可以用exclude外，还可运用include，进一步提高针对性

  ( 优先级：exclude > inclue >test )

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghjjjgx1vpj30ey03zdg4.jpg" style="zoom:150%;" />

  ↓

  -----→==利用多线程==   ：HappyPack

  （nodejs是单线程，基于此的webpack也是，所以可利用：

                  - HappyPack插件

     - thread-loader

  ↓

  <u>HappyPack插件:</u>  

  手动引入happypack和线程池happyThreadPool → 
          const HappyPack = require('happypack');
          const happyThreadPool = HappyPack.ThreadPool({ size: OscillatorNode.cups().length })

  ​         → 在plugins里new一下

  new HappyPack({
      id: 'jsx',
      threads: happyThreadPool,
      loaders: ['babel-loaders'] //给babel配置个happypack，前提是这个loader支持happypack
  })

  注：url-loader  和 file-loader都不支持


  ↓

  <u>thread-loader</u>：是针对loader进行的，他讲提放在一个worker的运行池里，达到多线程构件的目的。

  使用时，放在多有loader而之前


3. tree-shaking 思想：消除无用的js代码（DCE)
   - ①webpack会分析 modules引入的情况，去除不实用的import引入
   - ②借助工具，对无用模块进行删除
     实例：util.js工具库中导出函数
     结论：在mode development下webpack只是在结果中删除了无用函数，其他部分依然保留
                   在mode production下，webpack会完全删除无用函数


