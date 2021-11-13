---
date: 2021-10-24
title: "Electron学习笔记"
tags: ["Electron, Node.js, JavaScript"]
categories: ["Front End"]
---
# Electron 学习笔记

## 导图

![](https://i.loli.net/2021/11/13/JrhlMv93CXYHpzQ.png)

## 基本介绍

### Electron三个组成部分

1. **Chormium - UI**

- Web生态兼容性

2. **Node.js - 底层操作**

- 文件读写；
- 使用C++；

3. **Native API - 跨平台**

- 统一原生界面如窗口，托盘；
- 系统能力：Notification；
- 基础能力：软件更新/崩溃；



### 浏览器发展史

Nexus世界上第一个浏览器

Mosaic - Mozilla - NetScape（所有浏览器的UA<font color="#aaa" size="2">(user-agent)</font>都会带上Mozilla）

IE (1995 Microsoft)

JavaScript (1995 NetScape) Brendan Eich - 10天完成！

开源Mozilla - 火狐

Chrome (2008,极简 多进程 v8)

Chrominum 开源 node.js 开源 v8开源

node-webkit (NW 2011) Github前五 - 赵成 （后加入Github Atom 项目组)

Atom-shell  -> Electron (2013->2015)



> 判别是否Electron应用：
>
> 项目目录下 Contents/Framework 下是否有Electron



### Electron应用最小组成

index.html 页面

package.json 包体描述 

main.js 管理整个应用



## 架构和原理

### Chromium架构

![](https://i.loli.net/2021/11/13/PHTq1pKvuUyijNe.png)

IPC跨进程通信 （主进程RenderProcessHost，Renderer进程RenderProcess）

ResourceDispatcher页面请求资源转发到IPC  Browser处理再返回

### Electron架构

![](https://i.loli.net/2021/11/13/89zRApQhS3Eo6DP.png)

### Node.js和Chromiums整合

难点：事件循环（Node.js基于libuv，Chromium基于message bump）

- Chromium集成到Node.js ，用libuv实现messagebump(NW) 【NSRunLoop glib 边界情况】
- Node.js集成到Chromium【libuv轮询backend_fd】

![](https://i.loli.net/2021/11/13/shuEibU4Q6YL3fG.png)

延申资料

![](https://i.loli.net/2021/11/13/wgdMLeOtpraU7A3.png)

## 桌面端技术选型

入口快捷

离线可用

调用系统能力（通知，硬件）

安全需求（金融）



Native(C++/C#/Objective-c)

高性能，原生，体积小；门槛高

QT(C++)

跨平台，高性能，媲美原生；门槛高，迭代速度一般

Flutter

发展中，基建少

Nw.js （微信开发者工具）

支持XP，源码加密，支持扩展；包体积大，性能一般

Electron

包体积大，性能一般



Carlo，WPF，Chromium Embedded Framework，PWA





## 开发环境准备

安装 vscode，nodejs(NVM)【跳过】

安装 Electron：

```npm install electron --save-dev```

```npm install --arch=ia32 --platform=win32 electron```  (32位打包，在32,64都能用)

验证安装成功

```npx electron -v```

```./node_modules/.bin/electron -v```



## 开发一个番茄钟

 休息5分钟弹出休息结束

应用启动 → 倒计时工作25分钟 → 弹出是否休息→休息则5分钟倒计时后结束→不休息则重新开始25分钟

![](https://i.loli.net/2021/11/13/1B3YZLaqrF7zOu4.png)

### 引入模块

```js
const { app, BrowserWindow } = require('electron')
```

可以先阅读&操作一遍官方的[快速开始文档](https://www.electronjs.org/zh/docs/latest/tutorial/quick-start)



### 第三方库

[timer.js](https://github.com/husa/timer.js/)

#### 安装

```npm install timer.js```

#### 引入

``` js
var myTimer = new Timer({
	onTick:() => {}
    onEnd: () => {}
})
```

#### 使用



## 与Web开发异同

### 一、主进程与渲染进程

主进程（唯一）

- package.json中main脚本的进程
- 管理原生GUI(BrowserWindow、Tray、Dock、Menu)
- 创建渲染进程，控制应用生命周期(app)

渲染进程（多个）

- 展示Web页面的进程
- 通过Node.js、Electron提供的API跟系统底层打交道

![](https://i.loli.net/2021/11/13/aCwW3I48t5MOiSR.png)

| 主进程                                        | 渲染进程                            | 交叉                  |
| --------------------------------------------- | ----------------------------------- | --------------------- |
| app管理应用生命周期(退出)，设置属性(Dock)     | ipcRenderer（跟ipcMain进行IPC通信） | clipboard剪切板       |
| BrowserWindow管理窗口                         | remote调用主进程模块（建议不用）    | crashReporter监听进程 |
| ipcMain（跟ipcRenderer进行IPC通信）           | desktopCapture系统截图/视频流       |                       |
| Menu/Tray/MenuItem/dialog/Notification原生GUI |                                     |                       |
| webContents加载具体页面                       |                                     |                       |
| autoUpdater更新模块                           |                                     |                       |
| globalShortcut设置全局快捷键                  |                                     |                       |

### 二、进程间通信

- 通知事件（创建原生菜单只有主进程能做）
- 数据传输（在某页面获得内存情况）
- 共享数据（用户信息在各个进程中共用）

#### IPC模块通信

Electron提供的IPC通信模块：主进程ipcMain、渲染进程ipcRenderer（两者都是EventEmitter对象）

#### 从渲染进程到主进程

- Callback写法
  - ipcRenderer.send(channel, ...args)
  - ipcMain.on(channel, handler)

- Promise写法（Electron7.0之后，处理请求+响应模式）
  - ipcRenderer.invoke(channel, ...args)
  - ipcMain.handle(channel, handler)

#### 从主进程到渲染进程

- 主进程通知渲染进程
  - ipcRenderer.on(channel, handler)
  - webContents.send(channel) [BroswerWindow]

#### 页面间（渲染进程与渲染进程）通信

- 通知事件
  - 通过主进程转发（Electron 5之前）
  - ipcRenderer.sendTo（Electron 5之后）
- 数据共享
  - web技术（localStorage、sessionStorage、indexedDB）
  - <font color=#ccc>使用remote</font>

```js 

// main.js
// new win1 & win2 instanceof BrowserWindow and loadFile
global.sharedObject = {
	win2WebContentsId = win2.webContents.id // 传输webContents.id
}

// renderer1.js
const { ipcRenderer, remote } = require('electron')
let sharedObject = remote.getGlobal('sharedObject')
let win2WebContentsId = sharedObject.win2WebContentsId
ipcRenderer.sendTo(win2WebContentsId, 'do-some-work', 1)

// renderer2.js
const { ipcRenderer } = require('electron')
ipcRenderer.on('do-some-work', (e, a) => {
    alert('renderer2 handle some work' + a)
})
```



#### 坑🕳

- 少用remote模块（好写，但触发底层IPC通信事件，性能开销大，易卡死）
- 不要用sync模式（ipcRenderer的方法，易卡死）
- 在请求+响应的通信模式下，需要自定义超时限制



### 三、Native及原生GUI

#### 使用Electron API创建原生GUI

- BrowserWindow 应用窗口
- Tray 托盘
- app设置dock.badge（未读数）
- Menu菜单
- dialog原生弹框
- TouchBar苹果触控板



#### 使用Electron API获得底层能力

- clipboard
- globalShortcut 全局快捷键
- desktopCapture 捕获桌面
- shell打开文件、URL
- ...



#### 使用Node.js获得底层能力

- Electron 同时在主进程和渲染进程中对Node.js暴露了所有的接口
  - fs进行文件读写
  - crypto进行加解密
- 通过npm安装社区上所有的Node.js库

🌰 

使用node.js的fs模块打印文件内容

```js 
mainWindow.webContents.openDevTools() // 主进程打开开发工具
```

``` js
require('fs').readFileSync('./index.html', 'utf-8') // 开发工具的console中执行
```



#### 使用Node.js调用原生模块

node.js add-on

node-ffi (Foreign Function Interface)



#### 调用OS能力

- winRT（调用蓝牙等）
- Applescript（苹果系统）
- Shell（node.js child_process）



#### Electron能力

![](https://i.loli.net/2021/11/13/kWbyjTlwS71KrPL.png)





### 四、释放前端想象力

- 不用兼容Safari、IE
  - babel中设置targets为Electron对应的Chrome版本

- 大胆使用Chrome最新支持的API 【[文档地址](https://developers.google.com/web/updates)】
  - [原生LazyLoad ](https://mathiasbynens.be/demo/img-loading-lazy) 
  - ES 6/7/8/9/10高级语法
    - async await / Promise
    - String/Array/Object高级用法
    - BigInt（处理大数不用再引用long.js啦）

- 无跨域问题（使用Node.js发送请求、使用Electron net发送请求）

- 操作本地文件
- 更好用的本地DB（LowDB, LevelDB, SQLite）
- 多线程、多进程并行（Node.js: worker + child_process）