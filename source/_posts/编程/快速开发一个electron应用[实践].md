title: 快速开发一个electron应用-[实践]
date: 2017-12-22
tags: npm
categories: 编程

keywords: electron, react
---

## 一、背景
* 假如你做了APP，H5要给别人看，在述职PPT中最直接了当的办法-->截图！
* 可是只放个截图会不会很单调啊，会不会被吐槽很丑，要是加个模型机预览会不会好很多

## 二、PS大法
不会搞设计的研发不是好研发，说干就干
* 第一步，找模板(心里有点虚)[http://www.ui.cn/detail/26554.html](http://www.ui.cn/detail/26554.html)
* 第二步，先得有一张APP/H5截图
* 第三步，PS技能合成
<!--more-->
</br>
  ![合成](waimai_ps.jpg)
* 第四步，合成后的结果
</br>
  ![合成后的结果](ps_combine.jpg)

## 三、技能升级
​       步入正题，正好最近对electron比较感兴趣，又是要做工具，那就试着写个小工具用
### 1.electron介绍

​        electron最开始不叫这个名字，叫“Atom Shell”,顾名思义，它给我们的应用整合提供了一个可运行的平台，electron通过将[Chromium](https://www.chromium.org/Home)和[Node.js](https://nodejs.org/)合并到同一个运行时环境中，并将其打包为Mac，Windows和Linux系统下的应用来实现这一目的，它负责比较难搞的部分，你只需把精力放在你的应用的核心上即可。现在比较流行的Atom编辑器也是基于electron开发。

[官网介绍传送门](https://electronjs.org/docs/tutorial/about)

### 2.项目结构
```javascript
  .
  ├── dist                //应用程序打包输出
  ├── app
        ├── assets        //资源
        ├── config        //配置
        ├── pages         //主入口
        └── dist          //bundle文件输出
  ├── .babelrc            
  ├── index.html
  ├── app.js            
  ├── webpack.config.dev.js // dev环境开发
  ├── webpack.config.js   // prod环境打包
  ├── package.json
```
* app.js为应用启动脚本

  ```javascript
  const {app, BrowserWindow} = require('electron')
  const path = require('path')
  const url = require('url')

  // 保持一个对于 window 对象的全局引用，如果你不这样做，
  // 当 JavaScript 对象被垃圾回收， window 会被自动地关闭
  let win

  function createWindow () {
    // 创建浏览器窗口。
    win = new BrowserWindow({width: 800, height: 600})
    // 然后加载应用的 index.html。
    win.loadURL(url.format({
      pathname: path.join(__dirname, 'index.html'),
      protocol: 'file:',
      slashes: true
    }))
    // 打开开发者工具。
    win.webContents.openDevTools()
    // 当 window 被关闭，这个事件会被触发。
    win.on('closed', () => {
      // 取消引用 window 对象，如果你的应用支持多窗口的话，
      // 通常会把多个 window 对象存放在一个数组里面，
      // 与此同时，你应该删除相应的元素。
      win = null
    })
  }

  // Electron 会在初始化后并准备
  // 创建浏览器窗口时，调用这个函数。
  // 部分 API 在 ready 事件触发后才能使用。
  app.on('ready', createWindow)

  // 当全部窗口关闭时退出。
  app.on('window-all-closed', () => {
    // 在 macOS 上，除非用户用 Cmd + Q 确定地退出，
    // 否则绝大部分应用及其菜单栏会保持激活。
    if (process.platform !== 'darwin') {
      app.quit()
    }
  })

  app.on('activate', () => {
    // 在macOS上，当单击dock图标并且没有其他窗口打开时，
    // 通常在应用程序中重新创建一个窗口。
    if (win === null) {
      createWindow()
    }
  })
  ```
### 3.不同系统的启动方式

| 系统            | 启动方式                             |
| ------------- | -------------------------------- |
| macOS / Linux | $ ./node_modules/.bin/electron . |
| Windows       | $ .\node_modules\.bin\electron . |

*  可在package.json中配置启动脚本

### 4.开发过程

技术选型为electron + react + webpack，总体思路是利用Canvas做图片的合并：
</br>
![实现思路](design.jpg)

* 第一步，模板设计，模板的宽高(画布宽高)，获取截图相对于模型图片的x,y偏移，以及截图的大小
* 第二步，将第一步获取的数据抽象成配置文件config/flex.js
* 第三步，初始化画布
```javascript
/**
 * [initCanvas 初始画布]
 */
initCanvas = () => {
  const canvas = document.createElement('canvas')
  canvas.width = this.state.canvasWidth
  canvas.height = this.state.canvasHeight
  const context = canvas.getContext('2d')
  this.setState({
    context,
    canvas,
  })
}
```
* 第四部，首先将机型模板图片利用canvas绘制
```javascript
/**
 * [draw description]
 * @param  {[type]}   url      [图片url]
 * @param  {[type]}   x        [相对x偏移]
 * @param  {[type]}   y        [相对y偏移]
 * @param  {Function} callback [合成后回调]
 */
draw = (url, x, y, callback) => {
  const image = new Image();
  image.onload = () => {
    if (!callback) {
      // 模板
      this.state.context.drawImage(image, x, y)
    } else {
      // 截图
      this.state.context.drawImage(image, x, y, this.state.width, this.state.height)
      callback(this.state.canvas.toDataURL('image/png'))      }
  }
  image.src = url;
}
componentDidMount() {
  this.initCanvas()
  // 绘制模型机图片
  this.draw(this.state.theme, 0, 0)
  document.getElementById('upload').addEventListener('change', this.uploadImage)
}
```
第五步，截图上传事件处理
```javascript
/**
 * [uploadImage 上传图片回调]
 * @param  {[type]} event [description]
 */
uploadImage = (event) => {
  const reader = new FileReader()
  const file = event.target.files[0] || event.dataTransfer.files[0]
  reader.onload = (e) => {
    const base64 = e.target.result;
    if (file.size > 1024 * 1024 * 10) {
      toast.warn("图片大小不能超过10M", {
        position: toast.POSITION.BOTTOM_RIGHT,
      })
      return
    }
    this.combine(base64,  (url) => {
      this.setState({
        theme: url,
        combineSuccess: true,
      })
    })
  }
  reader.readAsDataURL(file)
}

componentDidMount() {
  this.initCanvas()
  this.draw(this.state.theme, 0, 0)
  document.getElementById('upload').addEventListener('change', this.uploadImage)
}

componentWillUnmount() {
  document.getElementById('upload').removeEventListener('change', this.uploadImage)
}
```
### 5.调试
* 一期暂时没有实现代码的热加载
* 首先启动npm run watch(webpack --watch)命令监听文件变动，然后npm run mac(或npm run win)启动electron
* 文件每次变更需要手动刷新窗口
【注】如果要实现热加载可以参考[https://segmentfault.com/a/1190000005747526](https://segmentfault.com/a/1190000005747526)

### 6.打包构建
（1）应用icon
[http://www.easyicon.net/covert/](http://www.easyicon.net/covert/)可以用在线转换工具
* mac 为512 x 512
* win 为256 x 256
（2）代码打包
```bash
npm run build
```
（3）应用程序打包
```bash
// 打包exe文件
npm run build:win
// 打包dmg文件
npm run build:mac
```
## 四、下载图片遇到的问题
事情总没有那么完美，当截图大于1M会出现无法下载图片的情况，图片base64编码过大，a标签的href属性限制了字符串的长度，经过一番调研，base64改为文件流下载：
```javascript
  /**
   * [baseb4ToBlob base64转blob]
   * @param  {[type]} dataURL [base64编码]
   */
  base64ToBlob(dataURL) {
    const parts = dataURL.split(';base64,')
    const contentType = parts[0].split(':')[1]
    const raw = window.atob(parts[1])
    const uInt8Array = new Uint8Array(raw.length)
    for (let i = 0; i < raw.length; ++i) {
      uInt8Array[i] = raw.charCodeAt(i)
    }
    const blob = new Blob([uInt8Array], {type: contentType})
    return window.URL.createObjectURL(blob)
  }
```
## 五、结果
</br>
![成果](result.jpg)
对比下结果：

| 方案         | 耗时    | 用户         |
| ---------- | ----- | ---------- |
| PS大法       | 5 min | 会ps的同学 |
| iPhoneView | 10 s  | anyone     |

## 六、代码仓库
[https://github.com/geneking/iphone-view](https://github.com/geneking/iphone-view)
