## 一 Hello World

### 1.1 koa 简介

koa 是 express 团队打造的更加轻量级的 web 框架，内部没有任何功能模块封装，源码仅仅只有几千行。koa1 中使用了 ES6 的 promise，yield 语法，koa2 使用了 ES7 的 async await 语法，为 node 的回调地狱问题提供了最优的解决方案。

注意：

- web 开发的常见功能，在 koa 中都需要额外安装对应模块，比如路由模块，express 中自带，而 koa 需要安装 koa-router
- koa 的请求、响应对象都被包装进了 context 对象中

### 1.2 koa2 的 hello world

```js
const koa = require("koa");

let app = new koa();

app.use((ctx) => {
  ctx.body = "hello world";
});

app.listen(3000);
```

### 1.3 context 对象

koa 将 node 的 Request 和 Response 对象封装进了 Context 对象中，所以也可以把 Context 对象称为一次对话的上下文。

Context 对象内部封装的常见属性：

```js
app.use((ctx) => {
  ctx; // Context对象
  ctx.request;
  ctx.response;

  this; // Context对象也可以直接写为this
  this.request;
  this.response;
});
```

### 1.4 错误处理

```js
app.use((ctx, next) => {
  next();
  if (ctx.status == 404) {
    ctx.status == 404;
    ctx.body = "404 not found";
  }
});
```

ctx.throw 可以抛出错误：

```js
app.use((ctx) => {
  ctx.throw(500); // 页面会抛出状态码为500的错误页面
});
```

## 二 koa 的中间件

### 2.1 koa 中间件 demo

中间件函数是一个带有 ctx 和 next 两个参数的简单函数。next 用于将中间件的执行权交给下游的中间件。

```js
const koa = require("koa");

let app = new koa();

app.use((ctx, next) => {
  console.log("执行中间件1");
  next();
  console.log("next之后的代码1");
  ctx.body = "hello world2";
});

app.use((ctx, next) => {
  console.log("执行中间件2");
  ctx.body = "hello world2";
  next();
  console.log("next之后的代码2");
});

app.listen(3000);
```

我们能看到执行结果是：按照顺序执行了中间件代码，再按反方向执行一遍 next 之后的代码，web 界面也输出的是 hello world2

```
执行中间件1
执行中间件2
next之后的代码2
next之后的代码1
```

上述的执行方式，我们称之为杨冲模型：
![](/images/node/yangchong.png)

koa 中间件与 Express 的不同：

- Express 中的中间件是按照顺序执行的；
- koa 中的中间件无论写在什么位置，都会先执行。因为 Koa 的中间件是洋葱模型！

### 2.2 koa-compose 组合中间件

如果需要将中间件组合使用，可以使用 koa-compose

```js
function middleware1(ctx, next) {
  console.log("midlle1...");
  next();
}

function middleware2(ctx, next) {
  console.log("midlle2...");
  next();
}

const all = compose([middleware1, middleware2]);

app.use(all);
```

### 2.3 koa 常用中间件

- koa-bodyparser:获取 POST 请求参数
- koa-router:路由中间件
- koa-static:静态文件目录
- koa-views:加载模板文件

综合案例：

```js
const koa = require("koa");
const path = require("path");
const ejs = require("ejs");
const views = require("koa-views");
const bodyParser = require("koa-bodyparser");
const static = require("koa-static");
const Router = require("koa-router");
const favicon = require("koa-favicon");

const app = new koa();
const router = new Router();

//加载静态资源
app.use(static(path.join(__dirname, "static")));

//favicon
app.use(favicon(__dirname + "/static/favicon.ico"));

// 加载ejs模板引擎:ejs后缀方式
// app.use(views(path.join(__dirname, './views'), {
//     extension: 'ejs'
// }));

// 加载ejs模板引擎:html后缀方式
app.use(
  views(path.join(__dirname, "views"), {
    map: { html: "ejs" },
  })
);

//post解析中间件
app.use(bodyParser());

//路由->渲染模板
router.get("/", async (ctx, next) => {
  await ctx.render("test", {
    msg: "hello",
  });
});
router.post("/", (ctx, next) => {
  let data = ctx.request.body;
  console.log(JSON.stringify(data));
  ctx.body = data;
});

app.use(router.routes()); //启动路由中间件
app.use(router.allowedMethods()); //根据ctx.status设置响应头

//支持链式写法
// app.use(router.routes()).use(router.allowedMethods());

app.listen(3000);
```

### 2.4 Koa 中间件源码阅读

koa 的中间件：

中间件的本质是一个函数，在 koa 中，该函数通常具有 ctx 和 next 两个参数，分别表示封装好的 req/res 对象以及下一个要执行的中间件，当有多个中间件的时候，本质上是一种嵌套调用，就像洋葱。

koa 和 express 都是用 app.use()来加载中间件，但是内部实现大不相同，koa 的源码文件 Application.js 中定义了 use 方法：

```js
use(fn){
    //....
    this.middleware.push(fn);
    return this;
}
```

koa 在 application.js 中维护了一个 middleware 的数组，如果有新的中间件被加载，就 push 到这个数组中。
