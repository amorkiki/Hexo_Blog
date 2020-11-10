---
title: vue_shop--login branch
date: 2018-11-20 18:31:07
tags: vue
categories: project-share
top_img: false
indexing: true
---

## 前端项目脚手架初始化 （略略略 👅）

## 后台项目的环境安装配置

> 1 安装 MySQL 数据库 --给予 XAMMP
>
> 2 安装 Node.js 环境 ✅
>
> 3 配置项目相关信息
>
> 4 启动项目
>
> 5 使用 Postman 测试后台项目接口是否正常
>
> - 在 api 文件夹中安装依赖包
> - 用 node 命令执行 app.js
> - 查看 api 接口文档
> - 基于登录接口，测试接口是否正常工作：
>   - 安装 postman
>   - 输入要测试的请求方式，地址以及其他参数可在 body 里设置，
>   - 点击 send

> 用 XAMMP 导入给予的 SQL 文件：
>
> - 启动 XAMMP，打开 network
> - http://localhost:8080/phpmyadmin 进入 myadmin
> - 创建新数据库 vue_shop
> - 导入 sql 文件

---

### 登录业务的相关技术点 noted~🤭

1. http 是无状态的
2. 通过 cookie 在客户端记录状态
3. 通过 session 在服务器端记录状态 
4. 通过 token 方式维持状态

> - localStorage：持久化存储
> - sessionStorage：会话时存储

---

### 🧠 操作要点步骤分析：

#### 登录页面布局注意点：

- 在项目文件夹下写代码之前，先 git status 一下，看当前工作区是否干净

- > 登录组件布局：
  >
  > - 背景颜色 在根盒子 div 中添加 class 名，在下面 style 中写相应的 class
  >
  > - 添加后页面出现报错 Can't resolve 'less-loader
  >
  > - 因为要使用 less 必须先配置 less-loader
  >
  >   - 在依赖中安装 less-loader 在开发依赖中
  >   - 又因为 less-loader 是在 less 下执行的，再安装一下 less
  >
  > - 运行后发现背景色并没有铺满页面，控制台中检查发现是 HTML 元素没有铺满全屏
  >
  > - 修改相应元素的样式，使得其铺满全屏
  >
  >   - 在 src 下的 assets 里新建 css 文件夹，在其中建立 global.css 群居样式表--（静态文件添加
  >
  >   - ```css
  >     /* 全局样式表 */
  >     html,
  >     body,
  >     #app {
  >       height: 100%;
  >       margin: 0;
  >       padding: 0;
  >     }
  >     ```
  >
  >   - 导入全局样式表到 main.js
  >
  >   - 页面中查看效果，发现 HTML 和 body 等元素已经撑满屏幕
  >
  > - 找到设置背景的 div 盒子，也让他撑满屏幕（这个在 Login.vue 中写哦~~）

> - 密码框和用户名前的小图标
>
>   - 在 el-input 里添加 prefix-icon="el-icon-search"
>
> - 图标跟换：通过第三方图标库--阿里图标库
>
>   - 使用步骤如下：
>
>     第一步：引入项目下面生成的 fontclass 代码：
>
>     ```html
>     <link rel="stylesheet" type="text/css" href="./iconfont.css" />
>     ```
>
>     第二步：挑选相应图标并获取类名，应用于页面：
>
>     ```css
>     <i class="iconfont icon-xxx"></i>
>     ```
>
>     > "iconfont"是你项目下的 font-family。可以通过编辑项目查看，默认是"iconfont"。
>
> - 将 fonts 文件夹放入 assets 里
>
> - 在入口文件中导入字体图标 main.js
>
>   ```js
>   import "./assets/fonts/iconfont.css";
>   ```
>
> - 组件中添加 icon

> - 登录前的预校验
>
>   - Form Methods 里有个 validate 函数，接受一个 callback 回调，回调函数会在校验结束后被调用（成功或失败）
>
>   - 拿到表单引用对象, 绑定登录按钮的单击事件 login，在 methods 里添加事件 login
>
>   - validate 函数接收的回调函数用箭头函数，返回的 valid 值时布尔值
>
>     ```js
>     login () {
>       this.$refs.loginFormRef.validate(valid => {
>         console.log(valid)
>       })
>     }
>     ```

---

### 实现登录

#### 🧛‍♀️axios 包实现原理分析~

> - 根据验证结果决定是否发具体的登录请求
>
>   - 接着上面的 login 的箭头函数，添加一个 if 判断
>
>     ```js
>     //如果不通过，直接return,不发起请求
>     if (!valid) return;
>     ```
>
>   - 如果 valid 是 true，则可以发起登录请求---需要全局配置 axios 包
>
>     - 在 main.js 里全局引入 import axios from 'axios'
>     - 将这个包挂载到 vue 的原型对象上，Vue.prototype.\$http = axios，
>     - 这样每个 vue 组件都可以通过 this 访问\$http, 从而去发起 ajax 请求
>     - 在给 axios 设置请求根路径：axios.defaults.baseURL = '接口的地址'
>
>     ```js
>     // 导入axios包
>     import axios from "axios";
>     // 配置请求的根路径
>     axios.defaults.baseURL = "https://api.naccl.top/vue/shop/api/private/v1/";
>     // 将axios挂在到Vue原型上
>     Vue.prototype.$http = axios;
>     ```
>
>   - 回到 Login.vue 组件，在箭头函数中通过 this 访问到原型上的\$http
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
>     - 此时再点击登录后，拿到的就不是 promise，而是一个 data 数据对象，里面有 6 个属性，这些都是 axios 给我们封装好的，其中的 data 才是服务器返回的真实数据，只需要 data 哦
>       <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqsmizcqj30er03kgm3.jpg" style="zoom:150%;" />
>
>     - 所以我们要从对象身上，将 data 属性解构赋值出来，用 const {data : res}，将 data 解构，并赋值给 res，打印下 res，代表拿到的真实的服务器数据
>
>       ```js
>       const { data: res } = await this.$http.post("login", this.loginForm);
>       console.log(res);
>       ```
>
>       <img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqxtvokrj30b101vq2v.jpg" style="zoom:200%;" /><img src="https://tva1.sinaimg.cn/large/007S8ZIlly1ghsqzmfygrj30ei0213yk.jpg" style="zoom:200%;" />
>
>     - meta 对象里的数据显示出登录成功或者失败，所以需要来个 if 判断登录状态
>
>       ```js
>       if (res.meta.status !== 200) return console.log("登录失败");
>       console.log("登录成功");
>       ```

#### 🧛‍♀️token 保存原理分析~

> - 将登录成功之后的 token，保存到客户端的 sessionStorage 中
>
>   - 项目中，除了登录之外的其它接口，必须在登录之后才能访问
>
>   - token 只应在当前网站打开期间生效，所以将 token 保存在 sessionStorage 中
>
>     localStorage：持久化存储
>
>     sessionStorage：会话时存储
>
>   - 先打印下 res，拿到：
>     ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghsro5j7ctj30eh058dga.jpg)
>
>   - data 里的 token 是我们要拿到的
>
>     ```js
>     window.seesionStorage.setItem("token", res.data.token);
>     ```
>
>   - 再进行页面跳转
>
>     ```js
>     this.$router.push("/home");
>     ```
>
>     跳转之后在控制台中检查是否拿到 token
>
>     ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghsrw6dm2xj30ib06q74s.jpg)
>
> - 通过编程式导航跳转到后台主业，路由地址是/home
>
>   - 新建 Home.vue,写好三个框架
>   - 在 router.js 路由规则中，引入/home 这个路劲

---

#### 🧛‍♀️ 导航守卫控制权限

> - 因为 home 页面是个有权限的页面，当删除 token 时，刷新页面，需要重新定位到登录页面，而不是留在 home 页面
>
>   - 运用路由导航守卫，为路由对象调用 beforeEach 函数，它接收回调函数，包含 3 个形参（to，from，next）
>
>     - to 将要访问的路径
>     - from 从哪个路径跳转
>     - next 是一个函数，表示放行
>       - next() 直接 next 放行
>       - next('/login') 给个路径表示要强制跳转的页面
>
>   - 在 router 里的 index.js 中，
>
>     ```js
>     // 挂载路由导航守卫
>     router.beforeEach((to, from, next) => {
>       if (to.path === "/login") return next();
>       // 获取token
>       const tokenStr = window.sessionStorage.getItem("token");
>       if (!tokenStr) return next("/login");
>       next();
>     });
>     ```

### 一些语法问题の解决

Eslint 检查出的问题---缩进；分号；空格····一系列烦人の不规范 🤯

解决：

根目录中创建文件 .prettierrc, 他是 json 格式

```json
{
  "tabWidth": 2,
  "semi": false,
  "singleQuote": true,
  "bracketSpacing": true
}
```
