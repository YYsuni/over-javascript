## 一 React 简介

React 是 Facebook 开源的一款用于构建前端界面的 JS 库，专注于 MVC 中的视图层（V）。React 的构想是将 UI 抽象为不同的组件，像搭积木一样拼装起来。
前端开发中，经常需要利用 DOM 操作将数据实时反应到 UI 上，频繁 DOM 操作往往非常影响性能。React 会在虚拟 DOM 中比较渲染前后的差异，再决定最优更新 DOM，由于虚拟 DOM 是内存数据，对实际 DOM 操作的仅仅是 Diff 部分，因而提高了性能。

> Diff 算法：
> tree diff:新旧 DOM 树，逐层对比。把所有节点对比完后，就能找到哪些需要更新。
> component diff: 在对比每一层的时候，组件之间对比。组件雷西兴不同，则移除旧组件，新组件替换到被移除的位置
> element diff：组件中的每个元素也要对比
> key:key 是个属性，可以把页面上的 DOM 节点和虚拟 DOM 中的对象做一层关联关系。
> react 与 vue 的区别：

```
他们都采用了虚拟DOM，组件化，数据驱动视图等思想。但是:
vue支持表单控件双向数据绑定，react不支持
react采用JSX来编写组件，Vue使用单文件组件
```

页面中直接使用 react，实现 helloworld：

```js
//shell
$ mkdir react-demos
$ cd react-demos
$ npm init --yes
$ npm install --save react react-dom @babel/standalone
//代码：
<head>
  <meta charset="UTF-8">
  <title>demo - Hello World</title>
  <script src="node_modules/@babel/standalone/babel.js"></script>
  <script src="node_modules/react/umd/react.development.js"></script>
  <script src="node_modules/react-dom/umd/react-dom.development.js"></script>
</head>

<body>
  <div id="root"></div>
  <script type="text/babel">
    ReactDOM.render(
      <h1>Hello, react!</h1>,
      document.getElementById('root')
    )
  </script>
</body>
```

## 二 HelloWorld

使用官方提供的构建工具能够快速的构建基于 react 的 app：

```
npm i -g create-react-app
create-react-app helloworld
cd helloworld
npm start
```

也可以利用 webpack 自建一个 helloword 例子，如下：
项目结构：
![](/images/JavaScript/react-01.png)
package.json：

```
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.1.6",
    "@babel/preset-env": "^7.1.6",
    "@babel/preset-react": "^7.0.0",
    "babel-loader": "^8.0.4",
    "html-webpack-plugin": "^3.2.0",
    "webpack": "^4.26.1",
    "webpack-cli": "^3.1.2"
  },
  "dependencies": {
    "react": "^16.6.3",
    "react-dom": "^16.6.3"
  }
```

.babelrc:

```
{
    "presets": ["@babel/preset-react","@babel/preset-env"]
}
```

webpack.config.js:

```JavaScript
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: {
        index: path.resolve(__dirname,'src/index.js')
    },
    output: {
        path: path.resolve(__dirname, 'dist'),      //输出文件夹
        filename: 'bundle.js'                       //输出文件名
    },
    devServer:{
        hot: true,
        open: true,
        port: 3000,
        inline: true
    },
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            }
        ]
    },
    plugins: [
        new htmlWebpackPlugin({
            template: path.resolve(__dirname,'src/index.html'),
            filename: 'index.html'
        })
    ]
};

```

index.html:

```
    <div id="app">

    </div>
```

index.js:

```JavaScript
import React from 'react';
import ReactDom from 'react-dom';

let helloDiv = React.createElement(
    'div',
    {
        id:'box',
        title: 'hello'
    },
    'hello world'
);
let targetDom = document.getElementById('app');
ReactDom.render(
    helloDiv,
    targetDom
);
```

## 三 JSX

HTML 语言直接写在 JavaScript 语言之中，不加任何引号，这就是 JSX 的语法，它允许 HTML 与 JavaScript 的混写。看上去像是在 Javascript 代码里直接写起了 XML 标签，实质上这只是一个语法糖，每一个 XML 标签都会被 JSX 转换成纯 Javascript 代码，当然你想直接使用纯 Javascript 代码写也是可以的，只是利用 JSX，组件的结构和组件之间的关系看上去更加清晰。

```JavaScript
let title = "hello"
const myel = <h2>{title} world</h2>
ReactDom.render(
    myel,
    document.querySelector("#app")
)
```

JSX 本质：上述的 myel 其实仍然是通过 createElement 方法创建了 DOM 对象。
注意：
JSX 中的 类选择器的名称 class 需要使用 className，因为 ES6 中 class 是关键字；
同样 for 也是关键字，需要使用 htmlFor 代替。

```
JSX中的注释写法：
{
//这里是注释
}
或者 { /* 这里是注释 */ }
```

ReactDOM.render 用于将模板转为 HTML 语言，并插入指定的 DOM 节点。这个方法， 必须而且只能返回一个有效的 React 元素对象。这意味着，如果你的组件是由多个元素构成的，那么你必须在外边包一个顶层 元素，然后返回这个顶层元素。
如果在 JSX 中使用的属性不存在于 HTML 的规范中，这个属性会被忽略。如果要使用自定义属性，可以用 data- 前缀。
JSX 中的标签的属性支持...展开与重写：

```
<Hello name="lisi" {...obj} foo={'override'}/>
```

## 四 组件

### 4.1 构造函数方式创建组件

构造函数就是 React 的组件，组件可以直接以标签的形式使用。

```JavaScript
function MyDiv(){
    // return null; //什么也不创建可以return null
    return <div>基本组件</div>
}

ReactDOM.render(
    <div>
        <MyDiv></MyDiv>
    </div>,
    document.getElementById('app')
);

```

注意：函数的首字母必须大写，因为 React 解析标签时候按照首字母区分，如果是小写按照普通标签处理，如果是大写，按照组件形式处理。

### 4.2 传值给组件

组件外有一个对象，组件内不能直接使用该对象，必须在 构造函数 参数列表中，定义 props 属性接收。

```JavaScript
const person = {
    name: "lisi",
    age: 30
}

function MyDiv(props){
    return <div>名字是：{props.name}</div>
}
ReactDOM.render(
    <div>
        <MyDiv {...person}></MyDiv>
    </div>,
    document.getElementById('app')
);

```

注意：组件内部不能修改对象的字段值，通过 props 得到的任何数据都是只读的。

### 4.3 组件化

上述基于构造函数的组件仍然是书写在 index.js 中，我们可以新建一个独立的 js 文件，该 js 文件即上述构造函数代码，并且导出：export default MyDiv

### 4.4 class 方式创建组件

```JavaScript
//继承react组件，MyDiv2才能是组件
class MyDiv2 extends React.Component {
    render(){
        // return null; //必须有return，没有则return null;
        return <div>class类组件，名称是：{this.props.name}</div>
    }
}

ReactDOM.render(
    <div>
        <MyDiv2 {...person}></MyDiv2>
    </div>,
    document.getElementById('app')
);

```

class 创建的组件其实和构造函数方式类似，props 都是只读的。
注意：如果 class 的构造器内部不能使用 this.props.name 这样获取，需要直接将 props 作为参数传递进来。

### 4.5 有状态组件与无状态组件

class 创建的组件内部拥有一个 this.state 属性，用来存放自己的私有数据，是可读可写的！，那么我们这样分类：

```
有状态组件：class创建的组件，拥有this.state属性，有自己的声明周期函数
无状态组件：构造函数创建的组件，没有state属性，也没有自己的生命周期函数
如果一个组件需要存放自己的私有数据，或者再组件的不同阶段执行不同的逻辑，推荐使用class方式创建有状态组件。
```

### 4.6 使用 css

推荐使用 import 的方式引入 css，但是直接引入的结果会是空对象，需要开启模块化：

```
    {test:/\.css$/, use:['style-loader','css-loader?modules']}
```

当启用 CSS 模块化后，导入样式表得到的 itemStyles 就成了一个样式对象，其中，属性名是在样式表中定义的类名，属性值，是自动生成的一个复杂的类名（为了防止类名冲突）。
