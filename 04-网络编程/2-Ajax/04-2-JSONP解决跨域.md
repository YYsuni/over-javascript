# 04-2-JSONP 解决跨域

## 一 方案一：JSONP

script 标签可以获取不同域下的文件，比如使用 script 标签引入 cdn 上的 jquery 文件就是利用了 script 标签的跨域功能：

`<script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>`。

利用 script 标签的该特性进行跨域请求。

这时候如果直接使用 script 标签还会遇到问题：拿到的数据无法使用。

假设返回的数据是：

```txt
"{"uid":"1001"}"
```

通过 script 标签引入了该数据，但是这个数据没有变量接收，在脚本中也就无法使用了！为了解决该问题，必须让服务端进行配合，如果服务端返回的数据是：``

```txt
"let user = {'uid':'1001'}"
```

这样的结果在脚本中就可以直接使用了！

```html
<script src="http://localhost:3000/crosDemo"></script>
<script>
  console.log("跨域示例：", user);
</script>
```

但是这时候我们需要防止 script 标签的异步加载问题，而且要注意顺序问题。而且每次页面都自动加载了这个跨域请求是不可控的，显然不符合真实开发要求。

我们一般动态的创建 script 标签然后来获取数据，但是新的问题产生了：请求是异步的，无法在创建标签后直接就能获得数据，所以必须让服务端返回一个回调函数：

```js
// 服务端代码
let data = JSON.stringify({ uid: "1001" });
res.send(`cb(${data});`);

// 客户端代码：定义返回数据后的回调函数，名称必须写上一致，这里是 cb
function cb(data) {
  console.log("jsonp 数据：", data);
}

let btn = document.querySelector("#btn");
btn.onclick = function () {
  // 创建 script 跨域请求的标签
  let script = document.createElement("script");
  script.src = "http://localhost:3000/crosDemo2";
  document.querySelector("head").appendChild(script);

  // 跨域得到的结果是： cb("数据")，自动定义好的cb函数
};
```

为了方便双方互相通信，回调函数名可以通过请求参数进行传递：

```js
// 服务端代码
app.get("/crosDemo2", (req, res) => {
  // 获取回调函数名称
  let callback = req.query.callback;

  // 定义要返回的数据
  let data = JSON.stringify({ uid: "1001" });

  // 返回数据
  res.send(`${callback}(${data});`);
});

// 客户端代码
// 定义返回数据后的回调函数，名称必须写上一致，这里是 cb
function cb(data) {
  console.log("jsonp 数据：", data);
}

let btn = document.querySelector("#btn");
btn.onclick = function () {
  // 创建 script 跨域请求的标签
  let script = document.createElement("script");
  script.src = "http://localhost:3000/crosDemo2?callback=cb";
  document.querySelector("head").appendChild(script);

  // 跨域得到的结果是： cb("数据")，自动定义好的cb函数
};
```

贴士：服务端框架 Express 是直接自持 json 请求的：

```js
app.get("/crosDemo2", (req, res) => {
  res.jsonp({ uid: 1 });
});
```
