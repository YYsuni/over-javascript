## 浏览器 eventloop

浏览器中有 2 个非常重要的线程：JS 解析线程、UI 绘制线程，二者不能同时运行，因为二者如果操作同一个 DOM 则会出现渲染的异常。

下面的代码：

```js
console.log('aaa')

setTimeout(() => {
  console.log(111)
})

console.log('bbb')

setTimeout(() => {
  console.log(222)
})

console.log('ccc')

setTimeout(() => {
  console.log(333)
})

console.log('ddd')
```

其执行结果：

```txt
aaa
bbb
ccc
ddd
111
222
333
```

执行原理图：

![eventloop](./eventloop1.svg)

注意：上述的定时器在栈中其实是直接执行了定时器本身，只有其回调函数才是等到**时间到了之后**进入回调队列！

## 宏任务与微任务

在队列中，还有宏任务与微任务的区别，一般情况下微任务会优先于宏任务执行：

```js
console.log('aaa')

setTimeout(() => {
  console.log(111)
})

console.log('bbb')

Promise.resolve().then((data) => {
  console.log(222)
})

console.log('ccc')
```

执行结果：

```txt
aaa
bbb
ccc
222
111
```

其原理是：
![eventloop](./eventloop2.svg)

那么如果二者进行了混合：

```js
console.log('aaa')

setTimeout(() => {
  console.log(222, '-t1')
  Promise.resolve().then((data) => {
    console.log(222, '-p1')
  })
})

console.log('bbb')

Promise.resolve().then((data) => {
  console.log(333, '-p2')
  setTimeout(() => {
    console.log(333, '-t2')
  })
})

console.log('ccc')
```

执行结果：

```txt
aaa
bbb
ccc
333 -p2
222 -t1
222 -p1
```

注意：其执行其实是：先清空微任务队列，然后每执行一个宏任务，就会重新再去清空下微任务队列

贴士：Node 旧版与浏览器的循环有少许不同。

常见微任务：Promise、MutationObserve、MessageChannel
常见宏任务：setImmediate（IE 浏览器 才拥有）、setTimeout

阻塞与非阻塞：指调用方，如用户发起了请求之后，调用方是客户端，客户端可以等待，也可以不等待，即阻塞、非阻塞
同步与异步：指被调用方，如用户发起了请求之后，被调用方是服务端，服务器可以使用同步的方式返回消息，也可以使用异步的方式返回消息。

贴士：如果服务端采用异步方式返回消息，客户端既可以继续一直等待（阻塞），也可以选择执行其他任务（非阻塞）
