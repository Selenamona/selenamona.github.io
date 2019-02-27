---
layout:     post
title:      React Native
summary: 
categories: REACT
technique: true
---


## | 项目搭建

**准备**

`npm install -g yarn react-native-cli`

设置镜像源：

`yarn config set registry https://registry.npm.taobao.org --global`
`yarn config set disturl https://npm.taobao.org/dist --global`

**创建**

使用 React Native 命令行工具来创建新项目：`react-native init AwesomeProject`

准备模拟器，编译运行项目：`react-native run-android`


**自动刷新**

- 安卓：键盘 R 建按两次；
- reload JS


**报错**

react-native run-android 报错：
```
could not connect to development server. 
Try the follwing to fix the issue:
```

解决办法：

```
- 根目录运行：
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/ 

- 重新 react-native run-android
```


## | 参考
[React Native 中文网](https://reactnative.cn/docs/getting-started/)