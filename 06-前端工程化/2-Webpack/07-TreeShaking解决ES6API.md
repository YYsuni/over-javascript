## 一 TreeShaking 简介

webpack 从版本 2 开始引入了 TreeShaking，即引入一个文件后，该文件内未被使用的方法，将不会被打包。

TreeShaking 只支持 ESModule。

development 模式默认不开启 Treeshaking，可以通过配置如下方式修改：

```js
//webpakc添加配置，在dev模式下也启用
optimization: {
    usedExports: true
}

//package.json修改以下配置，默认为false，treeshaking对所有模块生效
"sideEffects":["@babel/polly-fill"]   //防止import polyfill这样的文件不被打包

//一般这样配置：
"sideEffects":[
    "*.css"
]
```

注意：

- 开发环境下即使开启了 treeshaking，所有源码依然会被打包，只是会提示\*\*未被使用
- 生产环境下默认开启了 treeshaking，是不需要配置 optimization 的，但是仍然需要配置 package.json
