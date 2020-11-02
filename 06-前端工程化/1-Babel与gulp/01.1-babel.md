## 一 Babel

ES6 React 等写法在浏览器中并未得到完全的支持，利用 Babel 工具可以将 ES6、React 等语法编译为浏览器识别的 ES5。

使用步骤：

```
1 项目根目录安装：
    旧版：npm i -D babel-cli babel-core babel-preset-env
    新版：npm i -D @babel/cli @babel/core @babel/preset-env

2 项目根目录创建 .babelrc ,内容如下:
    旧版：{"presets":["env"]}
    新版：{"presets":["@babel/env"]}

3 编译src目录下所有文件到dist目录下
    npx babel src -d dist                           // 若npm<5.2，则可以使用 ./node_modules/.bin/babel
```

babel-cli 只是一个执行 babel 命令行工具，本身不具备编译功能，编译功能是由插件 babel-preset-env 提供的。

带 env 是指最新的 babel 编译工具，包含了所有的 ES\*功能，如果我们不需要这么多的新特性，可以有选择的安装编译插件：

```
# ES2015转码规则
$ npm install --save-dev babel-preset-es2015
# react转码规则
$ npm install --save-dev babel-preset-react
# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```

在命令行中敲击大量命令显然不是高效的，可以直接通过配置文件配置 babel，新建文件 `.babelrc`即可：

```
  {
    "presets": [
      "es2015",
      "react",
      "stage-2"
    ],
    "plugins": []
  }
```
