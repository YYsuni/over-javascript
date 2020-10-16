## 一 常见 webpack 本身配置

### 1.1 入口与出口

入口与出口可以分别配置：

```js
const path = require("path");
const htmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: {
    //多个entry
    main1: path.resolve(__dirname, "./src/index.js"),
    main2: path.resolve(__dirname, "./src/index.js"),
  },
  output: {
    //name变成了上述的入口名
    path: path.resolve(__dirname, "dist"),
    filename: "[name].js",
  },
  module: {
    rules: [
      {
        test: /\.(css|scss)$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
  plugins: [
    new htmlWebpackPlugin({
      template: path.resolve(__dirname, "src/index.html"),
      filename: "index.html",
    }),
  ],
};
```

注意：此时 html-webpack-plugin 会将多个入口都注入到 html 中。

output 的其他配置：

- publicPath: 会给注入到 html 中的 JS 的 src 添加该前缀

### 1.2 sourceMap

sourceMap 可以映射打包后的代码错误点与源码错误点位置，方便调试。  
配置方式：

```
# 开发环境推荐配置： eval意思是eval方式打包，打包速度快，提示也全
devtool: 'cheap-module-eval-source-map'

# 生产环境推荐配置：因为依赖多，eval容易出问题
devtool: ''cheap-module-source-map'
```

## 二 webpack-dev-server

### 2.1 -watch 参数

webpack 的打包命令如果添加了`watch`参数，则可以监听源码文件，当源码文件有改动时，则自动重新打包。

### 2.2 webpack-dev-server

如果我们要实现 watch 的效果，且能自动打开浏览器，刷新浏览器，那么需要使用 webpack-dev-server,该工具会使 contentBase 配置中的目录成为服务器静态文件目录。

第一步：安装

```
cnpm i webpack-dev-server -D
```

第二步：脚本修改

```js
"dev": "webpack-dev-server --config webpack.config.js"
```

第三步：修改 webpack 配置

```js
    devServer: {
        contentBase:  path.join(__dirname, "dist"),
        open: true,                 //启动时，会打开浏览器并渲染页面
        port: 3000,                 //默认是3000
        hot: true,                   //开启hotModule功能
        hotOnly: true,               //html生效，则浏览器不刷新
        historyApiFallback:true      //单页面应用启用路由时候需要该设置
    },
```

注意：

- webpack-dev-server 打包的 dist 目录内是没有文件的，文件位于内存中。
- 当开启 hot 与 hotOnly 时，需要引入 webpack 自带的插件，如下所示:

```js
new webpack.HotModuleReplacementPlugin();
```

这样配置：可以更方便的调试 css，js，因为某块地方的更改不会改变其他模块已经产生的数据结果（即不会因为改变文件而全局重新生成）。

比如有模块 a 与模块 b，模块 c 引用了模块 a，现在模块 a 改变了，只会改变模块 a 内部的数据，模块 c 不会变化，如果想要模块 c 也跟着改变，可以在模块 c 中的源码这样书写：

```js
if (module.hot) {
  moduel.hot.accept("a", () => {
    //业务代码
  });
}
```

## 三 webpack 打包模式。

webpack 在打包时有开发模式（development）和生产模式（prodction）两种，在 mode 中配置，那么为了对应不同的环境就需要不同的配置。

二者区别：

```

```

npm 脚本配置：

```json
    "dev": "webpack-dev-server --config webpack.config.dev.js",
    "build": "webpack --config webpack.config.prod.js"
```

由于 dev 与 prod 的配置有很多相同的地方，推荐将共同部分抽离。

```
# 安装webpack配置合并插件
cnpm install -D webpack-merge

# 将通用配置移动到到webpack基础配置文件：webpack.config.base.js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	entry: {
        main: path.resolve(__dirname, './src/index.js')
    },
    module:{
        rules: [
            {
                test:  /\.(css|scss)$/,
                use:['style-loader', 'css-loader']
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

# 开发配置：webpack.config.dev.js
const path = require("path");
const merge = require("webpack-merge");
const webpack = require("webpack");

const baseConfig = require("./webpack.config.base");

const devConfig = {
    mode: 'development',
    devtool: 'cheap-module-source-map',
    output: {                                       //name变成了上述的入口名
		path: path.resolve(__dirname, 'dist'),
		filename: '[name].js'
    },
    devServer: {
        contentBase:  path.join(__dirname, "dist"),
        open: true,                                 //启动时，会打开浏览器并渲染页面
        port: 3000,                                 //默认是3000
        hot: true,                                  //开启hotModule功能
        hotOnly: true
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ]
};

module.exports = merge(baseConfig, devConfig)


# 生产配置：webpack.config.prod.js
const path = require("path");
const merge = require("webpack-merge");

const baseConfig = require("./webpack.config.base");

const prodConfig = {
    mode: 'production',
    output: {                                           //name变成了上述的入口名
		path: path.resolve(__dirname, 'build'),
		filename: '[name].js'
    },
};

module.exports = merge(baseConfig, prodConfig);


```

贴士：在开发时，使用 webpack-dev-server 往往不能查看打包后的代码了，也可以在 npm 脚本内再建一个`"dev-build": "webpack --config webpack.config.dev.js"`。
