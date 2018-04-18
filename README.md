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

### 1.1 检测是否安装了node.js

打开终端输入`npm --version`，若无打印版本则需先安装node.js

### 1.2 命令行安装cordova

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

Error: ios-deploy was not found. Please download, build and install version 1.8.3 or greater from https://github.com/phonegap/ios-deploy into your path, or do 'npm install -g ios-deploy'
```

或者：
```
Error: Error code 65 for command: xcodebuild with args: -xcconfig,/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/cordova/build-debug.xcconfig,-project,TestProject.xcodeproj,-target,TestProject,-configuration,Debug,-sdk,iphonesimulator,-destination,platform=iOS Simulator,build,CONFIGURATION_BUILD_DIR=/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/build/emulator,SHARED_PRECOMPS_DIR=/Users/didi/Desktop/xcode/demo/TestProject/platforms/ios/build/sharedpch
```
直接进入项目目录 TestProject/platforms/ios,使用Xcode8打开项目选择模拟器或者真机运行

注意：真机运行时，需要手机和电脑在同一局域网

3.6 修改TestPorject里面www文件夹下的index.html并保存，几秒后，就会在模拟器或手机看到变化

## 4 热更新步骤

## 5 补充说明

### 5.1 本地调试

### 5.2 手动更新
