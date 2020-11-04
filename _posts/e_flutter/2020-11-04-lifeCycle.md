---
layout: post
title: Flutter 生命周期
summary:
categories: Flutter
technique: true
---

<img src="/assets/images/flutter_lifeCycle.png" width="400">

State 生命周期可以分为三个阶段：创建，更新，销毁

1、创建阶段

State 初始化时会依次执行 ： 构造函数 > initState > didChangeDependencies > Widget build , 此时页面加载完成。

**_构造方法_**

State 生命周期的起点，Flutter 会通过调用 StatefulWidget.createState() 来创建一个 State。可以通过构造方法来接收父 Widget 传递的初始化 UI 配置数据，只执行一次

**_initState_**

在 State 对象被插入视图树的时候调用，只执行一次。widget 创建执行的第一个方法，可以再里面初始化一些数据

**_didChangeDependencies_**

用来处理 State 对象依赖关系变化，会在 initState() 调用结束后被调用

1、在初始化 initState 后执行；
2、显示/关闭其它 widget。
3、可执行多次；

**_build_**

build：构建视图。经过以上步骤，Framework 认为 State 已经准备好了，于是调用 build。需要根据父 Widget 传递的初始化数据，以及 State 当前状态，创建一个 Widget 并返回。可执行多次，初始化之后开始绘制界面，当 setState 触发的时候会再次被调用

2、 更新阶段

**_didUpdateWidget_**

当调用了 setState 将 Widget 的状态被改变时 didUpdateWidget 会被调用，Flutter 会创建一个新的 Widget 来绑定这个 State，并在这个方法中传递旧的 Widget

3、 销毁阶段

**_deactivate_**

在打开新的 Widget 或回到这个 widget 时会执行； 可执行多次；

**_dispose_**

当 State 对象从树中被永久移除时调用。此时，组件就要被销毁，所以可以进行最终的资源释放、移除监听事件

**_reassemble_**

点击闪电会执行，只用于调试时的 hot reload。 release 版本不会执行该函数。

## 常见场景

1、 Widget A 打开 Widget B： Navigator.push(B)

B 构造函数--->B initState--->B didChangeDependencies--->B build--->A deactive--->A didChangeDependencies.

2、Widget B 退出： Navigator.pop

A deactive--->A didChangeDependencies--->A build--->B deactive--->B dispose

参考链接：[state/app 生命周期](https://www.jianshu.com/p/9df4d7765657)
