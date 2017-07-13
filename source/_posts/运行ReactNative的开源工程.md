---
title: 运行ReactNative的开源工程
date: 2016-04-30 22:14:27
tags: ReactNative
categories: ReactNative
---
在学习 React Native 的过程中, 需要多学习其他人的代码, 则运行其他项目必不可少. 完整的 React Native 包含两个部分, Android 和 iOS . 本文介绍一些在运行时的注意事项. 不要太纠结于全平台, 优先学习iOS平台.
<!--more-->
---


## 运行 iOS 版
React Native 的规范或编码更偏向于 iOS, 同时一些开源项目仅支持 iOS版本. 因此, 在运行时, 优先从 iOS 版本入手.

### 安装NPM, npm install, 下载 Node 库的依赖.

#### Q: 未安装库, 错误: 'RCTRootView.h' file not found

进入工程目录的 ios 文件夹, 启动xxx.xcodeproj文件. 使用 Xcode 启动, 或 使用 open xxx.xcodeproj 均可.

直接运行项目, 即可显示. iOS 工程会自启动服务, 加载本地数据.


#### Q: react-tools → aft无法下载

依赖的 RN 版本过低. 升级 RN 的版本, 即可, 目前低于版本16, 均无法下载 aft . RN 还在迭代过程中, 低版本不会向下兼容, 要抱有探索精神.

测试0.15.0的aft可以下载, 时间较长, 耐心等待.

loadDep:react-tools → aft ▌ ╢██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░╟
依赖 RN 的位置, package.json , 修改 react-native 项为较高版本. 高版本与低版本可能会不兼容.

在版本号前需要添加^, 如^0.18.0.
```
{
  "name": "WeatherProject",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node_modules/react-native/packager/packager.sh"
  },
  "dependencies": {
    "react-native": "^0.18.0"
  }
}
```


#### Q:低版本兼容, XCode运行报错

Log类接口修改, 添加 RCTLogSource source 即可.

```
RCTSetLogFunction(^(RCTLogLevel level, RCTLogSource source, NSString *fileName, NSNumber *lineNumber, NSString *message)
```

---

## 运行 Android 版
>多数 Demo 不包含 Android 版本. 直接于项目中, 观察 index.android.js 文件. 如果文件中添加新的模块, 则表示支持 Android 版; 如果是默认的HelloWorld, 则表示不支持. 不支持就不必启动, 使用 iOS 版本即可.

>不支持 Android 版本的原因, 主要是 React Native 的控件有一部分是有区分的, 专门开发 Android 版或 iOS 版. 默认控件通过后缀区分, IOS 表示 iOS 独有, Android 表示 Android 独有, 未添加则表示两者均可.

1. 使用 react-native 命令启动 Android 项目.

2. react-native run-android *(即shell命令: cd android && ./gradlew installDebug)*

3. 注意修改 Gradle 的构建版本

```
classpath 'com.android.tools.build:gradle:1.2.3' 
```
否则报错:

Error while uploading app-debug.apk : Unknown failure
低版本 RN 运行 Android 项目, 问题多多, 请耐心解决. 推荐使用 iOS 进行学习与演示.


---
#### 运行项目只是开始, 要想熟练使用, 还要多多练习 RN 的编码风格.

OK, that's all! Enjoy it!


[原文链接：http://www.jianshu.com/p/e366cffec87a](http://www.jianshu.com/p/e366cffec87a)