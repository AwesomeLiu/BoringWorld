## 1. 浏览器存储的方式有哪些

| 方式 | 数据生命周期 | 数据存储大小 | 与服务端通信 |
|-----|-----|-----|-----|
| cookie | 由服务器生成，可以设置过期时间 | 4k | 每次都会携带在 header 中，影响性能 |
| localStorage | 手动清除 | 5M | 不参与 |
| sessionStorage | 页面关闭就被清理 | 5M | 不参与 |
| indexedDB | 手动清除 | 无限 | 不参与 |

## 2. 跨域

原文： https://juejin.im/post/6844903767226351623

### 同源策略：协议 + 域名 + 端口 三者相同才叫同源

`http://www.abc.com:8080/foo/bar.js`

+ 协议：http://

+ 域名

    - 子域名：www

    - 主域名：abc.com

+ 端口号：8080

同源策略限制的内容有：

+ Cookie / LocalStorage / IndexedDB 等存储性内容

+ DOM 节点

+ AJAX 请求发送后，结果被浏览器拦截

但以下三个标签是允许跨域加载资源的：

+ `<img src="xxx" />`

+ `<link href="xxx">`

+ `<script src="xxx">`

特别说明：

1. 如果是协议和端口造成的跨域问题，前台是无能为力的

2. 在跨域问题上，不会根据域名对应的 IP 地址是否相同来判断

跨域请求可以发出去，服务端也能收到请求并正常返回结果，只是结果被浏览器拦截了

### 跨域解决方案

#### 1. jsonp

原理：利用 `<script>` 标签没有跨域限制的漏洞，网页可以得到从其他来源动态产生的 JSON 数据。JSONP 请求一定需要对方的服务器做支持才可以

JSONP 属于非同源策略

优点：兼容性好，可用于解决主流浏览器的跨域数据访问的问题

缺点：仅支持 get 方法，具有局限性，不安全可能会遭受 XSS 攻击

#### 2. CORS （跨域资源共享）

CORS 需要浏览器和后端同时支持，IE 8/9 需要通过 XDomainRequest 来实现

浏览器会自动进行 CORS 通信，实现 CORS 通信的关键是后端。服务端设置 Access-Control-Allow-Origin 开启 CORS

2.1. 简单请求

同时满足以下条件，就属于简单请求

+ 使用 GET / HEAD / POST 

+ Content-Type 的值为 text/plain / multipart/form-date / application/x-www-form-urlencoded

+ 请求中的任意 XMLHttpRequestUpload 对象没有注册任何事件监听器；XMLHttpRequestUpload对象可以使用 XMLHttpRequest.upload 属性访问

+ 请求中没有使用 ReadableStream 对象

2.2. 复杂请求

不符合简单请求的请求均属于复杂请求。复杂请求的 CORS 请求，会在正式通信之前，增加一次 HTTP 查询请求，称为 预检请求，该请求是 Options，通过该请求来知道服务端是否允许跨域请求

+ Access-Control-Allow-Origin

+ Access-Control-Allow-Headers

+ Access-Control-Allow-Methods

+ Access-Control-Max-Age

+ ...

#### 3. postMessage

postMessage 是 HTML5 XMLHttpRequest Level 2 中的 API，是为数不多可以跨域操作的 window 属性之一，可用于解决以下问题：

+ 页面和其打开的新窗口的数据传递

+ 多窗口之间消息传递

+ 页面与嵌套的 iframe 消息传递

+ 以上场景的跨域数据传递

postMessage() 方法允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域信息传递

> otherWindow.postMessage(message, targetOrigin, [transfer])

+ message: 将要发送的数据

+ targetOrigin: 通过窗口的 origin 属性来指定哪些窗口能接收到消息事件，其值可以是字符串 "*"（无限制） 或一个 URL。在发送消息时，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配 targetOrigin 提供的值，那么消息就不会被发送，只有完全一致才会被发送

+ transfer: 可选参数，是一串和 message 同时传递的 Transferable 对象，这些对象的所有权将被转译给消息的接收方，而发送一方将不再保留所有权

#### 4. WebSocket

WebSocket 是 HTML5 的一个持久化的协议，它实现了浏览器与服务器的全双工通信，同时也是跨域的一种解决方案。

WebSocket 和 HTTP 都是应用层协议，属于 TCP 协议。但 WebSocket 是一种双向通信协议，在建立连接之后，WebSocket 的 server 与 client 都能主动向对方发送或接受数据

使用 Socket.io 较原生 WebSocket API 更加方便，并提供了向下兼容

#### 5. Node 中间件代理（两次跨域）

原理：同源策略是浏览器需要遵循的标准，而如果是服务器向服务器请求就无需遵循同源策略

代理服务器的步骤：接受客户端请求 -> 将请求转发给服务器 -> 拿到服务器响应数据 -> 将响应转发给客户端

#### 6. nginx 反向代理

使用 nginx 反向代理实现跨域，是最简单的跨域方式。只需要修改 nginx 的配置即可解决跨域问题，支持所有浏览器，支持 session，不需要修改任何代码，不会影响服务器性能

实现思路：通过 nginx 配置一个代理服务器（域名和 domain1 相同，端口不同）做跳板机，反向代理访问 domain2 接口，并且可以顺便修改 cookie 中 domain 信息，方便当前域 cookie 写入，实现跨域登录

#### 7. window.name + iframe

window.name 属性的独特之处：name 值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值

通过 iframe 的 src 属性由外域转向本地域，跨域数据即由 iframe 的 window.name 从外域传递到本地域

#### 8. location.hash + iframe

实现原理：不同域之间通过 iframe 的 location.hash 传值，相同域之间直接 js 访问来通信

#### 9. document.domain + iframe

该方法只适用于二级域名相同的情况下，如 a.test.com 和 b.test.com

实现原理：两个页面都通过 js 强制设置 document.domain 为基础主域，就实现了同域

### 总结

+ CORS 支持所有类型的 HTTP 请求，是跨域 HTTP 请求的根本解决方案

+ JSONP 只支持 GET 请求，JSONP 的优势在于支持老式浏览器，以及可以向不支持 CORS 的网站请求数据

+ 不管是 Node 中间件代理或 nginx 反向代理，主要是通过同源策略对服务器不加限制

+ 用的较多的跨域方案是 cors 和 nginx 反向代理

## 3. 输入 URL 回车后发生什么？

参考1： https://zhuanlan.zhihu.com/p/43369093

参考2： https://zhuanlan.zhihu.com/p/81267752

1. 浏览器查找当前 URL 是否存在缓存，并比较缓存是否过期

2. DNS 解析 URL 对应的 IP

3. 根据 IP 建立 TCP 连接（三次握手）

4. 发起 HTTP 请求

5. 服务器处理请求，浏览器接受 HTTP 响应

6. 浏览器解析，构建 DOM 树，渲染页面

7. 关闭 TCP 连接（四次挥手）

## 4. 浏览器渲染的步骤

原文： https://www.jianshu.com/p/e6252dc9be32

用户看到页面实际上可以分为两个阶段：页面内容加载完成和页面资源加载完成，分别对应于 DOMContentLoaded 和 Load

+ DOMContentLoaded 事件触发时，仅当 DOM 加载完成，不包括样式表、图片等

+ load 事件触发时，页面上所有的 DOM，样式表，脚本，图片都已加载完成

浏览器渲染过程主要包括以下几步：

1. 浏览器将获取的 HTML 文档解析成 DOM 树

2. 处理 CSS 标记，构成层叠样式表模型 CSSOM (CSS Object Model)

3. 将 DOM 和 CSSOM 合并成渲染树 (rendering tree)，代表一系列将被渲染的对象

4. 渲染树的每个元素包含的内容都是计算过的，它被称之为布局 layout。浏览器使用一种流式处理的方法，只需要一次绘制操作就可以布局所有的元素

5. 将渲染树的各个节点绘制到屏幕上，这一步被称为绘制 painting

以上步骤并不一定是一次性顺序完成，比如 DOM 或 CSSOM 被修改时，亦或是哪个过程会重复执行，这样才能计算出哪些像素需要在屏幕上进行渲染。而在实际情况中， JS 和 CSS 的某些操作会多次修改 DOM 或 CSSOM

### 浏览器渲染网页的具体流程

#### 构建 DOM 树

当浏览器接收到服务器响应来的 HTML 文档后，会遍历文档节点，生产 DOM 树

+ DOM 树在构建过程中可能会被 CSS 和 JS 加载而执行阻塞

+ `display: none` 的元素也会在 DOM 树中

+ 注释也会在 DOM 树中

+ script 标签也会在 DOM 树中

无论是 DOM 还是 CSSOM，都要经过 Bytes(3C 62 6F...) -> characters(`<html>`) -> tokens(StartTag: html) -> nodes(HTML Node) -> object model(tree)

当前节点的所有子节点都构建好后才会去构建当前节点的下一个兄弟节点

#### 构建 CSSOM 规则树

浏览器解析 CSS 文件并生成 CSSOM，每个 CSS 文件都被分析成一个 StyleSheet 对象，每个对象都包含 CSS 规则，CSS 规则对象包含对应于 CSS 语法的选择器和声明对象以及其他对象

+ CSS 解析可以和 DOM 解析同时进行

+ CSS 解析与 script 的执行互斥

+ 在 webkit 内核中进行了 script 执行优化，只有在 JS 访问 CSS 时才会发生互斥

#### 构建渲染树 (Render Tree)

通过 DOM 树和 CSS 规则树，浏览器可以将其构建渲染树。浏览器会先从 DOM 树的根节点开始遍历每个可见节点，然后对每个可见节点找到适配的 CSS 样式规则并应用

+ Render Tree 和 DOM Tree 不完全对应

+ `display: none` 的元素不在 Render Tree 中

+ `visibility: hidden` 的元素在 Render Tree 中

渲染树生成后，需要各个节点的位置信息，才能渲染到屏幕上

#### 渲染树布局 (layout of the render tree)

布局阶段会从渲染树的根节点开始遍历，每个渲染树的节点都是一个 Render Object 对象，包含宽高，位置，背景色等样式信息

+ `float`, `absolute`, `fixed` 元素会发生位置偏移

+ 脱离文档流就是指脱离 Render Tree

#### 渲染树绘制 (painting the render tree)

在绘制过程中，浏览器会遍历渲染树，调用渲染器的 `paint()` 方法，绘制工作由浏览器的 UI 后端组件来完成

### 渲染阻塞

#### JS 阻塞

JS 可以修改 DOM 结构和节点样式。当浏览器在遇到 `<script>` 标签时，DOM 构建暂停，直至脚本完成执行，然后继续构建 DOM。如果脚本是外部的，会等待脚本下载完毕，再继续解析文档。

在 `<script>` 上增加 `defer` 或 `async` 属性，脚本解析会将脚本中改变 DOM 和 CSS 的地方分别解析出来，然后追加到 DOM 树和 CSSOM 规则树上

#### CSS 阻塞

浏览器必须保证在合成渲染树前，CSSOM 是完备的（内联、内部和外部样式全部下载并解析完毕），只有 CSSOM 和 DOM 的解析完全结束，浏览器才会进入下一步渲染，这就是 CSS 阻塞

CSS 阻塞会使页面一直处于白屏状态，即使没有给页面任何样式声明，CSSOM 依然会生成，生成的 CSSOM 自带浏览器默认样式

当解析 HTML 时，会把新来的元素插入到 DOM 树里，同时查找 CSS，然后把样式规则应用到元素上，查找样式表是按照从右到左的顺序匹配

### 回流和重绘 (reflow 和 repaint)

#### reflow

当浏览器发现布局发生了变化，就会倒回去重新渲染，回退的过程叫 reflow

reflow 会从 html 这个 root frame 开始递归往下，依次计算所有节点尺寸和位置，以确认是渲染树的一部分还是全部发生了变化

#### repaint

当改变某个元素的背景色、文字颜色、边框颜色等不影响周围或内部布局的属性时，会触发 repaint

+ `display: none` 触发 reflow

+ `visibility: hidden` 触发 repaint

#### 引发 reflow

浏览器会对回流做优化，批量处理回流

+ 页面第一次渲染（初始化）

+ DOM 树变化（增删节点）

+ Render Tree 变化（padding, width 等改变）

+ 浏览器窗口 resize

+ 获取元素的某些属性（offsetLeft/Top/Width/Height / scrollTop/Left/Width/Height / clientTop/Left/Width/Height / getComputedStyle()） 会提前触发回流，优化失效

#### 引发 repaint

+ reflow 必定引起 repaint

+ 改变颜色，背景色等（注意：字体大小发生变化引发 reflow）

#### 减少 reflow / repaint 的触发次数

+ 使用 `transform` 形变和位移可以减少 reflow

+ 避免逐个修改节点样式

+ 使用 Document Fragment 将需要多次修改的 DOM 元素缓存，最后一次性 append 到真实的 DOM 中渲染

+ 可以将多次修改的 DOM 元素设置 `display: none`，操作完再显示（`display: none` 的元素不在 Render Tree 内，修改隐藏元素的样式不会触发回流重绘）

+ 避免多次读取某些属性

+ 通过绝对位移将复杂的节点元素脱离文档流，形成新的 Render Layer，降低回流成本

### 优化渲染效率

+ 样式文件放在 `<head>` 里，脚本文件放在 `<body>` 结束前，防止阻塞

+ 简化并优化 CSS 选择器，减少嵌套层

+ DOM 的多个读/写操作放在一起，避免读写操作交叉

+ 重排得到的样式可以缓存结果

+ 通过 class 或 csstext 属性批量改变样式

+ 使用 `transform` 来做位移和形变

+ 尽量使用离线 DOM 来改变元素样式，如 Document Fragment 对象

+ 使用 `cloneNode()` 方法在克隆的节点上操作，再用克隆的节点替换原始节点

+ 先将元素设为 `display: none` （1 次重排和重绘），然后再进行多次操作，最后在恢复显示（1 次重排和重绘）

+ `absolute` 或 `fixed` 元素，重排开销会较小

+ 只有在必要的时候才使用 `display`，而 `visibility` 只有重绘

+ 使用 `window.requestAnimationFrame()` 和 `window.requestIdleCallback()` 调节重新渲染

## 5. get 和 post 的区别

+ 仅限于浏览器发请求的场景：GET 请求没有 body，只有 url，请求数据放在 url 的 querystring 中；POST 请求的数据放在 body 中

    - 如 Elastic Search 中的 GET，使用 body 来查询

+ GET 能对请求的数据做缓存，POST 不能缓存

+ REST 中的 GET / POST / PUT / DELETE

    - GET 用于获取资源

    - POST 用于创建一个资源

    - PUT 请求体应该是完整的资源，实际语义是 replace，用于替换资源

    - DELETE 用于删除资源

+ 私密数据传输使用 POST + body 更好，防止 body 里的数据被记录下来，使用 https

+ http 中 GET 在 url 中的参数只能支持 ASCII，而 POST 在 body 中的参数能支持任意 binary

+ 由于 url 有长度限制，导致 GET 数据有长度限制，限制都是由客户端/浏览器和服务器端决定的，HTTP 协议本身没有限制

## 6. 强制缓存和协商缓存

原文： https://zhuanlan.zhihu.com/p/143064070

### 强制缓存

强制缓存是直接从浏览器缓存中查找结果，并根据结果的缓存规则来决定是否使用该缓存的过程

+ 不存在缓存结果和标识，强制缓存失效，直接向服务器发起请求

+ 存在缓存结果和标识，但结果已失效，强制缓存失效，使用协商缓存

+ 存在缓存结果和标识，结果未失效，强制缓存生效，直接返回该结果

控制强制缓存的字段分别是 `Expires` (HTTP/1.0) 和 `Cache-Control` (HTTP/1.1)，其中 `Cache-Control` 优先级比 `Expires` 高

### 协商缓存

协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发送请求，由服务器根据缓存标识决定是否使用缓存的过程

+ 协商缓存生效，返回 304，服务器告诉浏览器资源未更新，则再去浏览器缓存中访问资源

+ 协商缓存失效，返回 200 和请求结果

协商缓存的字段：

+ `Last-Modified/If-Modified-Since`

+ `Etag/If-None-Match` (优先级高)

### 总结

强制缓存优先于协商缓存，若强制缓存生效则直接使用缓存，若不生效则进行协商缓存。协商缓存由服务器决定是否使用缓存，若协商缓存失效，则重新获取请求结果，生效则返回 304，继续使用缓存

## 7. HTTP 状态码

| 状态码分类 | 说明 |
|-----|-----|
| 100 - 199 | 信息响应 |
| 200 - 299 | 成功响应 |
| 300 - 399 | 重定向 |
| 400 - 499 | 客户端错误 |
| 500 - 599 | 服务器错误 |

| 常见状态码 | 英文 | 说明 |
|-----|-----|-----|
| 200 | OK | 请求成功 |
| 301 | Moved Permanently | 永久重定向 |
| 302 | Found | 临时重定向 |
| 304 | Not Modified | 协商缓存生效，资源未更新 |
| 403 | Forbidden | 服务器拒绝执行，与 401 不同的是，身份验证不能提供帮助 |
| 404 | Not Found | 请求失败，资源未被发现，与 410 不同的是，没有任何信息告知状况是暂时的还是永久的 |
| 500 | Internal Serval Error | 服务器遇到了不知道如何处理的情况 |
| 502 | Bad Gateway | 服务器作为网关需要得到一个处理请求的响应，但得到的是错误的响应 |
| 503 | Service Unavailable | 服务器因维护或重载而停机，无法处理请求 |
| 504 | Gateway Timeout | 服务器作为网关，不能及时得到响应时返回此错误码 |

## 8. OPTIONS

HTTP 的 OPTIONS 方法用于获取目的资源所支持的通信选项

+ 检测服务器所支持的请求方法

+ CORS 中的预检请求

## 9. HTTP 1.0 / 1.1 / 2 及 HTTPS

原文： https://zhuanlan.zhihu.com/p/43787334

### HTTP/1.0 和 HTTP/1.1 的区别

| 区别 | HTTP/1.0 | HTTP/1.1 |
|-----|-----|-----|
| 缓存处理 | `Pragma: no-cache` + `Last-Modified/If-Modified-Since` | 增加缓存控制策略：`Cache-Control`, `Etag/If-None-Match` |
| 错误状态管理 | - | 新增 24 个错误状态响应码，如 410 (Gone) 表示服务器上的某个资源被永久性删除 |
| 范围请求 | - | 在请求头引入 `range` 头域，允许只请求资源的某个部分，返回码 206 (Partial Content) |
| Host 头 | 认为每台服务器都绑定唯一一个 IP 地址，请求消息中的 URL 没有传递主机名（hostname） | 请求消息和响应消息都支持 Host 头域 |
| 持久连接 | - | 默认开启 `Connection: keep-alive`，TCP 连接默认不关闭，可被多个请求复用（发送 `Connection: close` 关闭 TCP 连接） |
| 管道机制 | - | 在同一个 TCP 连接中，客户端可以同时发送多个请求（可能造成队头阻塞 Head-of-line blocking） |

### HTTP/2

> 不再发布子版本，所以不叫 2.0

HTTP/2 协议只在 HTTPS 环境下才有效，解决了 HTTP/1.1 的性能问题

+ 二进制分帧：HTTP/1.1 的头信息是文本（ASCII 编码），数据体可以是文本，也可以是二进制；HTTP/2 头信息和数据体都是二进制，统称为帧：头信息帧和数据帧

+ 多路复用（双工通信）：通过单一的 HTTP/2 连接发起多重的请求-响应消息，即在一个连接里，客户端和浏览器都可以同时发送多个请求和响应，不用按照顺序一一对应，避免了队头阻塞。HTTP/2 把 HTTP 协议通信的基本单位缩小为帧，并行地在同一个 TCP 连接上双向交换消息

+ 数据流：因为 HTTP/2 的数据包是不按顺序发送的，HTTP/2 将每个请求或回应的数据包，成为一个数据流（stream）。每个数据流有一个唯一的编号，客户端发出的数据流 ID 为奇数，服务器发出的为偶数。通过发送信号（RST_STREAM 帧）取消数据流，但 TCP 连接还是打开的。

+ 首部压缩

+ 服务端推送

### HTTPS

HTTPS 是安全版的 HTTP，HTTPS 在传统的 HTTP 和 TCP 之间加了一层 SSL/TLS。HTTP 默认 80 端口，HTTPS 默认 443 端口

SSL/TLS 协议解决了三大风险：

+ 信息加密传输 解决了 窃听风险（获取通信内容）

+ 校验机制 解决了 篡改风险（修改通信内容）

+ 身份证书 解决了 冒充风险（冒充他人通信）

HTTPS 特点：

+ 缓存：只要在 HTTP 头部使用特定命令，就可以缓存 HTTPS

+ 延迟：HTTP 耗时 = TCP 握手；HTTPS 耗时 = TCP 握手 + SSL 握手。SSL 握手耗时大概是 TCP 握手耗时的三倍

## 10. Web 性能优化

+ 减少 HTTP 请求

+ 利用浏览器缓存

+ 代码压缩、混淆

+ HTML 代码结构优化，少用 iframe，减少 DOM 层级结构，减少重排和重绘等

+ 组件分成多个域（2 个较好）

+ 图片懒加载，脚本、数据预加载，图片转 base64

+ 根据实际情况优化，保证首屏加载时间

## 11. 事件冒泡及捕获
