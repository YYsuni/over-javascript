# 02-BOM 常见对象

## 一 location 对象

### 1.1 location 基础使用

location 用户获取、设置窗体的 URL，以及解析 URL。

注意：location 对象既是 window 对象的属性，也是 document 对象的属性，即 window.location 和 document.location 引用的是同一个对象。

```js
location.href; // "http:/www.wrox.com"
location.pathname; // /user/order
location.search; // "?name=lisi"
location.assign(); // 重定向
location.replace(); // 不记录历史，直接替换当前页面
location.reload(); // 重载页面，即强制刷新
```

### 1.2 解析查询参数

```js
function urlQuery() {
  //取得查询字符串并去掉开头的问号
  let qs = location.search.length > 0 ? location.search.substring(1) : "",
    //保存数据的对象
    args = {},
    //取得每一项
    items = qs.length ? qs.split("&") : [],
    item = null,
    name = null,
    value = null,

  //逐个将每一项添加到 args 对象中
  for (let i = 0; i < items.length;; i++) {
    item = items[i].split("=");
    name = decodeURIComponent(item[0]);
    value = decodeURIComponent(item[1]);
    if (name.length) {
      args[name] = value;
    }
  }
  return args;
}
```

### 二 navigator 对象

navigator 对象用于标识浏览器：

```js
navigator.appCodeName; // 浏览器名称，通常为Mozilla
navigator.userAgent; // 用户代理字符串
```

## 三 history 对象

history 对象保存的是网页浏览器历史记录：

```js
//后退一页
history.go(-1);
//前进一页
history.go(1);
//前进两页
history.go(2);

//跳转到最近的 wrox.com 页面
history.go("wrox.com");
//跳转到最近的 nczonline.net 页面
history.go("nczonline.net");

//后退一页
history.back();
//前进一页
history.forward();
```
