# 网络基础-HTTP

## 一 简单的 Node Web 程序

简单的 Web 服务器示例：

```js
var http = require("http");

var server = http.createServer(function (req, res) {
  res.writeHead(200, { "Content-Type": "text/plain;charset=UTF8" });
  res.end("hello world");
});

server.listen(8000);
```

以上简单的几行代码就能开启一个 web 服务，访问 <http://localhost:8000> ，即可看到页面输出 8000 端口。

Node 与传统的 web 开发最大的不同是，Node 代码不需要 Web 容器来运行：

```txt
Java：  Java开发的 Web 软件，需要先将源码打包为 war 包，然后将 war 包放置在其专用 web 服务器 Tomcat 中，由 Tomcat 来运行。
Php：   Php开发的 Web 软件，需要将源码放置在其专用的 web 服务器 Apache 中，由 Apache 来运行。
```

## 二 http 模块

### 2.1 createServer

`http.createServer()` 方法返回的是 http 模块封装的一个基于事件的 http 服务器。

`req`，`res` 分别是 http.IncomingMessage 和 http.ServerResponse 的实例。

http.Server 的主要事件有：

- request：客户端发起请求时，被处罚，提供 req，res 参数
- connection：TCP 建立连接时候处罚，提供一个 scoket 参数，是 net.Socket 的实例。
- close：服务器关闭时，触发。

http.createServer()方法其实就是添加了一个 Reuqest 事件监听，如下所示：

```js
var http = require("http");

var server = http.createServer();

server.on("error", function (err) {
  console.log(err);
});

server.on("request", function (req, res) {
  console.log("有用户请求了");
  console.log(req);
});

server.listen(8081, function () {
  console.log("server run...");
});
```

### 2.2 http 模块常见 api

`http.IncomingMessage` 是 http 请求信息，提供了 3 个事件：

- data：当请求数据到来时触发；
- end：当请求体数据传输完毕时候触发；
- close：当用户请求结束时候触发。

`http.IncomingMessage` 提供的属性有：

- method：请求方式
- headers：请求头
- url：请求路径
- httpVersion：http 版本

`http.ServerResponse` 是返回客户端的信息，主要方法有：

- `res.writeHead(statusCode,[headers];`：向请求的客户端发送响应头
- `res.write(data,[encoding]);`：向请求发送内容
- `res.end([data],[encoding);`：结束请求
