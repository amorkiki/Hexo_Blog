---
title: http_ReadNotes-03
date: 2019-06-27 19:40:18
tags: [ReadNotes, http]
top_img: false
categories: tech-share
indexing: true
cover: https://tva1.sinaimg.cn/large/007S8ZIlly1gfop86haw5j30jk0bhaaz.jpg
---

# IV. 返回结果的 HTTP 状态码

`HTTP 状态码`负责表示客户端 HTTP 请求的返回结果、标记服务器端的处理是否正常、通知出现的错误等工作。

## **4.1** 状态码告知从服务器端返回的请求结果

<u>状态码的职责</u>： 当客户端向服务器端发送请求时，描述返回的请求结果。借助状态码，用户可以知道服务器端是正常处理了请求，还是出现了错误。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfop86haw5j30jk0bhaaz.jpg)

状态码如 200 OK，以 3 位数字和原因短语组成。

数字中的第一位指定了响应类别，后两位无分类。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfop93smf5j30j4070my0.jpg)

## **4.2** **2XX** 成功

### **4.2.1** 200 OK

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopdhqdutj30jk06maah.jpg)

在响应报文内，随状态码一起返回的信息会因方法的不同而发生改变。比如，使用 GET 方法时，对应请求资源的实体会作为响应返回;而使用 HEAD 方法时，对应请求资源的实体首部不随报文主体作为响应返回(即在响应中只返回首部，不会返回实体的主体部分)。

### **4.2.2** 204 No Content

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopd2p2elj30ha064q3a.jpg)

服务器接收的请求已成功处理，但在返回的响应报文中不含实体的主体部分。另外，也不允许返回任何实体的主体。

比如， 当从浏览器发出请求处理后，返回 204 响应，那么浏览器显示的页面 不发生更新。

一般在**只需要从客户端往服务器发送信息，而对客户端不需要发送新信息内容的情况下**使用。

### **4.2.3** 206 Partial Content

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopf513o6j30ir06bmxk.jpg)

客户端进行了范围请求，而服务器成功执行了这部分的 GET 请求。响应报文中包含由 Content-Range 指定范围的实体内容。

## **4.3** **3XX** 重定向

### **4.3.1** 301 Moved Permanently

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopfx7t4ij30gz06bq3b.jpg)

`永久性重定向`

该状态码表示请求的资源已被分配了新的 URI，以后 应使用资源现在所指的 URI。

如果已经把资源对应的 URI 保存为书签了，这时应该按 Location 首部字段提示的 URI 重新保存。

像下方给出的请求 URI，当指定资源路径的最后忘记添加斜杠“/”，就 会产生 301 状态码。

http://example.com/sample

### 4.3.2 302 Found

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfophpt51xj30g506x74o.jpg)

`临时性重定向`

该状态码表示请求的资源已被分配了新的 URI，希望用户(本次)能使用新的 URI 访问。

和 301 Moved Permanently 状态码相似，但 302 状态码代表的资源不是被永久移动，只是临时性质的。

比如，用户把 URI 保存成书签，但不会 像 301 状态码出现时那样去更新书签，而是仍旧保留返回 302 状态码 的页面对应的 URI。

### 4.3.3 303 See Other

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopjcpbokj30ho077t94.jpg)

由于请求对应的资源存在着另一个 URI，应使用 GET 方法定向获取请求的资源。

303 状态码和 302 Found 状态码有着相同的功能，但 303 状态码明确 表示客户端应当采用 GET 方法获取资源，这点与 302 状态码有区 别。

比如，当使用 POST 方法访问 CGI 程序，其执行后的处理结果是希望 客户端能以 GET 方法重定向到另一个 URI 上去时，返回 303 状态 码。虽然 302 Found 状态码也可以实现相同的功能，但这里使用 303 状态码是最理想的。

当 301、302、303 响应状态码返回时，几乎所有的浏览器都会把 POST 改成 GET，并删除请求报文内的主体，之后请求会自动再次 发送。

301、302 标准是禁止将 POST 方法改变成 GET 方法的，但实际使 用时大家都会这么做。

### 4.3.4 304 Not Modified

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoplihnevj30hh06p3yv.jpg)

客户端发送附带条件的请求时，服务器端允许请求访问资源，但未满足条件的情况。

304 状态码返回时，不包含任何响应的主体部分。304 虽然被划分在 3XX 类别中，但是和重定向没有关系。

`附带条件的请求`是指采用 GET 方法的请求报文中包含 If-Match，If-Modified- Since，If-None-Match，If-Range，If-Unmodified-Since 中任一首部。

### 4.3.5 307 Temporary Redirect

`临时重定向`

该状态码与 302 Found 有着相同的含义。尽管 302 标准禁止 POST 变换成 GET，但实际使用时大家并不遵守。

307 会遵照浏览器标准，不会从 POST 变成 GET。但是，对于处理响应时的行为，每种浏览器有可能出现不同的情况。

## **4.4** **4XX** 客户端错误

### 4.4.1 400 Bad Request

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopp1zu1vj30gr068q39.jpg)

请求报文中存在<u>语法错误</u>。当错误发生时，需修改请求 的内容后再次发送请求。

另外，浏览器会像 200 OK 一样对待该状态 码。

### **4.4.2** 401 Unauthorized

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoppj6o2cj30gr0diq46.jpg)

发送的请求需要有通过 HTTP 认证(BASIC 认证、 DIGEST 认证)的认证信息。

若之前已进行过 1 次请求，则表示 用户认证失败。

返回含有 401 的响应必须包含一个适用于被请求资源的 WWW- Authenticate 首部用以质询(challenge)用户信息。

当浏览器初次接收 到 401 响应，会弹出认证用的对话窗口。

### **4.4.3** 403 Forbidden

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopqkl824j30fv05o74k.jpg)

对请求资源的访问被服务器拒绝了。服务器端没有必要 给出拒绝的详细理由，但如果想作说明的话，可以在实体的主体部分对原因进行描述，这样就能让用户看到了。

未获得文件系统的访问授权，访问权限出现某些问题(从未授权的发送源 IP 地址试图访问)等列举的情况都可能是发生 403 的原因。

### **4.4.4** 404 Not Found

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfoprjbvpvj30ji06k74l.jpg)

服务器上无法找到请求的资源。除此之外，也可以在服务器端拒绝请求且不想说明理由时使用。

## **4.5** **5XX** 服务器错误

### **4.5.1** 500 Internal Server Error

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopsjve7vj30im05zmxg.jpg)

服务器端在执行请求时发生了错误。也有可能是 Web 应用存在的 bug 或某些临时的故障。

### **4.5.2** **503 Service Unavailable**

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gfopt237lnj30fe05nweq.jpg)

服务器暂时处于超负载或正在进行停机维护，现在无法 处理请求。如果事先得知解除以上状况需要的时间，最好写入 RetryAfter 首部字段再返回给客户端。

> 状态码和状况的不一致
>
> 不少返回的状态码响应都是错误的，但是用户可能察觉不到这点。
>
> 比如 Web 应用程序内部发生错误，状态码依然返回 200 OK，这种 情况也经常遇到。
