# cordova-hot-code-push-guide
cordova-hot-code-push 使用指南

## 背景说明

基于 Cordova 框架能将网页应用 (js, html, css, 图片等) 打包成 App。当 App 在应用商店上架后，如何快速更新是我们需要考虑的问题。🤖

* 本地打包新版本 App 发布到应用商店，但这中发布流程耗费时间，尤其是 Apple Store

* 应用加载网络资源，这样 App 展示的内容就可以保证是最新的，但当应用断网时，应用就无法正常使用

我们能想的这两种方式都存在的很大的弊端，不适合实际应用！

### 插件 Cordova Hot Code Push (CHCP)

插件 [Cordova Hot Code Push](https://github.com/nordnet/cordova-hot-code-push) 正是针对 Cordava 应用如何快速更新问题而提供的解决方案，可以自动更新 web 相关的静态文件。

> 该插件提供了详细的 wiki 文档，请参考：[wiki文档](https://github.com/nordnet/cordova-hot-code-push/wiki)

## 1 配置环境

1.1 检测是否安装了node.js

打开终端输入`npm --version`，若无打印版本则需先安装node.js

1.2 命令行安装cordova

`npm install -g cordova`

## 2 创建[Cordova](https://cordova.apache.org)项目

通过命令行创建Cordova项目，并添加iOS/Android platform

```
cordova create TestProject com.example.testproject TestProject
cd ./TestProject
cordova platform add android
cordova platform add ios
```

## 3 安装[cordova-hot-code-push](https://github.com/nordnet/cordova-hot-code-push)插件

3.1 添加cordova-hot-code-push-plugin

`cordova plugin add cordova-hot-code-push-plugin`

3.2 添加本地调试插件

`cordova plugin add cordova-hot-code-push-local-dev-addon`

3.3 添加Cordova Hot Code Push CLI 客户端

`npm install -g cordova-hot-code-push-cli`

3.4 启动本地服务

`cordova-hcp server`

你会看到如下图：
```
Running server
Checking:  /Cordova/TestProject/www
local_url http://localhost:31284
Warning: .chcpignore does not exist.
Build 2015.09.02-10.17.48 created in /Cordova/TestProject/www
cordova-hcp local server available at: http://localhost:31284
cordova-hcp public server available at: https://5027caf9.ngrok.com
```


3.5 打开一个新的终端，进入项目根目录，启动app

`cordova run`

如果报错：
```
** BUILD SUCCEEDED **

Error: ios-deploy was not found. Please download, build and install version 1.8.3 or greater from 
https://github.com/phonegap/ios-deploy into your path, or do 'npm install -g ios-deploy'
```

或者：
```
Error: Error code 65 for command: xcodebuild with args: -
xcconfig,/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/cordova/build-debug.xcconfig,-
project,TestProject.xcodeproj,-target,TestProject,-configuration,Debug,-sdk,iphonesimulator,-destination,platform=iOS 
Simulator,build,CONFIGURATION_BUILD_DIR=/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/build/emulator,SHARED_PRECOMP
S_DIR=/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/build/sharedpch
```
直接进入项目目录 TestProject/platforms/ios,使用Xcode8打开项目选择模拟器或者真机运行

注意：真机运行时，需要手机和电脑在同一局域网

3.6 修改TestPorject里面www文件夹下的index.html并保存，几秒后，就会在模拟器或手机看到变化

## 4 热更步骤

### 热更流程 

![热更流程图](https://github.com/nordnet/cordova-hot-code-push/raw/master/docs/images/update-workflow.png?raw=true)


① 应用启动

② 热更新插件初始化，并在后台加载更新模块 (update loader)

③ 更新模块 (update loader) 从 Cordova 项目配置 config.xml 文件中获取 config-file （热更新插件配置文件 chcp.json 的加载路径），然后加载配置
文件 chcp.json，获取其中的 release 版本号，对比当前的版本号，若二者不同，说明有新版本，执行下一步

④ 更新模块 (update loader) 从 chcp.json 配置文件中获取 content_url 作为 base url，然后加载 chcp.manifest 文件，或者新版本文件变更信息


⑤ 更新模块 (update loader) 根据 content_url 作为 base url，下载所有变更、新增文件

⑥ 如果一切顺利， 更新模块 (update loader) 发送通知，该更新已准备好进行安装

⑦ 安装更新，应用重定向到新版本页面

### 插件配置

Cordova Hot Code Push 热更新插件需要两个配置文件：

* Application config：`chcp.json` 包含发布相关信息：热更新代码版本号，应用 native side 版本号等等
* Content manifest：`chcp.manifest` 包含项目热更新代码(静态)文件信息：文件名和文件哈希值

这两个配置文件对于插件的运行缺一不可，前者描述了热更新代码的版本信息，后者提供了热更新代码文件的变更信息。借助 cordova-hot-code-push-cli 这个命令行工具可以辅助我们创建这两个配置文件。

#### Application config

> Application config holds information about the current release of the web project.

chcp.json 置于 www 目录根目录，例子如下：

{

  "name": "wps-*****",

  "content_url": "https://kss.ksyun.com/*****/*****/",

  "ios_identifier": "326CN*****",

  "android_identifier": "com.**********.*****.*****.*****.*****",

  "update": "resume",

  "release": "2017.06.07-16.30.20",

  "min_native_interface": 1

}

实用小技巧：创建模版cordova-hcp.json，放在项目根目录下，避免每次修改chcp.json文件

之后每次修改代码后，在项目根目录运行 cordova-hcp build
就可以在www目录生成跟模版一样的chcp.json,只是release时间不一样


1、配置项

① name 项目名称

② content_url web 项目文件在服务器上的存储路径（即 www 目录在云存储中的目录路径），热更新插件将此 URL 作为 base url，用于下载配置文件和项目更新
文件（必需的配置项）

③ release 描述 web 项目版本号，每一次发布的版本号必须唯一（默认使用时间戳，格式为：yyyy.MM.dd-HH.mm.ss），插件是将版本号进行字符串相等比较来判断
是否存在新版本（必需的配置项）

④ min_native_interface
Minimum version of the native side that is required to run this web content
cordova 项目主要包含两部分：web content 和 native side。前者是网页内容，后者是 cordova 插件，为网页提供原生 API 支持，web content 的运行是基
于 native side。

该配置项指明 web content 运行时 native side 的最低版本。在 native side 代码有变更后（cordova 插件新增/删除，native side 版本号更新），为了确
保 web content 能正常运行，需要更新 min_native_interface 的值

在应用 config.xml 配置中可以定义了 native side 的版本号，例如
```
<chcp>

    <native-interface version="5" />

</chcp>
```
例如当前项目 native side 的版本号是5：

如果服务器上配置文件 chcp.json 中的 min_native_interface 值为 5，那么符合要求，热更新后的 web content 能够在正常运行

如果服务器上配置文件 chcp.json 中的 min_native_interface 值为 10，高于 config.xml文件中 <native-interface />，那么热更新将无法正常进行。此
时，插件会提示错误 chcp_updateLoadFailed，提示应用需要更新升级

⑤ update 何时触发进行安装（install）代码热更新

代码热更新涉及两个主要过程：fetch update 和 install update。前者获取热更新变更文件，后者将获取到的更新文件安装到 App 中生效。此字段是针对后者，何时 install update，可选值：

start：应用启动，默认项（install update when application is launched）

resume：应用从后台恢复（install the update when application is resumed from background state）

now：下载更新后立即执行（install update as soon as it has been downloaded）
当然也可以禁用自动 install update，手动调用相关 API 进行 install

⑥ android_identifier / ios_identifier

android_identifier: Package name of the Android version of the application
ios_identifier: Identification number of the application

用于跳转到 Google Play Store 或者 App Store 该应用页面

2、如何生成该文件：

在 cordova 项目根目录执行 cordova-hcp init ，会通过命令行交互的方式，提示输入配置有关信息，创建该文件，会在项目根目录创建一个默认 Application config 文件 cordova-hcp.json

然后在每次应用打包时，再执行 cordova-hcp build 即可在 web 项目 www 根目录生成一个 chcp.json 文件。

Content manifest

Content manifest describes the state of the files inside your web project.

通过执行 cordova-hcp build 在 www 根目录自动生成 chcp.manifest 文件
```
[

  {

    "file": "import.html",

    "hash": "fc9301d4bd7381ba6033aa51884ed2dd"

  },

  {

    "file": "index.html",

    "hash": "f73630f62a531ab6c41cd067eb4f9b07"

  },

  {

    "file": "lib/lib.min.js",

    "hash": "6ecb0251f4c54f80586d9059dfc61de8"

  },

  ...

]
```
chcp.manifest 文件中包含的是 web content 静态文件信息，每一个项都包括两个字段：

file: 相对于 www 目录的文件路径

hash: 文件的 MD5 哈希值，用于判断文件是否发生变更 

基于 chcp.manifest 文件

> 在 fetch update 阶段，从服务器上获取新增、修改文件
> 在 install update 阶段，移除被删除文件

### Cordova config.xml 配置

Cordova 项目的 config.xml 文件用于设置项目配置选项，Cordova Hot Code Push 热更新插件的配置项也需要在该文件中进行相应的配置。

<chcp>

    <config-file url="https://kss.ksyun.com/********/chcp.json" />

    <auto-download enabled="false" />

    <auto-install enabled="false" />

    <native-interface version="1" />

</chcp>

config-file：配置文件 chcp.json 从服务器上加载的路径（必须的配置项）

auto-download：是否自动下载热更新代码，默认是 true

auto-install：是否自动安装热更新代码，默认是 true

native-interface：当前 native side 的版本号

可以禁用自动下载，安装热更新代码，通过手动调用执行。


### 将 www 目录打包上传到服务器或者云存储目录

新版本发布时，都需要执行如下处理：

* 对 `www` 目录下的静态文件进行打包，包括代码压缩，合并等等
* 执行 `cordova-hcp build` 生成 `chcp.json` 和 `chcp.manifest` 文件
* 将 `www` 目录下的静态文件上传至服务器或者云存储目录

## 5 补充说明

### 5.1 本地调试

### 5.2 手动更新
