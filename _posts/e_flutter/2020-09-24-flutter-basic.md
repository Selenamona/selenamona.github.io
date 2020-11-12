---
layout: post
title: Flutter 入门
summary: 框架概述
categories: Flutter
technique: true
---

# | 语法

## | [基础 Widgets](https://flutterchina.club/widgets/basics/)

**_scaffold（脚手架）_**

实现基本的 Material Design 可视化布局结构。Scaffold 类提供了很多的 Material 组件。

- AppBar-通常显示在使用 appBar 属性的应用顶部。
- BottomNavigationBar-通常使用 bottomNavigationBar 属性在应用程序底部显示，此功能向图中所示一样，无法做定制，- 只能以图片和文本形式存在。
- BottomAppBar-通常使用 bottomNavigationBar 属性显示在应用程序的底部，用来构建定制化的底部导航栏或者布局。
- FloatingActionButton-圆形按钮，通常使用 floatingActionButton 属性显示在应用程序的右下角。
- SnackBar-通常显示在应用程序底部附近的临时通知

[中文文档](https://flutterchina.club/get-started/codelab/)

# | Flutter Widget 框架概述

Flutter Widget 采用现代响应式框架构建，这是从 React 中获得的灵感，中心思想是用 widget 构建你的 UI。

Widget 描述了他们的视图在给定其当前配置和状态时应该看起来像什么。当 widget 的状态发生变化时，widget 会重新构建 UI，Flutter 会对比前后变化的不同， 以确定底层渲染树从一个状态转换到下一个状态所需的最小更改（译者语：类似于 React/Vue 中虚拟 DOM 的 diff 算法）。

runApp 函数接受给定的 Widget 并使其成为 widget 树的根。

1. widget
   （1）概念
   widget 的主要工作是通过实现 build 函数 来构建自身。一个 widget 通常由一些低级别的 widget 组成，flutter 框架依次的构建这些低级别的 widget，直到构建到最底层的子 widget 时，它会计算并描述 widget 的几何形状

（2）分类
StatelessWidget：无状态，比如标题栏中的标题
StatefulWidget：有状态，创建时需要指定一个 State ，在需要更新 UI 时调用 setState(VoidCallbackfn)，并在 VoidCallback 中改变一些些变量数值等，组件会重新 build 以达到数显状态/UI 的效果。

**_管理状态的常见方法：_**

- widget 管理自己的 state
- 父 widget 管理子 widget 状态
- 混合管理

**_决定状态管理的原则：_**

- 有关用户数据由父 widget 管理
- 有关界面效果由 widget 本身管理
- 状态被不同 widget 共享，由他们共同的父 widget 管理

（3）Widget 支持库
flutter 提供了一套丰富、强大的基础 widget ，在此基础上还提供了 Android 默认风格库： Material 与 IOS 风格库：[Cupertino](https://flutterchina.club/widgets/cupertino/)。

（4）基础 Widget

- Text：文本。new Center：将子组件放置在自己的中心位置。
- Row：水平布局，基于 web Flexbox 布局模型。
- Column：垂直布局，基于 web Flexbox 布局模型。
- Stack：取代线性布局，Stack 允许子 widget 堆叠，可以使用 Positioned 来定位他们相对于 Stack 的上下左右四条边的位置。Stacks 是基于 Web 开发中的绝度定位（absolute positioning )布局模型设计的。
- Container：矩形元素，可以装饰 BoxDecoration，如 background、边框、阴影，它可以具有边距（margins）、填充（padding）和应用与其大小的约束（constraints）。Container 可以使用矩阵在三维空间中对其进行变换

2. element

widget 中主要包含了组件的配置数据，但它并不代表最终绘制在屏幕上的显示元素，真正代表屏幕上显示元素的是 element，widget 是 element 的配置数据，一个 widget 可同时对应多个 element

3. state

每一个 StatefulWidget 类都会对应一个 State 类，State 表示与其对应的 StatefulWidget 要维护的状态，保存的状态信息可以在 build 时被获取，同时，在 widget 生命周期中可以被改变，改变发生时，可以调用其 setState() 方法通知 framework 发生改变，framework 会重新调用 build 方法重构 widget 树，最终完成更新 UI 的目的。

state 中包含两个常用属性：widget 和 context。widget 属性表示当前正在关联的 widget 实例，但关联关系可能会在 widget 重构时发生变化（framework 会动态设置 widget 属性为最新的 widget 对象）。context 属性是 buildContext 类的实例，表示构建 widget 的上下文，每个 widget 都有一个自己的 context 对象，它包含了查找、遍历当前 widget 树的方法。

**_State 的生命周期：_**

在 StatefulWidget 调用 createState 之后，框架将新的状态对象插入树中，然后调用状态对象的 initState。 子类化 State 可以重写 initState，以完成仅需要执行一次的工作。 例如，您可以重写 initState 以配置动画或订阅 platform services。initState 的实现中需要调用 super.initState。

当一个状态对象不再需要时，框架调用状态对象的 dispose。 您可以覆盖该 dispose 方法来执行清理工作。例如，您可以覆盖 dispose 取消定时器或取消订阅 platform services。 dispose 典型的实现是直接调用 super.dispose。

- initState：当前 widget 对象插入 widget 树中时调用
- didChangeDependencies：当前 State 对象的依赖项发生变化时调用
- build：绘制当前界面布局时调用
- reassemble：使用热重载时调用
- didUpdateWidget：widget 配置内容有变动重构时调用
- deactivate：当前 widget 对象从 widget 树中移出时调用
- dispose：当前 widget 对象从 widget 树中永久删除时调用

### | [widget 分类](https://flutter.dev/docs/development/ui/widgets/layout)

1. Single-child layout widgets；父级 widget 中只能包含唯一一个子级 widget
   主要有： Contrainer、Padding、Center 等等
2. Multi-child layout widgets；父级 widget 中能包含多个子级 widget
   主要有： Row、Column 等等

# | 属性

```javascript

new Row(
mainAxisAlignment: MainAxisAlignment.spaceBetween,
// mainAxisAlignment：
// start, // 将子控件放在主轴的开始位置
// end, // 将子控件放在主轴的结束位置
// center, // 将子控件放在主轴的中间位置
// spaceBetween, // 将主轴空白位置进行均分，排列子元素，手尾没有空隙
// spaceAround, // 将主轴空白区域均分，使中间各个子控件间距相等，首尾子控件间距为中间子控件间距的一半
// spaceEvenly, //将主轴空白区域均分，使各个子控件间距相等
mainAxisSize: MainAxisSize.max,
// 交叉轴的布局方式，对于column来说就是水平方向的布局方式
crossAxisAlignment: CrossAxisAlignment.center,
//就是字child的垂直布局方向，向上还是向下
verticalDirection: VerticalDirection.down,
```

## | 坑

1. Flutter 中 Row 中不能直接使用 textfield 控件。需添加 expanded

[实战文档参考 book.flutterchina.club/](https://book.flutterchina.club/chapter3/flutter_widget_intro.html)

[dart 文档](https://dart.cn/guides/language/language-tour)
