# 04-2-Vue-Router 基础使用

## 一 路由常见配置

### 1.1 路由模式

路由模式有两种：

- hash 模式：默认的 vue 路由模式，地址为需要有#号："#/home"
- history 模式

如下所示：

```js
let router = new VueRouter({
  mode: "history", // 默认是hash，此时设置为history模式
  routes: [
    {
      path: "/home",
      component: Home,
    },
  ],
});
```

### 1.2 路由别名

路由中的 name 属性是个可选项，作用是给路由起个名字。

name 的使用场景一：

```js
let router = new VueRouter({
  routes: [
    {
      path: "/home",
      component: Home,
      name: "Home", // 可选项
      alias: "/index", // 别名：访问/index渲染Home
    },
    {
      path: "*",
      redirect: { name: "Home" },
    },
  ],
});
```

name 的使用场景二：

```html
<router-link :to="{name:'路由的名字'}"></router-link>
```

### 1.3 路由重定向

重定向设置：

```js
let router = new VueRouter({
  routes: [
    {
      path: "/home",
      component: Home,
      name: "Home",
      alias: "/index",
    },
    {
      path: "*", // 配置在最后，当所有路由都未被匹配到则如何处理
      // component: Error,          // 可以直接调转到错误提示页

      // redirect会替换掉浏览器中的地址，alias不会
      // redirect: '/index'         // 也可以配置重定向
      // redirect: {path: '/home'} // 重定向方式二
      // redirect: {name: 'Home'}   // 重定向方式三，name是路由的名字
      redirect: (to) => {
        // 重定向方式四,动态设置重定向的目标；
        //     console.log(to);       // to目标路由对象，就是访问的路径的路由信息
        //     return '/home'         // return值也可以写为  {path:} 或 {name:}

        //除了可以直接return重定向的路由外，还可以通过 path\hash\query 等判断，动态设置重定向的目标路由：

        if (to.path === "/123") {
          return "/home";
        } else if (to.path === "/456") {
          return { path: "/document" };
        } else {
          return { name: "about" };
        }
      },
    },
  ],
});
```

### 1.4 动态路由

路由中直接设定参数：

```js
routes: [{ path: "/user/:uid", component: User }];
```
