## 一 中间件

中间件是在管道中执行的，在 Express 中，使用 app.use()向管道中插入中间件。

中间件讲究顺序，匹配上第一个之后，就不会往后匹配了，next 函数才能够继续往后匹配。

模糊意义上讲，app.get() app.post()等方法也属于中间件。

app.use()也是一个中间件。与 get、post 不同的是，他的网址不是精确匹配的，使用 user(path,fn())时候，user 内的路由，将匹配/path /path/images/ /path/images/1.png 等路由情况。

如果写一个 / 实际上就相当于"/"，就是所有网址，也可以直接不写该地址。

大坑：express 的中间件函数，不需要传入 req，res，他是在中间件函数执行回调的时候自动传入。创建中间件：

```JavaScript
module.exports = function (req, res, next) {
    //中间件函数在这里调用
    next();     //记得使用next()或者 next(route)
};
```

使用中间件：

```JavaScript
const test2 = require('./test1');
app.use(test2);

app.get('/',function (req, res) {//每次访问/调用一次中间件
    res.send("hello world!");
});


```

创建可配置的模块化中间件：

```JavaScript
module.exports = function (config) {
    if (!config) config= {}
    return function (req, res, next) {
        next();     //除非这个中间件是终点，否则需要next
    }
};
```

输出多个相互关联的中间件：

```JavaScript
module.exports = function (config) {
    if (config)  config= {}
    return {
        m1: function (req, res, next) {
            next();     //除非这个中间件是终点，否则需要next
        },
        m2: function (req, res, next) {
            next();     //除非这个中间件是终点，否则需要next
        }
    }
};
```

使用互相关联的中间件：

```JavaScript
const test2 = require('./test1')({option:'test'});
app.use(test2.m1);
app.use(test2.m2);

```

也可以将处理函数挂载在一个对象的原则上：

```JavaScript
//创建中间件
function Stuff(config) {
    this.config = config || {};
}

Stuff.prototype.m1 = function (req, res, next) {
    //注意这里的this最好别用
};
module.exports = Stuff;
//使用中间件
const test2 = require('./test1');
let stuff = new test2({option:"test"});
app.use(stuff.m1);
```


## 一 中间件概念

在业务系统中，往往接口都必须经过一些特殊方法进行处理后，才会执行下一步动作。而当多个接口都要应用到这些处理时，每个接口都书写一遍显然是不合适的。我们可以利用类似 Java 的 filter 原理，在 Node 中也制作类似的方法，让路由在执行控制器方法之前先执行这些通用方法。这些通用方法即是中间件。

由于中间件往往需要对请求的上下文，即请求对象 req、响应对象 res，进行一定的封装处理，所以中间件函数必须具备这 2 个参数，而且中间件也必须提供一种机制，在当前中间处理完成后，通知下一个中间件执行，在 Node 框架 Connect 中，通过尾触发方式实现了中间件：

```js
function middleware(req, res, next) {
  // 业务代码
  next();
}
```

## 二 中间件设计

### 2.1 中间件的使用

常见的场景是鉴权，比如有的页面需要登录才能访问，有的页面无需登录，如果现在封装一个是否登录的中间函数 isLogin()，那么在实际开发中的使用方式是：

```js
// 需要登录
app.get("/user/:username", isLogin, function (req, res) {});

// 不需要登录
app.get("/news/:date", isLogin, function (req, res) {});
```

isLogin 中间件的写法：

```js
function isLogin(req, res, next) {
  var cookie = req.headers.cookie;
  var cookies = {};

  if (!cookie) {
    console.log("cookie不存在");
    return;
  }

  if (!cookie.isLogin) {
    console.log("用户未登录");
    return;
  }

  next();
}
```

### 2.2 中间件的开发

中间件和具体业务应该都是不同的业务处理单元，现在改进上一节中 use 方法：

```js
app.use = function(){
    var handle = {
        path: pathR egexp(path),            // 第一个参数作为路径
        stack: Array.prototype.slice.call(arguments, 1)     // 其他的都是处理单元
    };
    routes.all.push(handle);
}
```

改进后的 use()方法都将中间件存进了 stack 数组中，等待匹配后触发执行，匹配部分修改如下：

```js
function match(pathname, routes) {
  for (var i = 0; i < routes.length; i++) {
    var route = routes[i];
    var reg = route.path.regexp;
    var matched = reg.exec(pathname);

    if (matched) {
      handle(req, res, route.stack);
      return true;
    }
  }

  return false;
}
```

一旦匹配成功，中间件具体如何调用都交给了 handle()方法处理，该方法封装后，递归性的执行数组中的中间件，每个中间件执行完成后，按照约定调用传入 next()方法触发下一个中间执行：

```js
function handle(req, res, stack) {
  function next() {
    // 从stack数组中取出中间件并执行
    var middleware = stack.shift();
    if (middleware) {
      // 传入next()函数自身，使中间件能够执行结束后递归
      middleware(req, res, next);
    }
  }
  // 启动执行
  next();
}
```

### 2.3 通用中间件

有的中间件要在所有路由中触发，如果每个路由都要书写一遍该中间件，显然是不合理的，用户期望的使用方式是：

```js
app.use(isLogin);
```

现在修改 use()方法，以实现更灵活的适应参数变化：

```js
app.use = function (path) {
  var handle;

  if (typeof path === "string") {
    handle = {
      // 第一个参数作为路径
      path: pathRegexp(path),
      // 其他的都是处理单元
      stack: Array.prototype.slice.call(arguments, 1),
    };
  } else {
    handle = {
      // 第一个参数作为路径
      path: pathRegexp("/"),
      // 其他的都是处理单元
      stack: Array.prototype.slice.call(arguments, 0),
    };
  }
  routes.all.push(handle);
};
```

改进匹配过程，之前的匹配是一旦一次匹配后就不再执行，现在需要将所有匹配到的中间都暂时保存起来：

```js
function match(pathname, routes) {
  var stacks = [];

  for (var i = 0; i < routes.length; i++) {
    var route = routes[i];
    var reg = route.path.regexp;
    var matched = reg.exec(pathname);
    if (matched) {
      // 抽取具体值.....将中间件保存
      stacks = stacks.concat(route.stack);
    }
  }

  return stacks;
}
```

高进分发过程：

```js
function (req, res) {

    var pathname = url.parse(req.url).pathname;
    var method = req.method.toLowerCase();

    // 获取all()方法里的中间件
    var stacks = match(pathname, routes.all);

    if (routes.hasOwnPerperty(method)) {
        // 根据请求方法分发，获取相关的中间件
        stacks.concat(match(pathname, routes[method]));
    }

    if (stacks.length) {
        handle(req, res, stacks);
    } else {
        // 处理404请求
        handle404(req, res);
    }
}
```

### 2.4 异常处理

中间件自身也可能出现错误，所以必须为 next()方法添加 err 参数，捕获异常：

```js
function handle(req, res, stack) {
  function next(err) {
    if (err) {
      return handle500(err, req, res, stack);
    }

    // 从stack数组中取出中间件执行
    var middleware = stack.shift();
    if (middleware) {
      // 传入next()函数自身，使中间件能够执行结束后递归
      try {
        middleware(req, res, next);
      } catch (ex) {
        next(err);
      }
    }
  }

  // 启动执行
  next();
}
```

注意：异步方法的异常无法直接捕获，中间件异步产生的异常需要自己传递出来：

```js
function isLogin() {
  var uid = req.cookie.session_id;

  // 假设是个异步方法，产生了错误
  store.get(uid, function (err, session) {
    if (err) {
      next(err);
      return;
    }

    req.session = session;
    next();
  });
}
```

所以异步的中间件往往有四个参数：

```js
function middleware(err, req, res, next) {
  // ...
  next();
}
```
