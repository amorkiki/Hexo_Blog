---
title: vue_shop--login branch
date: 2020-08-16 18:31:07
tags: vue
---


## 前端项目脚手架初始化 （略略略👅）

## 后台项目的环境安装配置

> 1 安装 MySQL 数据库 --给予XAMMP
>
> 2 安装 Node.js 环境 ✅
>
> 3 配置项目相关信息 
>
> 4 启动项目
>
> 5 使用 Postman 测试后台项目接口是否正常
>
> - 在api文件夹中安装依赖包
> - 用node命令执行app.js
> - 查看api接口文档
> - 基于登录接口，测试接口是否正常工作：
>   - 安装postman
>   - 输入要测试的请求方式，地址以及其他参数可在body里设置，
>   - 点击send



> 用XAMMP导入给予的SQL文件：
>
> - 启动XAMMP，打开network
> - http://localhost:8080/phpmyadmin 进入myadmin
> - 创建新数据库vue_shop
> - 导入sql文件

--------------------------------------------------

### 登录业务的相关技术点noted~🤭

1. http 是无状态的
2. 通过 cookie 在客户端记录状态
3. 通过 session 在服务器端记录状态  
4. 通过 token 方式维持状态

> - localStorage：持久化存储
> - sessionStorage：会话时存储

--------------------------------------------------

### 🧠操作要点步骤分析：

#### 登录页面布局注意点：

- 在项目文件夹下写代码之前，先git status一下，看当前工作区是否干净

- > 登录组件布局：
  >
  > - 背景颜色 在根盒子div中添加class名，在下面style中写相应的class
  >
  > - 添加后页面出现报错Can't resolve 'less-loader
  >
  > - 因为要使用less必须先配置less-loader
  >
  >   - 在依赖中安装less-loader在开发依赖中
  >   - 又因为less-loader是在less下执行的，再安装一下less
  >
  > - 运行后发现背景色并没有铺满页面，控制台中检查发现是HTML元素没有铺满全屏
  >
  > - 修改相应元素的样式，使得其铺满全屏
  >
  >   - 在src下的assets里新建css文件夹，在其中建立global.css群居样式表--（静态文件添加
  >
  >   - ```css
  >     /* 全局样式表 */
  >     html,
  >     body,
  >     #app{
  >         height: 100%;
  >         margin: 0;
  >         padding: 0;
  >     } 
  >     ```
  >
  >   - 导入全局样式表到main.js
  >
  >   - 页面中查看效果，发现HTML和body等元素已经撑满屏幕
  >
  > - 找到设置背景的div盒子，也让他撑满屏幕（这个在Login.vue中写哦~~）



> - 密码框和用户名前的小图标
>
>   - 在el-input里添加    prefix-icon="el-icon-search" 
>
> - 图标跟换：通过第三方图标库--阿里图标库
>
>   - 使用步骤如下：
>
>     第一步：引入项目下面生成的fontclass代码：
>
>     ```html
>     <link rel="stylesheet" type="text/css" href="./iconfont.css">
>     ```
>
>     第二步：挑选相应图标并获取类名，应用于页面：
>
>     ```css
>     <i class="iconfont icon-xxx"></i>
>     ```
>
>     > "iconfont"是你项目下的font-family。可以通过编辑项目查看，默认是"iconfont"。
>
> - 将fonts文件夹放入assets里
>
> - 在入口文件中导入字体图标  main.js
>
>   ```js
>   import './assets/fonts/iconfont.css'
>   ```
>
> - 组件中添加icon



> - 登录前的预校验
>
>   - Form Methods里有个validate函数，接受一个callback回调，回调函数会在校验结束后被调用（成功或失败）
>
>   - 拿到表单引用对象, 绑定登录按钮的单击事件login，在methods里添加事件login
>
>   - validate函数接收的回调函数用箭头函数，返回的valid值时布尔值
>
>     ```js
>     login () {
>       this.$refs.loginFormRef.validate(valid => {
>         console.log(valid)
>       })
>     }
>     ```



-------------

### 实现登录

#### 🧛‍♀️axios包实现原理分析~

> - 根据验证结果决定是否发具体的登录请求
>
>   - 接着上面的login的箭头函数，添加一个if判断
>
>     ```js
>     //如果不通过，直接return,不发起请求
>     if(!valid) return;
>     ```
>
>   - 如果valid是true，则可以发起登录请求---需要全局配置axios包
>
>     - 在main.js里全局引入 import axios from 'axios'
>     - 将这个包挂载到vue的原型对象上，Vue.prototype.$http = axios，
>     - 这样每个vue组件都可以通过this访问$http, 从而去发起ajax请求
>     - 在给axios设置请求根路径：axios.defaults.baseURL = '接口的地址'
>
>     ```js
>     // 导入axios包
>     import axios from 'axios'
>     // 配置请求的根路径
>     axios.defaults.baseURL = 'https://api.naccl.top/vue/shop/api/private/v1/'
>     // 将axios挂在到Vue原型上
>     Vue.prototype.$http = axios
>     ```
>
>   - 回到Login.vue组件，在箭头函数中通过this访问到原型上的$http
>
>     ```js
>     const result = this.$http.post('login', loginForm)
>     console.log(result)
>     //点击登录后，在控制台拿到一个promise对象
>     //若果某个方法返回的是promise，可以用async和await简化promise操作
>     ↓
>     ↓
>     login () {
>       this.$refs.loginFormRef.validate(async valid => {
>         // console.log(valid)
>         if (!valid) return
>         const result = await this.$http.post('login', this.loginForm)
>         console.log(result)
>       })
>     }
>     ```
>
>     - 此时再点击登录后，拿到的就不是promise，而是一个data数据对象，里面有6个属性，这些都是axios给我们封装好的，其中的data才是服务器返回的真实数据，只需要data哦
>       <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqsmizcqj30er03kgm3.jpg" style="zoom:150%;" />
>
>     - 所以我们要从对象身上，将data属性解构赋值出来，用const {data : res}，将data解构，并赋值给res，打印下res，代表拿到的真实的服务器数据
>
>       ```js
>       const { data: res } = await this.$http.post('login', this.loginForm)
>       console.log(res)
>       ```
>
>       <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqxtvokrj30b101vq2v.jpg" style="zoom:200%;" /><img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqzmfygrj30ei0213yk.jpg" style="zoom:200%;" />
>
>     - meta对象里的数据显示出登录成功或者失败，所以需要来个if判断登录状态
>
>       ```js
>       if(res.meta.status !== 200) return console.log('登录失败')
>       console.log('登录成功')
>       ```



#### 🧛‍♀️token保存原理分析~



> - 将登录成功之后的token，保存到客户端的sessionStorage中
>
>   - 项目中，除了登录之外的其它接口，必须在登录之后才能访问
>
>   - token只应在当前网站打开期间生效，所以将token保存在sessionStorage中
>
>     localStorage：持久化存储
>
>     sessionStorage：会话时存储
>
>   - 先打印下res，拿到：
>     ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghsro5j7ctj30eh058dga.jpg)
>
>   - data里的token是我们要拿到的
>
>     ```js
>     window.seesionStorage.setItem('token', res.data.token)
>     ```
>
>   - 再进行页面跳转
>
>     ```js
>     this.$router.push('/home')
>     ```
>
>     跳转之后在控制台中检查是否拿到token
>
>     ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghsrw6dm2xj30ib06q74s.jpg)
>
> - 通过编程式导航跳转到后台主业，路由地址是/home
>
>   - 新建Home.vue,写好三个框架
>   - 在router.js路由规则中，引入/home这个路劲

----------

#### 🧛‍♀️导航守卫控制权限

> - 因为home页面是个有权限的页面，当删除token时，刷新页面，需要重新定位到登录页面，而不是留在home页面
>
>   - 运用路由导航守卫，为路由对象调用beforeEach函数，它接收回调函数，包含3个形参（to，from，next） 
>
>     - to将要访问的路径
>     - from从哪个路径跳转
>     - next是一个函数，表示放行
>       - next()  直接next放行
>       - next('/login') 给个路径表示要强制跳转的页面
>
>   - 在router里的index.js中，
>
>     ```js
>     // 挂载路由导航守卫
>     router.beforeEach((to, from, next) => {
>       if (to.path === '/login') return next()
>       // 获取token
>       const tokenStr = window.sessionStorage.getItem('token')
>       if (!tokenStr) return next('/login')
>       next()
>     })
>     ```



### 一些语法问题の解决

Eslint检查出的问题---缩进；分号；空格····一系列烦人の不规范🤯

解决：

根目录中创建文件 .prettierrc,  他是json格式

```json
{
    "tabWidth": 2,
    "semi": false,
    "singleQuote": true,
    "bracketSpacing": true
}
```






