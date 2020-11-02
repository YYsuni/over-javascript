# TCP 编程

## 一 TCP 编程简介

### 1.1 TCP 简介

TCP 即传输控制协议，在 OSI 七层模型中属于传输层协议，大多应用层协议都是基于 TCP 构建而来，如：HTTP/SMTP/IMAP 等，可以说 TCP 协议是现代网络编程最重要的基石之一。

TCP 是面向连接的协议，其显著的特征是在传输之前需要进行 3 次握手形成会话：

![三次握手](../../../images/node/tcp-01.svg)

当会话形成后，服务器和客户端之间分别提供一个套接字，这 2 个套接字形成一个连接，服务端和客户端通过套接字实现了二者的连接操作。

### 1.2 TCP 服务单示例

Node 使用 net 模块的 createServer 方法构建 TCP 服务器。

```js
const net = require("net");

const server = net.createServer();

// 创建一个套接字socket，作为参数传递给回调函数
server.on("connection", (socket) => {
  console.log("new connection comming");
});

server.listen(() => {
  console.log("Listen...");
});
```

## 二 TCP 编程示例

### 2.1 TCP 服务端

```js
const net = require("net");

// 创建一个套接字socket，作为参数传递给回调函数
const server = net.createServer((socket) => {
  // socket.end("world\n");
  socket.write("world\n");
});

server.on("error", (err) => {
  throw err;
});

server.on("connection", (socket) => {
  console.log("new connection comming...");
});

server.on("listening", () => {
  console.log("listening...");
});

server.on("close", (socket) => {
  console.log("close...");
});

server.listen(3001, () => {
  console.log("Listen on ", server.address());
});
```

### 2.2 TCP 客户端

Node 创建 TCP 客户端只需要创建一个连接 TCP 客户端的 socket 对象即可，示例如下：

```js
const net = require("net");

//新建的client对象（就是socket）用来支持数据交互
const client = new net.Socket();

client.connect(3000, "localhost", () => {
  //运行后输出该句，证明回调已经执行
  console.log("connect...");
  client.write("msg of client"); //发送数据
});

//接收数据
client.on("data", (data) => {
  console.log("the data come from server is " + data.toString());
});
```

创建 client 时候可以传入一个 json 对象，这个 json 对象有以下属性：

```txt
fd：            置顶一个存在的文件描述，默认为null
readable：      是否允许在这个socket上读，默认为false
writeable：     是否允许在这个socket上写，默认为false
allowHalfOpen： 该属性为false时，TCP服务器接收到客户端发送的一个FIN包后将会回发一个FIN包，
                该属性为true时，TCP服务器接收客户端发送的一个FIN包后不会回发FIN包。
```

## 三 了解 TCP 与 Socket

### 3.1 TCP 协议

TCP 是面向连接的，提供端到端可靠的数据流（flow of data）的协议。与其他协议相比，TCP 提供了超时重发、检验数据、流量控制等功能，保证了数据能从一段传输到另一端。

面向连接：在正式收发数据前，必须和对方建立可靠的连接，类似打电话需要先拨号等待应答，再说明自己是谁。这个过程称为三次握手：

```txt
主机 A 向主机 B 发出连接请求数据包：可以发送数据吗？

主机 B 收到信息发送统一连接和要求同步（一个发、一个收）的数据包：可以发送

主机 A 发出确认同步包：好的，同步吧。（三次握手结束，开发正式收发数据）
```

数据可靠性的保持：

```txt
数据会被切割为 TCP 认为最适合发送的数据块，应用程序产生的数据报长度会保持不变。

TCP 发出一个段后，内部启动定时器，等待目的端确认收到这个报文段，如果不能及时收到确认，将重发该报文段（自适应超时、重传策略）。

接收端收到发送端数据后，将会在延迟很短时间后，发送一个确认。

TCP 将保持它首部和数据的检验和，这是一个端到端的检验和，目的是校验传输中的数据变化，如果出现差错，TCP 会丢弃该报文段，并不确认收到该报文段。

IP 层的数据到达可能会失序，在该情况下，TCP 会对接收到的数据进行重新排序。

IP 层的数据会发生重复，TCP 的接收端会丢弃重复的数据。
```

TCP 还会提供流量控制：

```txt
TCP 连接的每一方都有固定大小的缓存空间，TCP 的接收端只允许另一端发送 接收端的缓存区能接纳的数据。这可以防止较快的主机让较慢的主机缓存区溢出。
```

### 3.2 Socket

将 Socket 绑定到一个端口号上，TCP 层才可以标识数据最终要发送到哪个应用程序。Socket 即网络上 运行在两个程序之间双向通信链路的一个端点。

一般场景中，绑定在端口上的 Socket 会一直在等待，监听客户端的连接请求。Socket 是可读可写的双工流。

对于客户端来说，其知道服务端的主机地址与端口号，所以客户端可以与服务端建立连接，此时还要把自己的本地端口告知服务端以让服务端也能识别。

一旦客户端与服务端的 Socket 连接建立了，双方就可以开始数据传输。这时，服务端的 Socket 已经被使用了，必须要再开启一个 Socket，才能接收新的客户端的连接。

贴士：IP 地址与端口号组合为`端点`，端点是 TCP 识别连接的唯一标识。
