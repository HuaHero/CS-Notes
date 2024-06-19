# 1.WebSocket的基本概念
WebSocket是一种网络通信协议，很多高级功能都需要用到它。

# 2. 为什么需要WebSocket？
初次接触 WebSocket 的人，都会问同样的问题：我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？
答案很简单，因为 HTTP 协议有一个缺陷：通信只能由客户端发起。
举例来说，我们想了解今天的天气，只能是客户端向服务器发出请求，服务器返回查询结果。HTTP 协议做不到服务器主动向客户端推送信息。

这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用"轮询"：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。
轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开（HTTP长连接））。因此，工程师们一直在思考，有没有更好的方法。WebSocket 就是这样发明的。

**一句话： WebSocket是一种全双工的通信，相对于HTTP这种单工通信的方式，解决需要全双工通信的场景需求。**

# 3.WebSocket的基础知识
WebSocket 协议在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。
它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。websocket是一种全新的协议，协议名为"ws"。
![lQLPJwygMTQM3xnNAezNAl2wD4lIfhmO8PAGXIW2ifwuAA_605_492](https://github.com/HuaHero/CS-Notes/assets/2776844/cfd9b458-3a27-42a7-a964-2b0e40657255)

其他特点包括：
（1）建立在 TCP 协议之上，服务器端的实现比较容易。
（2）与 HTTP 协议有着良好的兼容性。**默认端口也是80和443，并且握手阶段采用 HTTP 协议**，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
（3）数据格式比较轻量，性能开销小，通信高效。
（4）可以发送文本，也可以发送二进制数据。
（5）没有同源限制，客户端可以与任意服务器通信。
（6）协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。

# 4.应用举例
XX司API协议对接（HTTP协议），主要目的是相机和平台建立websocket双向通信，平台侧能够使用LAPI协议主动获取相机侧的数据信息，相机侧也可以以LAPI的格式类型推送主动推送给平台；
两者以websocket创建连接，后续使用LAPI协议进行数据获取和推送，对于内部产品的对接提供了便利；对于外部产品，使用提供的协议文档同样可以对接我司设备，免除了我们对接外部产品的工作量，如下图报文数据即websocket协议的交互。
![lQLPJw-cKuOJCdnMg80CXbAcjGulSPjiQAZchrKvrJ4A_605_131](https://github.com/HuaHero/CS-Notes/assets/2776844/92277af6-dfdf-4256-9504-3729a4e7510f)

* WS和HTTP的消息头与交互过程（以注册认证协议举例），设备向服务器发起注册，伪代码如下：
 ![lQLPJwncmuOlQPnMp80CXbC8gcupW0Q84wZch5MiJcUA_605_167](https://github.com/HuaHero/CS-Notes/assets/2776844/e36bd3dd-89f0-4fb8-88a5-e739d675fd60)
DeviceType:设备类型，
DeviceCode: 设备序列号
**Algorithm：签名算法**
**Nonce: 随机字符，每次发起随机不同**
以上这些都是协议字段内容，**根据不同的协议均可更新修改**。
注册报文示例：
![lQLPKHrNz7CtvHnM0M0CXbBLCKF4E9MKdwZch6C32vMA_605_208](https://github.com/HuaHero/CS-Notes/assets/2776844/631608f6-748c-4f96-9168-9f3dd0b758cc)
响应报文示例：
![lQLPJwhDVSkNrDl8zQJdsDZArAW-wQMOBlyH0CBstAA_605_124](https://github.com/HuaHero/CS-Notes/assets/2776844/20923a05-04b3-4d29-b145-ff89485e1c26)

onnection:Upgrade必须设置为Upgrade，表示客户端希望连接升级
Upgrade:websocket必须设置为WebSocket，表示在取得服务器响应之后，使用HTTP升级将HTTP协议转换(升级)为WebSocket协议。
Sec-WebSocket-key:随机字符串，用于验证协议是否为WebSocket协议而非HTTP协议
Sec-WebSocket-Version:表示使用WebSocket的哪一个版本。
Sec-WebSocket-Accept:根据Sec-WebSocket-key和特殊字符串计算。验证协议是否为WebSocket协议（Accept是根据请求中的Key生成的，将其与一个固定字符串进行拼接后，使用SHA1算法得到hash后，再进行base64编码）。
Sec-WebSocket-Location:与Host字段对应，表示请求WebSocket协议的地址。
HTTP/1.1 101 Switching Protocols:101状态码表示升级协议，在返回101状态码后，HTTP协议完成工作，转换为WebSocket协议。此时就可以进行全双工双向通信了。
以上为注册标准格式，后面注册成功后，可以只发具体的Lapi协议。

# 5. 个人认为核心知识点&HTTP或TCP等：
 1. 连接建立过程
 2. 连接释放过程
 3. 连接生命周期

# 6. 参考链接
* How to Use WebSockets（http://cjihrig.com/blog/how-to-use-websockets/）
* WebSockets - Send & Receive Messages（https://tutorialspoint.com/sebsockets/websockets_send_eceive_messages.htm）
* Introducing WebSockets: Bringing Sockets to the Web（https://www.html5rocks.com/en/tutorials/websockets/basics/）







