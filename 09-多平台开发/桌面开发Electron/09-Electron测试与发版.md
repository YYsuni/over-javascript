# Electron 测试与发版

## 一 优化建议

### 1.1 包体积问题优化

Electron 与 Web 项目不同，其部署环境比较多维复杂，需要尽量节省内存。而 Electron 由于使用 Chrome 内核，其对 W3C 的标准支持足够，多数兼容性要求并不在 Electron 项目的考虑之中。比如 axios 库中做了大量的用户环境判断，这在 Electron 中是完全不需要的！

Electron 由于是本地应用，不存在网络下载速度问题，所以本地文件如 JS 模块，应该尽量合并，避免不必要的加载工作，当然这在单页面应用流行的当下，该问题并不突出。

Node.js 的 web 项目往往会在头部 require 大量模块，这是因为 web 环境不太需要担心初始化时候的资源消耗问题，Electron 应用应该尽量在使用时 require！

### 1.2 性能优化的建议

应当尽量规避同步方法的应用，比如主进程中使用会造成整个系统的停顿，用户体验极差。可以使用 Web Worker 标准来处理，或者判断系统是否空闲来处理：

```js
powerMoitor.getSystemIdleState(idleThreshold);
```

## 二 测试

### 2.1 单元测试

Electron 应用本身可以使用 Mocha 作为单元测试用工具。

### 2.2 界面测试

Electron 提供了界面测试框架 Spectron，内部封装了 ChromeDriver WebdriverIO。

安装

```txt
# 注意： spectron9  对应 Electron7，依次递增类推
npm i spectron -D
```

在 test 文件夹内创建 test.js 测试文件：

```js
const { Application } = require("spection");
const assert = require("assert");
const electronPath = require("electron");
const path = require("path");

describe("开始执行界面测试", () => {
  this.timeout(10000);

  beforeEach(() => {
    this.app = new Application({
      path: electronPath, // 导入 electron，其实是导出了本项目下 Electron 的课执行文件安装路径
      args: [path.join(__dirname, "..")],
    });
    return this.app.start();
  });

  afterEach(() => {
    if (this.app && this.app.isRunning()) {
      return this.app.stop();
    }
  });

  it("测试窗口是否启动", async function () {
    let count = await this.app.client.getWindowCount();
    assert.equal(count, 1);
  });
});
```

运行测试：

```txt
mocha
```

## 三 调试

### 3.1 性能监控工具

Electron 应用的性能监控可以直接利用 Chrome 开发者工具中的 Performance 工具，但是当页面很多，且需要精细化观察一个操作的性能问题时，可以使用官方提供的 contentTracing 模块。

在 app 的 ready 事件中，写入以下示例代码：

```js
(async () => {
  const { contentTracing } = require("electron");

  // 启动性能监控
  await contentTracing.startRecording({
    include_categories: ["*"],
  });

  // 等待运行 6 秒
  await new Promise((resolve) => {
    setTimeout(resolve, 6000);
  });

  // 关闭监控，查看日志位置
  const path = await contentTracing.stopRecording();
  console.log("日志地址：", path);
  createWindow();
})();
```

使用 Chrome 浏览器的 `chrome://tracing` 后，点击 Load 加载上述日志文件，即可查看性能状况。

### 3.2 开发环境调试工具

Electron 官方专门提供了 Devtron 工具用来在开发环境下进行调试，该工具是浏览器插件：

```txt
# 安装包文件
npm i devtron -D

# 启动窗口后，在开发者工具的命令行输入命令安装插件
require('devtron').install

# 安装完毕后，开发者工具会新增一个 Devtron 面板
```

### 3.3 生产环境调试工具

Electron 应用打包后，就很难直接调试了，这时候可以使用字节跳动的第三方工具<https://github.com/bytedance/debugtron>，该工具可以发现系统中安装的 Electron 应用。

### 3.4 日志工具

推荐的日志工具为：<https://github.com/megahertz/electron-log>

对于网络请求的监控，Electron 提供了 netLog 模块，用来以日志文件形式记录请求数据。

### 3.5 崩溃报告

目前没有很好的崩溃报告工具，Electron 官方的 mini-bareakpad-server 已经很久没更新。但是开发者可以利用 Electron 内置的崩溃报告模块 crashReporter 来定制：

```js
// 启动崩溃服务，会向地址发送 POST 请求
electron.crashReporter.start({
  productName: "demo",
  submitURL: "www.demo.com",
  uploadToServer: true,
  extra: "",
});

// 手动发送崩溃原因
electron.crashReporter.addExtraParameter(key, value);
```

## 四 发版

### 4.1 应用图标

图标建议准备 1024*1024 大小的 png 格式，存放在 public 目录下：

```txt
# 安装 electron-icon-builder 组件
npm i -D electron-icon-builder

# 在 package.json 中增加如下 script 指令
"build-icon": "electron-icon-builder" --input=./public/icon.png --output=buil --flatten

# 执行，会在 build/icons 目录生成各种大小的图标文件
npm run build-icon
```

### 4.2 打包

常用的打包工具有：electron-packager、electron-builder，后者内置了自动升级配置，只要将打包的文件随意放置在 web 服务中即可完成自动升级，推荐后者。

使用 `Vue CLI Plugin Electron Builder` 创建的项目，直接使用 `npm run electron:build` 即可打包，且会依据打包系统的不同产生不同平台的安装包。

### 4.3 签名

为了避免包被串改，默认情况下，Mac 只允许从 Store 中下载，Win 下安装未签名的程序会出现风险提示。

给 Windows 应用签名，需要购买签名证书，可以在 digicert、godaddy 等平台购买。

如果使用了 electron-builder 打包，购买证书后可以按照 <https://www.electron.build/configuration/win> 中的文档配置。

如果使用了 `Vue CLI Plugin Electron Builder` 创建的项目打包，则配置信息将放置在 vue.config.js 中：

```js
module.exports = {
  productionSourceMap: false,
  pluginOptions:{
    electronBuilder:{
      builderOptions: {
        win: {
          signingHashAlgorithms:["sha1", "sha256"],
          certificateFile: "证书文件",
          certificatePassword: "证书密码",
          certificateSubjectName: "",
          // ...
        }
      },
      mainProcessFile: 'public/background/start.js'
    }
  }
}
```

在 Mac 中打包时，无需额外配置，electron-builder 会自动加载钥匙串证书，但是该证书必须加入苹果开发者计划获取。

### 4.4 自动升级

electron-builder 自动升级需要添加配置：

```js
publish:[
  {
    provider: "generio",
    url: "http://dowload.demo.com"
  }
]
```

如果使用的是 `Vue CLI Plugin Electron Builder` 创建的项目打包，则此配置位于 vue.config.js 的 builderOptions 节点中。

url 是升级的地址在哪儿，还需要在主进程加入：

```js
const { autoUpdater } = require('electron-updater');

autoUpdater.checkForUpdates();

autoUpdater.on("update-download", ()=>{
  this.mainWin.webContents.send('updateDownLoaded');  
});

ipcMain.on('quitAndInstall', (event) =>{
  autoUpdater.quitAndInstall();
});
```

上述代码尽量保持在窗口启动后适当时机运行，以不占用窗口启动时间。autoUpdater 模块负责管理程序的升级，`checkForUpdates()` 则会检查服务端的配置文件是否存在更新的安装程序（对比package中的version），如果有，则开始下载，下载后会给渲染进程发送消息，由渲染进程提示用户“当前有新版本，是否需要升级”，用户选择升级后，由渲染进程发送 'quitAndInstall' 给主进程，主进程自动升级，退出程序，安装完毕后重启。

打包完毕后，Win 平台上传：`[app]Setup[version].exe` 和 `lastes.yml` 文件到服务端，Mac 上传 `[app]-[version]-mac.zip` 和 `[app]-[version]-mac.dmg` 以及 ``lastes-mac.yml`文件到服务端， Linux 平台则上传：`[app]-[version].AppImage` 和 `lastes-linux.yml`。
