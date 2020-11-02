## 二 path 模块

API 地址：<http://nodejs.cn/api/path.html>

该模块用于处理路径中常见的问题，如拼接路径时候，需要考虑兼容不同系统的分隔符（win 中的'\'，linux 中的'/'），又比如 2 个路径在拼接时，需要考虑前一个路径末尾是否带分隔符。有了 path 模块，默认会处理这些问题：

连接路径：

```js
const path = require("path");

// 返回: '/foo/bar/baz/asdf'
path.join("/foo", "bar", "baz/asdf", "quux", "..");

let myurl = path.join(__dirname, "username", "123");
console.log(myurl); //输出类似这样的绝对路径: /Test/username/123
```

\_\_dirname 和 path.dirname 的区别:

```js
console.log(__dirname); // 指向被执行 js 文件的绝对路径,包含的有文件名称
console.log(path.dirname("/abc/www/abc.txt")); // 只有路径，没有文件名称
```

获取路径的最后一部分：

```js
path.basename("/foo/bar/aaa.html"); // 得到的结果是： aaa.html

path.basename("/foo/bar/aaa.html", ".html"); // 得到的结果是： aaa
```

获取扩展名:

```js
// 如果后面只是个‘.’，那么得到的就是‘.’； 如果没有扩展名，得到的就是空
path.extname("index.html"); // 得到的结果： ‘.html’
```

路径的格式化处理：

```js
let obj = {
  root: "d:\\",
  base: "abc.txt",
  ext: ".txt",
  name: "abc",
};

console.log(path.format(obj)); //  输出的结果：d:\abc.txt
```

路径字符串转成对象

```js
    console.log(path.parse(__filename));
   //  输出的结果：
    {
        root: '文件根路径',
        dir: '文件的全路径',
        base: '文件名称',
        ext: '扩展名',
        name: '文件名称'
    }
```

## 三 url 模块

URL 的组成如下：

```txt
┌────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                              href                                              │
├──────────┬──┬─────────────────────┬────────────────────────┬───────────────────────────┬───────┤
│ protocol │  │        auth         │          host          │           path            │ hash  │
│          │  │                     ├─────────────────┬──────┼──────────┬────────────────┤       │
│          │  │                     │    hostname     │ port │ pathname │     search     │       │
│          │  │                     │                 │      │          ├─┬──────────────┤       │
│          │  │                     │                 │      │          │ │    query     │       │
"  https:   //    user   :   pass   @ sub.example.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          │  │          │          │    hostname     │ port │          │                │       │
│          │  │          │          ├─────────────────┴──────┤          │                │       │
│ protocol │  │ username │ password │          host          │          │                │       │
├──────────┴──┼──────────┴──────────┼────────────────────────┤          │                │       │
│   origin    │                     │         origin         │ pathname │     search     │ hash  │
├─────────────┴─────────────────────┴────────────────────────┴──────────┴────────────────┴───────┤
│                                              href                                              │
└────────────────────────────────────────────────────────────────────────────────────────────────┘
```

Node 中 ulr 模块用来处理 URL 总的各个字段：

```js
const url = require("url");
const myURL = url.parse(
  "https://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash"
);
```

但是最新的 H5 标准组织已经提出了 URL 操作的标准，新版的 Node 也开始支持：

```js
const myURL = new URL("https://%CF%80.example.com/foo");

console.log(myURL.href);
```

## 四 querystring 模块

该模块有 2 个主要方法：

- `querystring.parse()`：将查询字符串反序列化为一个对象，类似`JSON.parse()`
- `querystring.stringify()`：将一个对象序列化为一个字符串对象，类似`JSON.stringify()`

```js
const querstring = require("querystring");

let str1 = "username=lisi&password=123";
let obj1 = querstring.parse(str1); //  转换为了对象

let obj2 = { username: "zs", password: "456" };
let str2 = querstring.stringify(obj2); //  重新转换为字符串
```
