## 一 webpack 配置书写格式

我们需要注意的是：webpack 本身只是一个打包工具，不具备任何功能。各类打包、编译工具都是由加载器或者插件完成的，比如编译 ES6,react 都是由 babel 这个第三方工具完成的，webpack 可以使用 babel-loader 加载这个第三方工具。webpack 具备很好的可扩展性，在编译方面，可配置大量的加载器（loader），在打包方面，可以配置大量的插件（plugins）。

加载器的配置书写案例：

```js
module: {
  rules: [
    {
      test: /(\.jsx|\.js)$/, //正则匹配 哪些文件 需要该加载器
      use: {
        //匹配到的文件使用哪个加载器
        loader: "babel-loader",
        options: {
          //加载器的参数
          presets: ["env"],
        },
      },
      exclude: /node_modules/, //忽略掉哪些文件不走该加载器
    },
    {
      //后续的加载器
    },
  ];
}
```

插件的配置书写案例：

```js
plugins: [
  new htmlWebpackPlugin({
    template: path.resolve(__dirname, "src/index.html"),
    filename: "index.html",
  }),
  //热更新插件
  new webpack.HotModuleReplacementPlugin(),
];
```

注意：webpack 配置支持书写格式五花八门（字符串，数组，json），给入门者造成了相当的困扰，笔者推荐按照上面最复杂的格式书写。

## 二 loader

### 2.1 loader 使用步骤

webpack 只支持打包 JS 文件，对于其他文件的打包，要依赖于各种加载器，即 loader，每个加载器负责处理一种文件。比如现在需要打包图片，那么需要：

- 安装 loader：cnpm install -D file-loader
- 配置 loader：在 config 文件中新增 module 字段，该字段值是个对象，内部包含一个 rules 字段，rules 字段是个数组，该数组内书写规则。

注意：多个 loader 的处理顺序：从下到上，从右到左！

### 2.2 file-lader 打包文件

在 index.js 中加载一些文件，webpack 是不能正确打包的，需要安装并配置`file-loader`。

示例整体结构图：
![](/images/JavaScript/webpack-04.png)

index.js:

```js
//注意：该js文件被同级目录下的index.html所引用，打包完毕后需要在dist目录下也创建相同的index.js文件，引入该js
import saberJPG from "./public/images/sabar.jpg";

let img = new Image();
img.src = saberJPG;

let root = document.getElementById("root");
root.append(img);
```

webpack 配置：

```js
const path = require("path");

module.exports = {
  entry: {
    //entry是可以有多个的
    main: path.resolve(__dirname, "./src/index.js"),
  },
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "index.js",
  },
  module: {
    rules: [
      {
        test: /\.(jpg|png|jpeg|gif)$/,
        use: ["file-loader"],
      },
    ],
  },
};
```

npm 脚本：

```
	# 脚本
    "dev": "webpack --config webpack.config.js --mode development"

	# 依赖
	"devDependencies": {
   		"file-loader": "^3.0.1",
    	"webpack": "^4.29.6",
    	"webpack-cli": "^3.2.3"
  	}

```

注意：打包后生成的图片名不是原名，图片的目录也是根目录，而不是原有的 images 文件夹，可以这样配置：

```js
rules: [
  {
    test: /\.(jpg|png|jpeg|gif)$/,
    use: {
      loader: "url-loader",
      options: {
        name: "[name]_[hash].[ext]",
        outputPath: "public/images/",
      },
    },
  },
];
```

此时打开 dist 目录下的 index.html 进行测试，发现一切正常。

贴士：

- [name] [hash]是 webpack 的占位符，还有很多类似的占位符。
- 每次打包需要手动删除 dist 目录下的打包后文件

### 2.2 url-loader 打包文件

url-loader 具备 file-loader 的功能，且额外拥有自己的一些功能：

- 将会把小图片打包为 base64 格式，减少 http 请求

安装：

```
cnpm i url-loader -D
```

配置如下：

```js
            {
                test:  /\.(jpg|png|jpeg|gif)$/,
                use:{
                    loader: 'url-loader',
                    options: {
                        name: '[name]_[hash].[ext]',
                        outputPath: 'public/images/',
                        limit: 2048                     //如果图片超过2048字节不生成base64
                    }
                }
            }
```

### 2.2 打包 CSS

安装：

```
cnpm i -D style-loader css-loader
```

配置：

```js
            {
                test:  /\.css$/,
                use:['style-loader', 'css-loader']
            }
```

源码：

```js
import "./public/css/index.css"; // 可以在js文件中直接引入css
```

- css-loader：让 JS 支持 import css 模块，会遍历 CSS 文件，然后找到 url() 表达式的关系并处理他们
- style-loader： 把刚才分析得到的 css 代码插入页面 head 标签的 style 标签中。

如果用到了 saas，需要安装 node-saas,saas-loader（同理也有 less-loader）,配置如下：

```js
            {
                test:  /\.(css|scss)$/,
                use:['style-loader', 'css-loader','scss-loader']
            }
```

如果需要给某些 css 加上类`moz`这样的前缀，需要`postcss-loader`,配置方式：

```js
//环境
cnpm i -D autoprefixer postcss-loader

//第一步：根目录下创建 postcss.config.js,内容如下：
module.exports = {
    plugins: [
        require('autoprefixer')
    ]
}

//第二步：loader配置
            {
                test:  /\.(css|scss)$/,
                use:['style-loader', 'css-loader','scss-loader','postcss-loader']
            }
```

还有一种方式是使用 file-loader，配置如下：

```js
{
	test: /\.css$/,
	use: [
		{loader: 'style-loader/url'},
		{loader: 'file-loader'}
	]
}
```

此时 import 的 css 不会被以标签形式直接加入 html，而是额外打包成了 css 文件，通过 link 标签引入该 css,如果 import 了多个 css，那么会生成多个 link，这样会造成多次请求，所以很少使用。
style-loader 也可以控制样式的显示与否，配置如下：

```
use:[
    {loader: 'style-loader/useable'},
    {loader: 'css-loader'}
]
```

这样配置后，我们可以在 js 中控制 CSS 的显示与否，比如在 app.js 中加入如下代码：

```js
import baseCSS from "./css/index.css";
baseCSS.use();
setInterval(function () {
  baseCSS.unuse();
}, 300);
```

style-loader 也有大量的可配置选项：

```
	insertAt：		插入位置
	insertInto：		插入到某个dom节点
	singleton：		是否只使用一个style标签
	transform：		浏览器中转化，插入也面前执行一些列操作
```

案例：

```
{
	loader: 'style-loader',
	options: {
		insertInfo: '#box',
		singleton: true
	}
},
```

上述配置案例中 style 会直接插入到 #box 这个标签中，singleton 为 true 代表多个不同的 style 将会被合并到一个 style 中。

css-loader 选项：

```
	alias：			解析的别名
	importLoaders：	也作 @import，常用值为2，意思是必须按照顺序执行前2个loader
	minimize：		是否压缩，true为要压缩，
	modules：		是否启用css-modules，true为启用css模块化，推荐使用
	localIdentName:	CSS的class名重新生成，常见设定为：
                    [path][name]_[local]_[hash:base64:5]
```

### 2.3 压缩图片 img-loader

```
rules: [
    {
      test: /\.(jpe?g|png|gif|svg)$/i,
      use: [
        'url-loader?limit=10000',
        'img-loader'
      ]
    }
  ]
```

如果需要配置压缩率，可以添加可选项：

```
pngquant:{
    quality: 80
}
```

### 2.4 处理 img 标签 html-loader

```
{
    test: /\.html$/,
    use: [
        {
            loader: 'html-loader',
            options: {
                attrs: ['img:src', 'img:data-src']
            }
        }
    ]
}
```

### 2.5 处理 typescript

推荐使用官方的 loader：

```
npm i -D ts-loader
```

tsconfig.json 配置：

```js
{
    "compilerOptions": {
        "module": "commonjs",
        "target": "es5",
        "allowJs": true
    },
    "include": [
        "./src/*"
    ],
    "exclude": [
        "./node_module"
    ]
}

```

webpack 配置：

```
{
    test: /\.tsx?$/,
    use: {
        loader: 'ts-loader'
    }
}
```

注意：
ts 虽然对类型进行了限制，但是不会制动提示哪个地方出现了类型问题。

```
npm i @types/lodash
//如果使用了vue还需要安装：
npm i @types/vue

//使用
import * as _ from 'lodash';
```
