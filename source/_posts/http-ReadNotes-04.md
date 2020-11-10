---
title: http_ReadNotes-04
date: 2019-07-18 23:21:26
tags: [ReadNotes, http]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/007S8ZIlly1gfopwi7vkgj30i30d5dgt.jpg
---

# V. 与 HTTP 协作的 Web 服务器

一台 Web 服务器可搭建多个独立域名的 Web 网站，也可作为通信路 径上的中转服务器提升传输效率。

## **5.1** 用单台虚拟主机实现多个域名

HTTP/1.1 规范允许一台 HTTP 服务器搭建多个 Web 站点。

提供 Web 托管服务(Web Hosting Service)的供应商，可以用一台服务器为多位客户服务，也可以以每位客户持有的域名运行各自不同的网站。这是因为利用了`虚拟主机`(Virtual Host，又称虚拟服务器)的功能。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopwi7vkgj30i30d5dgt.jpg)

客户端使用 HTTP 协议访问服务器时，会经常采用类似 www.hackr.jp 这样的主机名和域名。

在互联网上，域名通过 DNS 服务映射到 IP 地址(域名解析)之后访问目标网站。

当请求发送到服务器时，已经是以 IP 地址形式访问了。

所以，如果一台服务器内托管了 www.tricorder.jp 和 www.hackr.jp 这 两个域名，当收到请求时就需要弄清楚究竟要访问哪个域名。

在相同的 IP 地址下，由于虚拟主机可以寄存多个不同主机名和域名 的 Web 网站，因此在发送 HTTP 请求时，必须在 Host 首部内完整指 定主机名或域名的 URI。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopyxahuqj30ev09omxy.jpg)

## **5.2** 通信数据转发程序 :代理、网关、隧道

HTTP 通信时，除客户端和服务器以外，还有一些用于`通信数据转发的应用程序`可以配合服务器工作。

将请求转发给通信线路上的下一站服务器，并且能接收从那台服务器发送的响应再转发给客户端。

### 代理

代理是一种`有转发功能的应用程序`，它扮演了位于服务器和客户端“中间人”的角色，接收由客户端发送的请求并转发给服务器，同时 也接收服务器返回的响应并转发给客户端。

- 代理不改变请求 URI，会直接发送给前方持有资源的目标服务器。
- 持有资源实体的服务器被称为源服务器。从源服务器返回的响应经过 代理服务器后再传给客户端。
- 在 HTTP 通信过程中，可级联多台代理服务器。请求和响应的转发会 经过数台类似锁链一样连接起来的代理服务器。
- 转发时，需要附加 Via 首部字段以标记出经过的主机信息。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoq5962glj30jl07d0tk.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoq6k52ppj30j2084q42.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoq7m3detj30g4079wf1.jpg)

使用代理服务器的理由:

- 利用缓存技术减少网络带宽的流量，
- 组织内部针对特定网站的访问控制，以获取访问日志为主要目的

代理有多种使用方法：

- 是否使用缓存
- 是否会修改报文

`缓存代理`

代理转发响应时，缓存代理(Caching Proxy)会预先将资源的副本 (缓存)保存在代理服务器上。

当代理再次接收到对相同资源的请求时，就可以不从源服务器那里获取资源，而是将之前缓存的资源作为响应返回。

`透明代理`

转发请求或响应时，不对报文做任何加工的代理类型被称为透明代理 (Transparent Proxy)。反之，对报文内容进行加工的代理被称为非 透明代理。

### 网关

网关是`转发其他服务器通信数据的服务器`，接收从客户端发送来的请求时，它就像自己拥有资源的源服务器一样对请求进行处理。有时客户端可能都不会察觉，自己的通信目标是一个网关。

- 网关能使通信线路上的服务器提供非 HTTP 协议服务。
- 利用网关能提高通信的安全性，在客户端与网关之间的通信线路上加密确保连接的安全。
- 网关可以连接数据库，使用 SQL 语句查询数据。
- 在 Web 购物网站上进行信用卡结算时， 网关可以和信用卡结算系统联动。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoq9mopetj30ii05jdg7.jpg)

### 隧道

隧道是在`相隔甚远的客户端和服务器两者之间进行中转`，并保持双方通信连接的应用程序。

- 届时使用 SSL 等加密手段进行通信。
- 目的是确保客户端能与服务器进行安全的通信。
- 隧道本身不会去解析 HTTP 请求。-----请求保持原样中转给之后的服务器。
- 隧道会在通信双方断开连接时结束。
- 隧道本身是透明的，客户端不用在意隧道的存在

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoqfvvnzoj30j204fwes.jpg)

## **5.3** 保存资源的缓存

`缓存`------代理服务器或客户端本地磁盘内保存的资源副本。

利用缓存可减少对源服务器的访问，节省通信流量和通信时间。

`缓存服务器`是代理服务器的一种，并归类在缓存代理类型中。

当代理转发从服务器返回的响应时，代理服务器将会保存一份资 源的副本。

利用缓存可避免多次从源服务器转发资源。

客户端可就近从缓存服务器上获取资源，而源服务器也不必多次处 理相同的请求了。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoqjp2bohj30jr0hlwg7.jpg)

### **5.3.1** 缓存的有效期限

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoql8swl0j30jm0amjsa.jpg)

### **5.3.2** 客户端的缓存

缓存不仅可以存在于缓存服务器内，还可以存在客户端浏览器中。

以 `Internet Explorer` 程序为例，把客户端缓存称为`临时网络文件` (Temporary Internet File)。

浏览器缓存如果有效，就不必再向服务器请求相同的资源了，可以直 接从本地磁盘内读取。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoqmly6c7j30dz0a2mxm.jpg)

## 在 **HTTP** 出现之前的协议

> 在 HTTP 普及之前，也就是从互联网的诞生期至今，曾出现过各式各样的协议。在 HTTP 规范确立之际，制定者们参考了那些协议的 功能。也有某些协议现在已经彻底退出了人们的视线。接下来，简单介绍一下这些协议。
>
> **FTP**(**File Transfer Protocol**)
>
> 传输文件时使用的协议。该协议历史久远，可追溯到 1973 年前 后，比 TCP/IP 协议族的出现还要早。虽然它在 1995 年被 HTTP 的 流量(Traffic)超越，但时至今日，仍被广泛沿用。
>
> **NNTP**(**Network News Transfer Protocol**)
>
> 用于 NetNews 电子会议室内传送消息的协议。在 1986 年前后出 现，属于比较古老的一类协议。现在，利用 Web 交换信息已成主 流，所以该协议已经不怎么使用了。
>
> **Archie**
>
> 搜索 anonymous FTP 公开的文件信息的协议。1990 年前后出现，现 在已经不常使用。
>
> **WAIS**(**Wide Area Information Servers**) 以关键词检索多个数据库使用的协议。1991 年前后出现。由于现
>
> 在已经被 HTTP 协议替代，也已经不怎么使用了。 **G o phe r**
>
> 查找与互联网连接的计算机内信息的协议。1991 年前后出现，由 于现在已经被 HTTP 协议替代，也已经不怎么使用了。

# VI. HTTP 首部

## **6.1** **HTTP** 报文首部

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoqomhiucj30i0073dg9.jpg)

### **HTTP** 请求报文

在请求中，HTTP 报文由==方法==、==URI==、==HTTP 版本==、==HTTP 首部字段==等部分构成。

下面的示例是访问 http://hackr.jp 时，`请求报文`的首部信息。

```http
GET / HTTP/1.1
Host: hackr.jp
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Ge Accept: text/html,application/xhtml+xml,application/xml;q=0. Accept-Language: ja,en-us;q=0.7,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Connection: keep-alive
If-Modified-Since: Fri, 31 Aug 2007 02:02:20 GMT If-None-Match: "45bae1-16a-46d776ac"
Cache-Control: max-age=0
```

### **HTTP** 响应报文

在响应中，HTTP 报文由 ==HTTP 版本==、==状态码(数字和原因短语)==、 ==HTTP 首部字段== 3 部分构成。

以下示例是之前请求访问 http://hackr.jp/ 时，`返回的响应报文`的首部信息。

```HTTP
HTTP/1.1 304 Not Modified
Date: Thu, 07 Jun 2012 07:21:36 GMT Server: Apache
Connection: close
Etag: "45bae1-16a-46d776ac"
```

## **6.2** **HTTP** 首部字段

### **6.2.1** **HTTP** 首部字段传递重要信息

在客户端与服务器之间以 HTTP 协议进行通信的过程中，无论是请求还是响应都会使用首部字段，它能起到<u>传递额外重要信息</u>的作用。

使用首部字段是<u>为了给浏览器和服务器提供</u>==报文主体大小==、==所使用的语言==、==认证信息==等内容。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoqx2vkajj30jv08nt9h.jpg)

### **6.2.2** **HTTP** 首部字段结构

`首部字段名`： `字段值`

在 HTTP 首部中以 Content-Type 这个字段来表示报文主体的对象类型。 Content-Type: text/html

字段值对应单个 HTTP 首部字段可以有多个值 ---- Keep-Alive: timeout=15, max=100

### 6.2.3 **4** 种 **HTTP** 首部字段类型

根据实际用途：

#### 通用首部字段(**General Header Fields**)

请求报文和响应报文两方都会使用的首部。

#### 请求首部字段(**Request Header Fields**)

从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加 内容、客户端信息、响应内容相关优先级等信息。

#### 响应首部字段(**Response Header Fields**)

从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。

#### 实体首部字段(**Entity Header Fields**)

针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更 新时间等与实体有关的信息。

### **6.2.4** **HTTP/1.1** 首部字段一览

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor11o3avj30e40abq3r.jpg)

​ 请求首部字段

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor16d6tfj30fp0hotav.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor1mqeq8j30et0a8t9q.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor1v4q78j30e30b6q41.jpg)

### **6.2.5** 非 **HTTP/1.1** 首部字段

还有 Cookie、Set-Cookie 和 Content-Disposition 等在其他 RFC 中定义的首部字段，它们的使用频率也很高。

这些非正式的首部字段统一归纳在 RFC4229 HTTP Header Field Registrations 中。

### **6.2.6** **End-to-end** 首部和 **Hop-by-hop** 首部

HTTP 首部字段将定义成`缓存代理`和`非缓存代理`的行为，分成 2 种类型。

#### 端到端首部(**End-to-end Header**)

分在此类别中的首部会转发给请求 / 响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。

#### 逐跳首部(**Hop-by-hop Header**)

分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。HTTP/1.1 和之后版本中，如果要使用 hop-by-hop 首部，需提供 Connection 首部字段。

下面列举了 HTTP/1.1 中的逐跳首部字段。除这 8 个首部字段之外， 其他所有字段都属于端到端首部。

- Connection
- Keep-Alive
- Proxy-Authenticate
- Proxy-Authorization
- Trailer
- TE
- Transfe r-Encoding
- Upgrade

## **6.3** **HTTP/1.1** 通用首部字段

### **6.3.1** Cache-Control

通过指定首部字段 Cache-Control 的指令，就能操作缓存的工作机 制。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor6f14pwj30j207ut98.jpg)

指令的参数是可选的，多个指令之间通过“,”分隔。首部字段 Cache-Control 的指令可用于请求及响应时。

```http
Cache-Control: private, max-age=0, no-cache
```

#### **Cache-Control** 指令一览

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor7jaab4j30f609fmy4.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor7tnfgij30ji0bfq4i.jpg)

#### 表示是否能缓存的指令 **public** 指令

当指定使用 public 指令时，则明确表明其他用户也可利用缓存

```http
Cache-Control: public
```

#### **private** 指令

响应只以特定的用户作为对象，这与 public 指令的行为相反。缓存服务器会对该特定用户提供资源缓存的服务，对于其他用户发送 过来的请求，代理服务器则不会返回缓存。

```http
Cache-Control: private
```

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfor8yt220j30ik07374r.jpg)

#### **no-cache** 指令
