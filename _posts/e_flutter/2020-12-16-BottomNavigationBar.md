---
layout: post
title: Flutter 底部导航栏 BottomNavigationBar
summary:
categories: Flutter
technique: true
---

bottomNavigationBar 是属于 Scaffold 中的一个位于底部的控件。通常和 BottomNavigationBarItem 配合使用。

```dart
// 构造方法
BottomNavigationBar({
  Key key,
  @required this.items,
  this.onTap,
  this.currentIndex = 0,
  BottomNavigationBarType type,
  this.fixedColor,
  this.iconSize = 24.0,
})
```

属性 | 值类型 | 说明  
items | BottomNavigationBarItem 类型的 List | 底部导航栏的显示项  
onTap | ValueChanged < int > | 点击导航栏子项时的回调  
currentIndex | int | 当前显示项的下标  
type | BottomNavigationBarType | 底部导航栏的类型，有 fixed 和 shifting 两个类型，显示效果不一样  
fixedColor | Color | 底部导航栏 type 为 fixed 时导航栏的颜色，如果为空的话默认使用 ThemeData.primaryColor  
iconSize | double | BottomNavigationBarItem icon 的大小
