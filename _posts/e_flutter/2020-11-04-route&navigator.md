---
layout: post
title: Flutter 路由/导航
summary:
categories: Flutter
technique: true
---

管理多个页面时有两个核心概念和类：Route 和 Navigator。 一个 route 是一个屏幕或页面的抽象，Navigator 是管理 route 的 Widget。Navigator 可以通过 route 入栈和出栈来实现页面之间的跳转。

## | 路由基本使用

**_页面跳转_**

```dart
// 跳准到SecondScreen页面，并传递变量index
Navigator.push(
    context,
    new MaterialPageRoute(builder: (context) => new SecondScreen(index)),
  );
```

**_页面返回_**

```dart
// 不传参返回
Navigator.pop(context);
// 传参返回
Navigator.pop(context,"aaa");
```

## | 定制路由

定制路由可以实现自定义的过渡效果等。定制路由有两种方式：
1、继承路由子类，如：PopupRoute、ModalRoute 等。
2、使用 PageRouteBuilder 类通过回调函数定义路由。
