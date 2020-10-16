## 一 JavaScript 概述

编程语言有解释型和编译型两种：

- 解释型：源代码无需经过编译，一行一行解析执行，比如 javascript、python
- 编译型：源码编译为可执行文件后，运行可执行文件，比如 C、C++，这些语言往往拥有一个名为 main 的入口函数

JavaScript（以下简写为 js）是一门解释型脚本编程语言，也是当前 Web 开发领域的核心语言，没有之一。

该语言由网景(Netscape)公司(火狐的前身)的`Brendan Eich（布兰登·艾奇）`花费了 10 天时间创建，语言名称中虽然带了 Java，但是和另一门编程语言 Java 没有任何关系。欧洲计算机制造商协会（ECMA 下属 TC39 制定）为 JavaScript 提出了语法规范：ECMAScript。即各家的 JS 脚本语言都要以 ECMAScript 语法规范来开发。

所以通常来说，ECMAScript 才是真正的脚本语言规范，而 JavaScript 只是其一个被大规模使用的实现，JavaScript 比 ECMAScript 规范所定义的内容要多很多，主要包括：

- 核心-ECMAScript：JavaScript 本身必须符合 TC39 制定的一系列脚本语言规范，如：语法、类型、关键字、基本对象等，都与浏览器没有关系！
- 文档对象模型-DOM：JavaScript 还提供了浏览器上的文档操作 API
- 浏览器对象模型-BOM：JavaScript 还提供了浏览器自身的操作 API

提到 JavaScript 时，通常其版本是 ECMAScrip3 和 ECMAScript5，JS（JavaScript 的简称）的当前主要版本有：

- ECMAScript3：1999 年标准规范，火狐的 js1.5 和 1.8 都是基于 3 规范
- ECMAScript4：为了适应互联网发展出来的激进版本，由于存在大量分歧，该版本并未普及
- **ECMAScript5**：2009 年发布，包含了 4 中的一些常见功能，当前的主流版本
- **ECMAScript6**：诞生于 2015 年，也称为 ES2015，是 js 划时代意义的版本。引入了开发大型项目所需要的功能，JS 终于不再是一门`玩具语言`。
- ES2016：诞生于 2016 年，即 ES7，后续版本均以年为名称，如 ES2017、ES2018。ES2016 中引入`async await`异步解决方案。

任何编程语言都需要编程语言引擎来对代码进行解析，从而让代码转换为机器指令（因为计算机只能运行机器指令，不能直接运行代码）。常见的 JS 引擎有：

- v8 引擎：js 最著名的引擎，以高性能著称，被应用于 Chrome 浏览器与 Node.js
- SpiderMonkey：火狐浏览器中的 js 引擎

在编程引擎基础上，第三方会为编程语言开发一些独立的库，对编程语言进行功能上的包装，这些最终包装出来的编程环境，我们称之为运行时！常见的 js 运行时有：

- Chrome 浏览器：JS 引擎为：V8，内置 webkit 排版引擎，提供了 html 文档操作 API
- FireFox 浏览器：JS 引擎为：SpiderMonkey，内置 Gecko 排版引擎，也提供了 html 文档操作 API
- Node.js：JS 引擎为 V8，内置了 Node 的 API，即提供了网络、文件等计算机系统操作 API

## 二 Hello World

`Hello World`程序是 1974 年`Brian Kernighan`撰写的`《Programming in C: A Tutorial》`中首次面向大众介绍 C 语言时使用的最简单程序示例。后来该习惯相继被大量编程语言沿用。

本笔记中的 JavaScript 代码大多都依赖于 Chrome 运行时，故而我们需要一个的 HTML 文件来运行 JS 代码。

编写 js 版`Hello World`，创建 helloworld.html 文件，代码如下：

```txt
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <script>
      // 这里开始书写js代码
      console.log("Hello World!");
    </script>
  </body>
</html>
```

用浏览器打开该网页，按 F12 打开控制台，控制台将会输出：`Hello World!`。

## 三 script 标签

JavaScript 的代码要被书写于脚本标签中，但是 HTML5 和 HTML4 的脚本标签规范不尽一致：

- HTML4 脚本标签：`<script type="application/javascript"> </script>`
- HTML5 脚本标签：`<script> </script>`

贴士：本章节均使用较新的 HTML5 规范。

## 四 JavaScript 一些常见使用

### 4.1 注释

js 中的注释：

```js
//  单行注释

/*
 *   多行注释
 *   多行注释
 *   多行注释
 */
```

贴士：多行注释中每行开头都有一个星号是推荐的写法，每行开头的星号是可以省略的，但是不推荐！

### 4.2 控制台

```js
console.log("打印日志");
console.warn("打印警告");
console.error("打印错误");
```

### 4.3 分号

js 语句末尾的分号可以写也可以不写，不写的时候要保证每行代码都具备完整的语义！

**笔者推荐：书写代码时，尽量保证每行代码都有自己的独立意义，不能在一行代码中添加多个功能！**
