# Node 基本使用

## 一 REPL 环境

Node 默认提供了一个命令环境用来基础使用，类似 Chrome 浏览器中的 Console 模块：

```txt
# 打开命令行窗口，输入 node 命令，即可进入 REPL 环境
node

# 输入
1+1

# 回车后界面将会显示
2
```

## 二 Node 执行 JS 文件

REPL 环境过于简单，Node 是可以直接运行 JS 文件的，创建一个 `app.js` 文件，内容如下:

```js
console.log(1 + 1);
```

在学习 Node 之前，该 JS 文件只能嵌入在 HTML 文档中，由浏览器来运行，现在 Node 命令可以直接运行：

```txt
node app.js                 # 可以省略 .js 后缀

# 输出2
```

## 三 全局对象

### 3.0 Node 全局对象汇总

为了方便操作，Node 默认提供了一些全局对象，这些对象的使用无需 `手动导入`，在任何地方都可以直接使用：

- `console`：控制台输入输出对象
- 定时器：如 `setTimeout`,`setTimeInterval`
- `global`：用于挂载全局变量
- `process`：用于获取系统运行信息
- `buffer`：缓存模块
- `stream`: 流模块
- `exports`：用于支持模块导出
- `module`：用于支持文件模块化
- `require`：该函数用于导入模块

### 3.1 全局对象 global

在原生 js 中定义全局变量，浏览器中可以通过 window 顶层对象访问（即全局变量是顶层对象的属性）：

```js
let a = 100;
console.log(window.a);
```

Node 没有 window 这个顶层对象，Node 自身的顶层对象是 global，定义全局变量方式：

```js
a = 3; // 第一种定义全局变量方式，如果使用var a = 3; global是无法访问到的。
global.b = 3; // 第二种定义全局变量方式

console.log(a);
console.log(b);
```

### 3.2 console

console 与 浏览器中的 console 用法一致，但是二者却不是同一种实现，Node 中的 console 借助了操作系统来实现。

常见用法如下：

```js
console.log(); // 打印信息
console.info(); // 返回信息性质的信息，控制台会显示蓝色惊叹号
console.error(); // 打印错误信息，控制台会显示红色叉子
console.dir(); // 对对象进行检查，并显示
console.time(); // 计时开始
console.timeEnd(); // 计时结束
console.trace(); // 输出当前代码在堆栈中调用路径
```

### 3.3 定时器

Node 中的定时器与浏览器中的定时器用法一致，这里不做过多介绍。

## 四 模块属性 `__filename` `__dirname`

`__filename` 和 `__dirname` 是常用的 2 个属性，但却并不是全局的，他们挂载到了每个模块上(模块机制章节讲解)，所以每个模块都可以直接使用：

- `__filename`：返回当前模块的文件的解析后的绝对路径（包含文件名）
- `__dirname`：返回当前模块文件所在目录解析后的绝对路径（不包含文件名），返回的是文件夹

示例：

```js
console.log(__dirname);
```
