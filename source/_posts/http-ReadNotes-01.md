---
title: http_ReadNotes-01
date: 2019-06-18 14:16:55
tags: [ReadNotes, http]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/0081Kckwly1gkfm850a0pj30u00k0mz5.jpg
---

> web 开发中可能会用到用到 HTTP 协议的各方面知识 の 工作：
>
> - 编写网络爬虫 phyton 程序
> - 分析抓包数据
> - 实现 HTTP 服务器
> - 提供网站 REST API
> - 修改后端定制框架

---

# I. web 及网络基础

==HTTP (HyperText Transfer Protocol） 超文本传输协议==

## **1.1** **HTTP** 协议访问 Web

- Web 是建立在 HTTP 协议上通信的。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9e5n5837j30if080gm2.jpg)

> 根据指定的 URL，web 浏览器从 web 服务器获取相应资源（resource），然后呈现在 web 页面上，实现这种功能的 web 浏览器 is----`客户端(client)`。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9e9fsd4wj30hq05474o.jpg)

> 完成这一系列事件，需要遵循一种规则约定----超文本传输协议 is `HTTP` Hyper Text Transfer Protocol。

## 1.2 WWW 构建技术

1. `HTML` ： 把 `SGML`(Standard Generalized Markup Language，标准通用标记语言) 作为页面的文本标 记语言的 `HTML`(HyperText Markup Language，超文本标记语言);
2. `HTTP` ：作为文档传递协议的 HTTP。
3. `URL` ：指定文档所在地址- URL(Uniform Resource Locator，统一资源定位符)。

## 1.3 网络基础 TCP/IP

> 通常使用的 网络(包括互联网)是在 `TCP/IP 协议族`的基础上运作的。而 HTTP 属于它内部的一个子集。

### **1.3.1** **TCP/IP** 协议族

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9en3wyyzj30hz08jglz.jpg)

图:**TCP/IP** 是互联网相关的各类协议族的总称

### **1.3.2** **TCP/IP** 的分层管理

TCP/IP 协议族按 层次 分为 4 层:

- `应用层`：
  - 决定了向用户<u>提供应用服务时的通信活动</u>；
  - TCP/IP 协议族内预存了各类通用的应用服务。
    如：`FTP`(File Transfer Protocol，文件传输协议)、
    `DNS`(Domain Name System，域名系统) 服务
  - HTTP 协议也处于该层。
- `传输层` ：
  - 对上层应用层，<u>提供处于网络连接中的两台计算机之间的数据传输</u>。
  - 两个性质不同的协议:
    `TCP`(Transmission Control Protocol，传输控制协议)
    `UDP`(User Data Protocol，用户数据报协议)。
- `网络层` ：
  - 处理在网络上流动的数据包，<u>数据包是网络传输的最小数据单位</u>。
  - 规定了<u>通过怎样的路径</u>(所谓的传输路线)到达对方计算机，并<u>把数据包传送给对方</u>。
  - 与对方计算机之间<u>通过多台计算机或网络设备进行传输时</u>，--<u>在众多的选项内选择一条传输路线</u>。
- `数据链路层` ：（网络接口层）
  - 处理连接网络的硬件部分，包括--
    <u>控制操作系统</u>、
    <u>硬件的设备驱动</u>
    `NIC`(Network Interface Card，网络适配器，即网卡)
    <u>光纤等物理可见部分</u>(还包括连接器等一切传输媒介)。
  - 硬件上的范畴均在链路层的作用范围之内。

> 这样一来：要改变设计时，只需把变动的那层替换掉即可。
>
> 把各层之间的接口部分规划好之 后，每个层次内部的设计就能够自由改动了。

### **1.3.3** **TCP/IP** 通信传输流

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9f2itcrej30i90e0wf5.jpg)

> 发送端从应用层往下走，接收端则往应用层往上走。

发出 HTTP 请求

​ 👇

TCP 协议把 HTTP 请求报文进行分割 ，打上标记序号、端口号

​ 👇

IP 协议增加 MAC 地址(作为<u>通信目的地</u>的 )后转发给链路层

​ 👇

这样一来，发往网络的通信请求就准备齐全了。

​ 👇

接收端的服务器在链路层接收到数据，按序往上层发送

​ 👇

当传输到应用层，才算真正接收到由客端发送过来的 HTTP 请求 👩

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9fbm9tolj30i80fvdh9.jpg)

> 发送端在层与层之间传输数据时，每经过一层时必定会被打上一个==该层所属的首部信息==。
>
> 接收端在层与层传输数据时，每经过一层时会把对应的首部消去。
>
> 这种==把数据信息包装起来==的做法称为`封装`(encapsulate)。

## **1.4** IP、TCP 和 DNS ---与 **HTTP** 关系密切

### **1.4.1** 负责传输的 **IP** 协议

- `IP 地址` ：指明了节点被分配到的地址

- `MAC 地址`(Media Access Control Address) ：网卡所属的固定地址
- IP 地址可以和 MAC 地址进行配对；IP 地址可变换，MAC 地址基本上不会更改。
- IP 间的通信依赖 MAC 地址

在网络上，通信的双方在同一局域网 (LAN)内的情况是很少的，通常是经过多台计算机和网络设备中转才能连接到对方。

而在进行<u>中转时，会利用下一站中转设备的 MAC 地址来搜索下一个中转目标</u>。

这时，会采用 `ARP 协议`(Address Resolution Protocol)。

- `ARP` 是用来 **<u>解析地址</u>**的协议，根据通信方 的 IP 地址就可以反查出对应的 MAC 地址。

- `路由选择(routing)` ：在到达通信目标前的中转过程中，那些计算机和路由器等网络设备只 能获悉很粗略的传输路线。这种机制称为路由选择(routing)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9gfsicbwj30hh0g075q.jpg)

### **1.4.2** 确保可靠性的 **TCP** 协议

> TCP 位于传输层，提供可靠的字节流服务。

- `字节流服务`--- 将大块数据分割成以报文段(segment)为单位的数据包进行管理。

- TCP 协议为 了更容易传送大数据才把数据分割；

- TCP 协议能够确认数据最终是否送达到对方： `三次握手(three-way handshaking)策略`

  - 数据包送出去后，TCP 会向对方确认是否成功送达；
  - 握手过程使用：
    - TCP 的标志(flag)
    - SYN(synchronize)
    - ACK(acknowledgement)。

  SNY ➡️

  ​ ⬅️ SNY/ACK

  ACK➡️

  ​

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9glwue5cj30hg09p3za.jpg)

## 1.5 负责域名解析的 **DNS** 服务

- `DNS`(Domain Name System)提供**域名**到 IP 地址之间的**解析服务**。

- **<u>计算机既可以被赋予 IP 地址，也可以被赋予主机名和域名。</u>**
- 用户通常使用`主机名`或`域名`来访问对方的计算机

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9h2hmrbjj30he0bbaay.jpg)

## **1.6** 各种协议与 **HTTP** 协议的关系

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9h85ouxyj30ft0mswgr.jpg)

## **1.7** **URI** 和 **URL**

`URI` Uniform Resource Identifier 统一资源标识符

- 由某个协议方案表示的资源的定位标识符。
- 协议方案是指 访问资源所使用的协议类型名称；采用 HTTP 协议时，协议方案就是 http。除此之外，还有 ftp、mailto、telnet、file 等；
- `URI` 用字符串标识某一互联网资源，而 `URL` 表示资源的地点(互联 网上所处的位置)。

### **URI** 格式

- 绝对 URI

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gf9icxnnjvj30g703twer.jpg)

> 使用 http: 或 https: 等协议方案名获取访问资源时要指定协议类型。
>
> 不区分字母大小写，最后附一个冒号(:)。
>
> 也可使用 data: 或 javascript: 这类指定数据或脚本程序的方案名。

##### 📟 登录信息(认证)

指定用户名和密码作为从服务器端获取资源时必要的登录信息(身份认证)。此项是可选项。

##### ✉️ 服务器地址

使用绝对 URI 必须指定待访问的服务器地址。地址可以是类似 hackr.jp 这种 `DNS` 可解析的名称，或是 192.168.1.1 这类 `IPv4` 地址名，还可以是 [0:0:0:0:0:0:0:1] 这样用方括号括起来的 `IPv6` 地址名。

##### 🕰 服务器端口号

指定服务器连接的网络端口号。此项也是可选项，若用户省略则自动 使用默认端口号。

##### 📑 带层次的文件路径

指定服务器上的文件路径来定位特指的资源。这与 UNIX 系统的文件目录结构相似。

##### 🔑 查询字符串

针对已指定的文件路径内的资源，可以使用查询字符串传入任意参 数。此项可选。

##### 🔫 片段标识符

使用片段标识符通常可标记出已获取资源中的子资源(文档内的某个位置)。但在 RFC 中并没有明确规定其使用方法。该项也为可选项。

> 一些用来制定 HTTP 协议技术标准的文档，它们被称为`RFC`(Request for Comments，征求修正意见书)。
>
> 并不是所有的应用程序都符合 RFC
>
> RFC 是 互联网的设计文档，要是不按照 RFC 标准执行，就有可能导致无法通信的状况。
>
> 基本 上客户端和服务器端都会以 RFC 为标准来实现 HTTP 协议。

---

# II.简单的 HTTP 协议

## 2.1 **HTTP** 协议作用

**HTTP** 协议用于客户端和服务器端之间的`通信`

请求访问文本或图像等资源的一端称为`客户端`，而提供资源响应的一 端称为`服务器端`。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomjb1s01j30jx07zt9e.jpg)

两台计算机作为客户端和服务器端的<u>角色有可能会互换</u>。但就仅从一条通信路线来说，服务器端和客户端的角色是确定的，而用 HTTP 协议能够明确区分哪端是客户端，哪端是服务器端。

## **2.2** How 达成通信

通过`请求和响应的交换`达成通信

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfommtf5i8j30jk074q3k.jpg)

肯定是先从客户端开始建立通信的，服务器端在没有接收到请求之前不会发送响应。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomnk9uoij30k90acgmd.jpg)

**客户端发送给某个 HTTP 服务器端的请求报文中**：

`GET`------ 请求访问服务器的类型，称为方法 (method)。

`字符串 /index.htm` -------请求访问的资源对象， 也叫做请求 URI(request-URI)。

`HTTP/1.1`------ HTTP 的版本号，用来提示客户端使用的 HTTP 协议功能。

综合来看，这段请求内容的意思是:<u>请求访问某台 HTTP 服务器上的 /index.htm 页面资源。</u>

`请求报文`是由==请求方法==、==请求 URI==、==协议版本==、==可选的请求首部字段== 和内==容实体==构成的。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomrgipocj30k60azgm8.jpg)

**接收到请求的服务器，会将请求内容的处理结果以响应的形式返回：**

`HTTP/1.1` 表示服务器对应的 HTTP 版本。

`200 OK` 表示请求的处理结果的状态码(status code)和原因短语(reason-phrase)。

下一行显示了创建响应的日期时间，是首部字段(header field)内的一个属性。

接着以一空行分隔，之后的内容称为`资源实体的主体`(entity body)。

`响应报文`基本上由==协议版本==、==状态码(表示请求成功或失败的数字代码)==、==用以解释状态码的原因短语==、==可选的响应首部字段==以及==实体主体==构成。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomutu23fj30k20awaal.jpg)

## **2.3** **HTTP** 是不保存状态的协议

HTTP 是一种不保存状态，即`无状态(stateless)协议`。HTTP 协议自身不对请求和响应之间的通信状态进行保存。也就是说在 HTTP 这个级别，协议对于发送过的请求或响应都不做持久化处理。

这是为了更快地处理大量事务，确保协议的可伸缩性，而特意把 HTTP 协议设 计成如此简单的。

HTTP/1.1 虽然是无状态协议，但为了实现期望的保持状态功能，于 是引入了 `Cookie 技术`。有了 Cookie 再用 HTTP 协议通信，就可以管理状态了。

## **2.4** 请求 **URI** 定位资源

HTTP 协议使用 **URI 定位互联网上的资源**。正是因为 URI 的特定功能，在互联网上任意位置的资源都能访问到。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomywnmktj30js0em0u5.jpg)

当客户端请求访问资源而发送请求时，**URI 需要将作为请求报文中的请求 URI 包含在内**。

**指定请求 URI 的方式**有很多。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfomzmqy4sj30jn0a5mxp.jpg)

如果不是访问特定资源而是对服务器本身发起请求，可以 用一个 \* 来代替请求 URI。

下面这个例子是查询 HTTP 服务器端支持 的 HTTP 方法种类。

OPTIONS \* HTTP/1.1

## **2.5** 告知服务器意图的 **HTTP** 方法

### **GET** :获取资源

- ==请求访问已被 URI 识别的资源==
- 指定的资源经服务器端解析后返回响应内容。
- 如果请求的资源是文本，那就保持原样返回;如果是像 CGI(Common Gateway Interface，通用网关接 口)那样的程序，则返回经过执行后的输出结果。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon42wnpfj30g7060mxc.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon4f24taj30lc09pq3m.jpg)

### **POST**:传输实体主体

- 虽然用 GET 方法也可以传输实体的主体，但一般不用 GET 方法进行传输，而是用 POST 方法。
- 虽说 POST 的功能与 GET 很相似，但 POST 的主要目的并不是获取响应的主体内容。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon6hk66ej30hd061dg0.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon6pxttjj30hs04omxg.jpg)

### **PUT**:传输文件

- 就像 FTP 协议的文件上传一样，要求<u>在请求报文的主体中包含文件内容，然后保存到请求 URI 指定的位置</u>。
- 鉴于 HTTP/1.1 的 PUT 方法自身<u>不带验证机制</u>，任何人都可以上传文件 , 存在安全性问题，因此<u>一般的 Web 网站不使用该方法。</u>
- 若配合 Web 应用程序的验证机制，或架构设计采用 REST(REpresentational State Transfer，表征状态转移)标准的同类 Web 网站，就可能会开放使用 PUT 方法。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon8qkhtdj30ki0cwt9w.jpg)

### **HEAD**:获得报文首部

- HEAD 方法和 GET 方法一样，只是不返回报文主体部分。用于<u>确认 URI 的有效性</u>及<u>资源更新的日期时间</u>等。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfon9zhpr5j30is0893yv.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfona6xjqmj30i804igls.jpg)

### **DELETE**:删除文件

- DELETE 方法用来删除文件，是与 PUT 相反的方法。DELETE 方法<u>按 请求 URI 删除指定的资源。</u>

- 但是，HTTP/1.1 的 DELETE 方法本身和 PUT 方法一样<u>不带验证机制</u>，所以一般的 Web 网站也不使用 DELETE 方法。当配合 Web 应用 程序的验证机制，或遵守 REST 标准时还是有可能会开放使用的。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonbcmqytj30jy09naaw.jpg)

### **OPTIONS**:询问支持的方法

- <u>查询针对请求 URI 指定的资源支持的方法</u>。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonbvvjfej30jk070gly.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonc8192qj30iv05qaad.jpg)

### **TRACE**:追踪路径

- <u>让 Web 服务器端将之前的请求通信环回给客户端的方法。</u>

- 发送请求时，在 Max-Forwards 首部字段中填入数值，每经过一个服务器端就将该数字减 1，当数值刚好减到 0 时，就停止继续传输，最后接收到请求的服务器端则返回状态码 200 OK 的响应。

- 客户端通过 TRACE 方法可以查询发送出去的请求是怎样被加工修改 / 篡改的。因为，请求想要连接到源目标服务器可能会通过代理 中转，TRACE 方法就是用来确认连接过程中发生的一系列操作。

  但是，TRACE 方法本来就不怎么常用，再加上它容易引发 XST(Cross-Site Tracing，跨站追踪)攻击，通常就更不会用到了。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonecee5bj30j60cvmyl.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoneu42hcj30ip041t8r.jpg)

### **CONNECT**:要求用隧道协议连接代理

- 要求<u>在与代理服务器通信时建立隧道，实现用隧道协 议进行 TCP 通信。</u>
- 主要使用 SSL(Secure Sockets Layer，安全套接 层)和 TLS(Transport Layer Security，传输层安全)协议把通信内容加密后经网络隧道传输。

- CONNECT 方法的格式如下所示。
  - CONNECT 代理服务器名:端口号 HTTP 版本

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfong1l3d3j30jc0bi3zk.jpg)

## **2.6** 使用方法下达命令

- **向请求 URI 指定的资源发送请求报文时，采用称为方法的命令。**
- 方法的作用在于，**可以指定请求的资源按期望产生某种行为**。
- 方法中 有 `GET`、`POST` 和 `HEAD` 等。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonh7ww7ij30jq08vwf8.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonhxn0q6j30jt0fhtah.jpg)

## **2.7** 持久连接节省通信量

### **2.7.1** 持久连接

持久连接(HTTP Persistent Connections，也称为 HTTP keep-alive 或 HTTP connection reuse)

**特点**： 只要任意一端没有明确提出断开连接，则保持 TCP 连接状态。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonjpakaaj30jr0h90ud.jpg)

**好处**：

- 减少 TCP 连接的重复建立和断开所造成的额外开销，减轻服务器端的负载。
- 使 HTTP 请求和响应能够更早地结束，Web 页面的显示速度提高。

### **2.7.2** 管线化

- 久连接使得多数请求以管线化(pipelining)方式发送成为可能。
- 管线化技术使得 不用等待响应亦可直接发送下一个请求。
- 同时并行发送多个请求，而不需要一个接一个地等待响应。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonn8n2qrj30jr09p752.jpg)

### **2.8** 使用 **Cookie** 的状态管理

HTTP 是无状态协议，无法根据之前的状态进行本次的请求处理。不保存状态，减少服务器的 CPU 及内存资源的消耗。

`Cookie 技术`通过==在请求和响应报文中写入 Cookie 信息来控制客户端的状态==。

- 根据从服务器端发送的响应报文内的 Set-Cookie 的首部字段信息，通知客户端保存 Cookie。
- 当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。
- 服务器端发现客户端发送过来的 Cookie 后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonvunolbj30j90ah0te.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfonwajvvej30j7093wf1.jpg)

**HTTP 请求报文和响应报文的内容:**

1. 请求报文(没有 **Cookie** 信息的状态)

   ```http
   GET /reader/ HTTP/1.1
   Host: hackr.jp
   *首部字段内没有Cookie的相关信息
   ```

2. 响应报文(服务器端生成 **Cookie** 信息)

   ```http
   HTTP/1.1 200 OK
   Date: Thu, 12 Jul 2012 07:12:20 GMT
   Server: Apache
   <Set-Cookie: sid=1342077140226724; path=/; expires=Wed, 10-Oct-12 07:12:20 GMT>
   Content-Type: text/plain; charset=UTF-8
   ```

3. 请求报文(自动发送保存着的 **Cookie** 信息)

   ```http
   GET /image/ HTTP/1.1
   Host: hackr.jp
   Cookie: sid=1342077140226724
   ```

#
