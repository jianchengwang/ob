---
title: electron-vue
createdAt: 2020-04-20
categories: 
- web
- vue
tags: 
- electron
- vue
---

最近喵到一个开源项目 [PicGo](https://github.com/Molunerfinn/PicGo) ，感觉还不错，顺便翻了下大佬的博客，发现有个实战系列[Electron-vue开发实战]([https://molunerfinn.com/electron-vue-1/#%E5%89%8D%E8%A8%80](https://molunerfinn.com/electron-vue-1/#前言))，于是趁机搞个`Todo`练练手，学习一波[Electron-vue](https://github.com/SimulatedGREG/electron-vue)，以下大部分代码来自大佬的博客，所以感兴趣的去看下大佬的博客吧，我只是搬运工小弟。感觉如果有开发过`java swing`或其他`gui`的应该都大同小异。

<!--more-->

## 安装

```
# 全局安装vue-cli
npm install -g vue-cli
# 然后使用vue-cli来安装electron-vue的模板
vue init simulatedgreg/electron-vue electron-vue-storage
# 安装依赖
cd electron-vue-storage
yarn
# 进入开发模式
yarn run dev
```

大概有一个粗略的认识。`electron`的开发其实包括了两个部分，一个是`main`进程的相关开发，一个是`renderer`进程的相关开发。对于`renderer`进程的开发对于大多数前端开发人员来说不难。`main`进程的相关开发，从需求出发，从工程本身的需要出发，那么只需要用到的时候再去学习即可。不过要对`Node.js`能做到的事有个概念——它并不是万能的。

## Main & Renderer

简单来说，`Main` 进程管理的是我们看到的这个`app窗口`([browser-window](https://www.electronjs.org/docs/api/browser-window)) ，`Renderer` 进程对应的就是我们属性的界面的`UI渲染` 。不过实际上，当然不仅如此，下面一张图能够把它们所支持、管理的`electron`或者原生的模块大致列出来：

![Main&Renderer process tree](http://blog.res.jianchengwang.info/electron-vue/main_renderer.png)

### Main进程开发

```js
import { app, BrowserWindow } from 'electron' // 从electron引入app和BrowserWindow

let mainWindow

const winURL = process.env.NODE_ENV === 'development'
  ? `http://localhost:9080` // 开发模式的话走webpack-dev-server的url
  : `file://${__dirname}/index.html`

function createWindow () { // 创建窗口
  /**
   * Initial window options
   */
  mainWindow = new BrowserWindow({
    height: 563,
    useContentSize: true,
    width: 1000
  }) // 创建一个窗口

  mainWindow.loadURL(winURL) // 加载窗口的URL -> 来自renderer进程的页面

  mainWindow.on('closed', () => {
    mainWindow = null
  })
}

app.on('ready', createWindow) // app准备好的时候创建窗口
```

暂且先不管渲染进程里的页面长什么样，在`app`准备好的时候打开一个窗口只需要调用一个创建`BrowserWindow`的方法即可。

`Main进程`里的开发有点当年写`jQuery`的样子，比较多的是`事件驱动型`的写法。

#### app

首先需要注意的是[app](https://electronjs.org/docs/api/app)的模块。这个模块是electron应用的骨架。它掌管着整个应用的生命周期钩子，以及很多其他事件钩子。

app的常用生命周期钩子如下：

- `will-finish-launching` 在应用完成基本启动进程之后触发
- `ready` 当electron完成初始化后触发
- `window-all-closed` 所有窗口都关闭的时候触发，在windows和linux里，所有窗口都退出的时候**通常**是应用退出的时候
- `before-quit` 退出应用之前的时候触发
- `will-quit` 即将退出应用的时候触发
- `quit` 应用退出的时候触发

而我们通常会在`ready`的时候执行创建应用窗口、创建应用菜单、创建应用快捷键等初始化操作。而在`will-quit`或者`quit`的时候执行一些清空操作，比如解绑应用快捷键。

特别的，在非`macOS`的系统下，通常一个应用的所有窗口都退出的时候，也是这个应用退出之时。所以可以配合`window-all-closed`这个钩子来实现。

这个需要用到哪些钩子函数，喵下官网文档吧，都差不多的东东，这里不多叙述。

#### Browser Window

下面我们来看看创建一个BrowserWindow的常用配置：

```js
let window

function createWindow () {
  window = new BrowserWindow({
    height: 900, // 高
    width: 400, // 宽
    show: false, // 创建后是否显示
    frame: false, // 是否创建frameless窗口
    fullscreenable: false, // 是否允许全屏
    center: true, // 是否出现在屏幕居中的位置
    backgroundColor: '#fff' // 背景色，用于transparent和frameless窗口
    titleBarStyle: 'xxx' // 标题栏的样式，有hidden、hiddenInset、customButtonsOnHover等
    resizable: false, // 是否允许拉伸大小
    transparent: true, // 是否是透明窗口（仅macOS）
    vibrancy: 'ultra-dark', // 窗口模糊的样式（仅macOS）
    webPreferences: {
      backgroundThrottling: false // 当页面被置于非激活窗口的时候是否停止动画和计时器
    }
    // ... 以及其他可选配置
  })

  window.loadURL(url)

  window.on('closed', () => { window = null })
}
```

同`app` 一样，也有很多钩子函数，这里也是直接跳过。

#### Tray

简单来说，就是不同系统任务栏里的图标组件了。

需要注意的是，`windows`和`macOS`里，图标的大小都是`16*16`px。`macOS`下顶部栏的图标通常都是走`黑白`路线，所以可以为两种系统分别准备不同的图标。`Tray` 生成代码

```js
function createTray () {
  const menubarPic = process.platform === 'darwin' ? `${__static}/menubar.png` : `${__static}/menubar-nodarwin.png`
  tray = new Tray(menubarPic)
  const contextMenu = // ...菜单
  tray.on('right-click', () => { // 右键点击
    window.hide() // 隐藏小窗口
    tray.popUpContextMenu(contextMenu) // 打开菜单
  })
  tray.on('click', () => { // 左键点击
    if (process.platform === 'darwin') { // 如果是macOS
      toggleWindow() // 打开或关闭小窗口
    } else { // 如果是windows
      window.hide() // 隐藏小窗口
      if (settingWindow === null) { // 如果主窗口不存在就创建一个
        createSettingWindow()
        settingWindow.show()
      } else { // 如果主窗口在，就显示并激活
        settingWindow.show()
        settingWindow.focus()
      }
    }
  })
}
```

注意上述代码里有一个`${__static}`的变量。该变量是`electron-vue`为我们暴露出来的项目根目录下的`static`文件夹的路径。通过这个路径，在开发和生产阶段都能很好的定位你的静态资源所在的目录。是个很方便的变量。

当然`Tray`并不只是一个图标而无其他作用了。Tray支持很多有用的事件。其中最关键的两个是`click`和`right-click`。分别对应鼠标左键点击和鼠标右键点击事件。

##### 鼠标左键点击事件

- 在`macOS`系统下，鼠标左键点击`Tray`的`icon`可能会出现配置菜单，也有可能会出现应用窗口。
- 在`windows`下，鼠标左键点击`Tray`的`icon`通常会出现应用的窗口。

##### 鼠标右键点击事件

- 在`macOS`系统下，鼠标右键点击`Tray`的`icon`通常会出现配置菜单。
- 在`windows`系统下，同上。

  所以一般自己根据需要写监听事件。

#### Menu

`electron`威力强大的`Menu`组件，既能够生成系统菜单项，也能实现绑定应用常用快捷键的功能。

主要分两种。

- 第一种是`app的菜单`。对于macOS来说就是顶部栏左侧区域的菜单项。对于windows而言就是一个窗口的标题栏下方的菜单区。
- 第二种是类似于`右键菜单`的菜单。

第一种菜单可以通过`Menu.setApplicationMenu()`来实现。

```js
const createMenu = () => {
  if (process.env.NODE_ENV !== 'development') {
    const template = [{
      label: 'Edit',
      submenu: [
        { label: 'Undo', accelerator: 'CmdOrCtrl+Z', selector: 'undo:' },
        { label: 'Redo', accelerator: 'Shift+CmdOrCtrl+Z', selector: 'redo:' },
        { type: 'separator' },
        { label: 'Cut', accelerator: 'CmdOrCtrl+X', selector: 'cut:' },
        { label: 'Copy', accelerator: 'CmdOrCtrl+C', selector: 'copy:' },
        { label: 'Paste', accelerator: 'CmdOrCtrl+V', selector: 'paste:' },
        { label: 'Select All', accelerator: 'CmdOrCtrl+A', selector: 'selectAll:' },
        {
          label: 'Quit',
          accelerator: 'CmdOrCtrl+Q',
          click () {
            app.quit()
          }
        }
      ]
    }]
    menu = Menu.buildFromTemplate(template)
    Menu.setApplicationMenu(menu)
  }
}
```

第二种菜单可以通过两个步骤来展示：

**1.** 创建菜单：

```js
const contextMenu = Menu.buildFromTemplate([...])
```

**2.** 展示菜单：

```js
tray.on('right-click', () => { // 右键点击tray的时候  tray.popUpContextMenu(contextMenu) // 弹出菜单})
```

```js
const contextMenu = Menu.buildFromTemplate([
   {
     label: '关于',
     click () {
       dialog.showMessageBox({
         title: 'PicGo',
         message: 'PicGo',
         detail: `Version: ${pkg.version}\nAuthor: Molunerfinn\nGithub: https://github.com/Molunerfinn/PicGo`
       })
     }
   },
   {
     label: '打开详细窗口',
     click () {
       if (settingWindow === null) {
         createSettingWindow()
         settingWindow.show()
       } else {
         settingWindow.show()
         settingWindow.focus()
       }
     }
   },
   {
     label: '选择默认图床',
     type: 'submenu',
     submenu: [
       {
         label: '微博图床',
         type: 'radio',
         checked: db.read().get('picBed.current').value() === 'weibo',
         click () {
           db.read().set('picBed.current', 'weibo')
             .write()
         }
       },
       {
         label: '七牛图床',
         type: 'radio',
         checked: db.read().get('picBed.current').value() === 'qiniu',
         click () {
           db.read().set('picBed.current', 'qiniu')
             .write()
         }
       },
       {
         label: '腾讯云COS',
         type: 'radio',
         checked: db.read().get('picBed.current').value() === 'tcyun',
         click () {
           db.read().set('picBed.current', 'tcyun')
             .write()
         }
       },
       {
         label: '又拍云图床',
         type: 'radio',
         checked: db.read().get('picBed.current').value() === 'upyun',
         click () {
           db.read().set('picBed.current', 'upyun')
             .write()
         }
       }
     ]
   },
   {
     label: '打开更新助手',
     type: 'checkbox',
     checked: db.get('picBed.showUpdateTip').value(),
     click () {
       const value = db.read().get('picBed.showUpdateTip').value()
       db.read().set('picBed.showUpdateTip', !value).write()
     }
   },
   {
     role: 'quit',
     label: '退出'
   }
 ])

 tray.on('right-click', () => {
   tray.popUpContextMenu(contextMenu)
 })
```

基本上有了上述的几个基本模块，我们的一个应用的骨架是基本搭建好了，拥有窗口、任务栏应用图标和菜单项。其他的Main进程的模块，并不是必须的，当会用到的时候将在之后的文章里逐步提及。下面我们将来看`renderer进程`的开发。

### Renderer进程开发

对于`electron-vue`而言，renderer进程其实大部分就是在写我们平时常写的前端页面罢了。不过相对于平时在浏览器里写的页面，在electron里写页面的时候你还能用到不少非浏览器端的模块，比如`fs`，比如`electron`通过`remote`模块暴露给`renderer`进程的模块。接下去我们来看看`renderer`进程有哪些需要注意的地方。

#### 使用Hash模式

往常我们在写Vue的时候都比较喜欢开启路由的`history`模式，因为这样在浏览器的地址栏上看起来比较好看——没有hash的`#`号，就如同请求后端的url一般。然而需要注意的是，`history`模式需要后端服务器的支持。

可能很多朋友平时开发的时候没有感觉，那是因为vue-cli里在开发模式下启动的`webpack-dev-server`帮你实现了服务端的`history-fallback`的特性。所以在实际部署的时候，至少都需要在你的web服务器程序诸如`nginx`、`apache`等配置相关的规则，让前端路由返回给`vue-router`去处理。

而electron里也是如此。在开发模式下，由于使用的是`webpack-dev-server`开启的服务器，所以`BrowserWindow`加载的是来自于类似`http://localhost:9080`这样的地址的页面。而在生产模式下，却是使用的`file://`的协议，比如`file://${__dirname}/index.html`来指定窗口加载的页面。

因此，从上面的表述你也能明白了。假如我有一个子路由地址为`child`。如果不启用Hash模式，在开发模式下没啥问题，`http://localhost:9080/child`，但是在生产模式下，`file://${__dirname}/index.html/child`却是无法匹配的一条路径。因此在electron下，`vue-router`请不要使用`history`模式，而使用默认的`hash`模式。

那么上面的问题就迎刃而解，变为`file://${__dirname}/index.html#child`即可。

```js
const winURL = process.env.NODE_ENV === 'development'
  ? `http://localhost:9080`
  : `file://${__dirname}/index.html`
const settingWinURL = process.env.NODE_ENV === 'development'
  ? `http://localhost:9080/#setting/upload`
  : `file://${__dirname}/index.html#setting/upload`
```

#### 实现titlebar

在上面讲`BrowserWindow`的时候，我说到有时为了应用的美观，并不想让我们的应用窗口采用系统默认的`titlebar`，而想用自己写的来实现。这样的话就在创建你的`BrowserWindow`的配置里加上一句

```js
titleBarStyle: 'hidden'
```

这样就行了。然后你就可以自行在renderer进程的页面里模拟一个顶部的`titlebar`了，比如上面提到的`PicGo`的`titlebar`的样子。实际上代码也很简单：

```html
<div class="fake-title-bar">  
    PicGo - {{ version }}  
    <div class="handle-bar" v-if="os === 'win32'"> 
        <!-- 如果是windows系统 就加上模拟的操作按钮-->    
        <i class="el-icon-minus" @click="minimizeWindow"></i>    
        <i class="el-icon-close" @click="closeWindow"></i> 
    </div>
</div>
```

然后把这个`titlebar`的`position`置顶即可。

不过在平时的使用中，我们要注意，一般我们鼠标按住`titleba`r的时候是可以拖动窗口的。但是如果我们在不加可拖拽的属性之前，我们自己写的`titlebar`是不具备这样的特性的。要加上这个特性也很简单：

```css
.fake-title-bar {  -webkit-app-region drag}
```

只需一条CSS，即可让你的titlebar可以拖拽。

不过在windows下，操作区的按钮（缩小、放大、关闭）长按应该是不能拖拽的，所以还需要：

```css
.handle-bar {  -webkit-app-region no-drag}
```

变成`no-drag`，这样就实现了我们自己生成应用的`titlebar`了。

#### drag & drop 的避免

通常我们用`Chrome`的时候，有个特性是比如你往`Chrome`里拖入一个`pdf`，它就会自动用`内置的pdf阅读器`打开。你往`Chrome`里拖入一张图片，它就会打开这张图片。由于我们的`electron`应用的`BrowserWindow`其实内部也是一个浏览器，所以这样的特性依然存在。而这也是很多人没有注意的地方。也就是当你开发完一个`electron`应用之后，往里拖入一张图片，一个`pdf`等等，如果不是一个可拖拽区域，那么它就不应该打开这张图、这个`pdf`，而是将其排除在外。

所以我们将在全局监听`drag`和`drop`事件，当用户拖入一个文件但是又不是拖入可拖拽区域的时候，应该将其屏蔽掉。因为所有的页面都应该要有这样的特性，所以我写了一个`vue`的`mixin`：

```js
export default {
  mounted () {
    this.disableDragEvent()
  },
  methods: {
    disableDragEvent () {
      window.addEventListener('dragenter', this.disableDrag, false)
      window.addEventListener('dragover', this.disableDrag)
      window.addEventListener('drop', this.disableDrag)
    },
    disableDrag (e) {
      const dropzone = document.getElementById('upload-area') // 这个是可拖拽的上传区
      if (dropzone === null || !dropzone.contains(e.target)) {
        e.preventDefault()
        e.dataTransfer.effectAllowed = 'none'
        e.dataTransfer.dropEffect = 'none'
      }
    }
  },
  beforeDestroy () {
    window.removeEventListener('dragenter', this.disableDrag, false)
    window.removeEventListener('dragover', this.disableDrag)
    window.removeEventListener('drop', this.disableDrag)
  }
}
```

这样在全局引入这个`mixin`即可。

#### remote模块的使用

`remote`模块是`electron`为了让一些原本在`Main`进程里运行的模块也能在`renderer`进程里运行而创建的。以下说几个我们会用到的。

在`electron-vue`里内置了`vue-electron`这个模块，可以在`vue`里很方便的使用诸如`this.$electron.remote.xxx`来使用`remote`的模块。

##### shell

`shell`模块的官方说明是：`Manage files and URLs using their default applications.`也就是使用文件或者`URL`的默认应用。通常我们可以用其让默认图片应用打开一张图片、让默认浏览器打开一个`url`。

如果我们想在`renderer`进程里点击一个按钮然后在默认浏览器里打开一个`url`的话就可以这样：

```html
<button @click="openURL"></button>

<script>
  export default {
    methods: {
      openURL () {
        this.$electron.remote.shell.openExternal('https://github.com/Molunerfinn/PicGo')
      }
    }
  }
</script>
```

更多请查看[文档](https://electronjs.org/docs/api/shell)

##### dialog

有的时候我们会有打开原生的对话框的需求，比如软件说明啥的

```js
s
openDialog () {
  this.$electron.remote.dialog.showMessageBox({
    title: 'PicGo',
    message: 'PicGo',
    detail: `Version: ${pkg.version}\nAuthor: Molunerfinn\nGithub: https://github.com/Molunerfinn/PicGo`
  })
}
```

更多请查看[文档](https://electronjs.org/docs/api/dialog)

#### Menu & Browser-Window的应用

使用`Menu`可能很多人能够理解。但是为什么要使用`BrowserWindow`呢？因为需要定位你打开`Menu`的窗口。

在`PicGo`里，有一个点击按钮打开`Menu`的操作，大致如下：

```js
buildMenu () {
    const template = [...]
    this.menu = Menu.buildFromTemplate(template)
  },
  openDialog () {
    this.menu.popup(remote.getCurrentWindow) // 获取当前打开Menu的窗口
  }
```

这里的`menu.popup`就需要你指定一下打开这个`menu`的窗口。它将自动定位你点击的位置而弹出。

#### Main & Renderer通信

##### ipcMain & ipcRenderer

在`Vue`里，如果是非父子组件通信，很常用的是通过`Bus Event`来实现的。而electron里的不同进程间的通信其实也很类似，是通过`ipcMain`和`ipcRenderer`来实现的。其中`ipcMain`是在`main`进程里使用的，而`ipcRenderer`是在`renderer`进程里使用的。

官网的例子简洁明了

```js
// In main process.
const {ipcMain} = require('electron')
ipcMain.on('asynchronous-message', (event, arg) => {
  console.log(arg)  // prints "ping"
  event.sender.send('asynchronous-reply', 'pong')
})

ipcMain.on('synchronous-message', (event, arg) => {
  console.log(arg)  // prints "ping"
  event.returnValue = 'pong'
})
```

```js
// In renderer process (web page).
const {ipcRenderer} = require('electron')
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // prints "pong"

ipcRenderer.on('asynchronous-reply', (event, arg) => {
  console.log(arg) // prints "pong"
})
ipcRenderer.send('asynchronous-message', 'ping')
```

其中`ipcMain`只有监听来自`ipcRenderer`的某个事件后才能返回给`ipcRenderer`值。而`ipcRenderer`既可以收，也可以发。

那么问题就来了，如何让`ipcMain`主动发送消息呢？或者说让`Main进程`主动发送消息给`ipcRenderer`。

首先要明确的是，`ipcMain`无法主动发消息给`ipcRenderer`。因为`ipcMain`只有`.on()`方法没有`.send()`的方法。所以只能用其他方法来实现。有办法么？有的，用`webContents`。

##### webContents

`webContents`其实是`BrowserWindow`实例的一个属性。也就是如果我们需要在`Main进程`里给某个窗口某个页面发送消息，则必须通过`win.webContents.send()`方法来发送。

```js
// In main process
let win = new BrowserWindow({...})
win.webContents.send('img-files', imgs)
```

```js
// In renderer process
ipcRenderer.on('img-files', (event, files) => {
  console.log(files)
})
```

