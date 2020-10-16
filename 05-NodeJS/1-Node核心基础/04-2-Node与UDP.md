
# UDP 编程

## 一 Node与UDP编程简介

TCP 数据传输是一种可靠的数据传输方式，在数据传输之前必须建立客户端与服务端之间的连接，而 UDP 是一种面向非连接的协议，所以其传输速度比 TCP 更加快速，其名称为：用户数据报协议。

Node 使用 dgram 模块中的 createSocket() 方法创建一个 UDP 服务器，这个方法接收一个必须参数和一个可选参数，必须参数是一个标识 UDP 协议的类型，可指定为 udp4 或者 udp6，可选参数是一个回调函数，即 UDP 服务器接收数据时触发的回调函数，回调函数有 2 个参数，一个是接收的数据，一个是存放发送者信息的对象。

```js
const dgram = require('dgram');

const socket = dgram.createSocket('udp4', (msg,rinfo) => {
    // code
});

socket.bind(3000,'localhost',() => {
    console.log('bind 3000....');
});
```

## 二 UDP 编程示例

### 2.1 UPD 服务端

```js
const dgram = require('dgram');
const server = dgram.createSocket('udp4');

server.on('error', (err) => {
  console.log(`server error:\n${err.stack}`);
  server.close();
});

server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});

server.on('listening', () => {
  const address = server.address();
  console.log(`server listening ${address.address}:${address.port}`);
});
```

这里与 TCP 的 createServer 一样，返回的都是 socket 对象，且回调函数就是监听 message 事件，因此使用 createServer 也可以不指定回调函数，直接显示的监听 message 事件就可以。

### 2.2 UDP 客户端

```js
var dgram = require('dgram');

var message = new Buffer('msg from client');

var socket = dgram.createSocket('udp4');

socket.send(message,0,message.length,3000,'localhost',function (err,bytes) {
    if(err){
        console.log(err);
        return;
    } else {
        console.log('client send ' + bytes + 'message')
    }
});

socket.on('message',function (msg,rinfo) {
    console.log('msg from server');
});
```

### 三 UDP 协议

UDP 的全程是用户数据报协议，与 TCP 协议在网络协议中都位于传输层，是无连接协议。

UDP 不提供数据包分组、组装，不能对数据包进行分排序，所以当 UDP 报文发送后，无法得知其是否已经完全安全到达。

UDP 的主要作用是将网络数据流量压缩成数据包形式，一个典型的数据包就是一个二进制数据的传输单位，每一个数据包的前8个字节用来包含报头信息，剩余字节用来包含具体的传输数据。

UDP 协议的主要应用场合是视频会议、聊天等场合。
