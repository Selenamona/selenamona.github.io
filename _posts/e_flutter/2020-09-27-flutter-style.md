---
layout: post
title: Flutter 样式
summary:
categories: Flutter
technique: true
---

## | _Image_

目前 Flutter 的 Image 支持以下图像格式：JPEG，PNG，GIF，动画 GIF，WebP，动画 WebP，BMP 和 WBMP。Flutter 中获取图片的方式提供了以下几种：

- Image（） 从 ImageProvider 获取图像
- Image.asset（） 本资源文件
- Image.file（） 文件形式
- Image.memory（）内存中
- Image.network（）网络中获取

**_设置图片缩放模式_**
width、height：用于设置图片的宽、高，当不指定宽高时，图片会根据当前父容器的限制，尽可能的显示其原始大小，如果只设置 width、height 的其中一个，那么另一个属性默认会按比例缩放，但可以通过下面介绍的 fit 属性来指定适应规则。

fit：该属性用于在图片的显示空间和图片本身大小不同时指定图片的适应模式。适应模式是在 BoxFit 中定义，它是一个枚举类型，有如下值：

- fill：会拉伸填充满显示空间，图片本身长宽比会发生变化，图片会变形。
- cover：会按图片的长宽比放大后居中填满显示空间，图片不会变形，超出显示空间部分会被剪裁。
- contain：这是图片的默认适应规则，图片会在保证图片本身长宽比不变的情况下缩放以适应当前显示空间，图片不会变形。
- fitWidth：图片的宽度会缩放到显示空间的宽度，高度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
- fitHeight：图片的高度会缩放到显示空间的高度，宽度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
- none：图片没有适应策略，会在显示空间内显示图片，如果图片比显示空间大，则显示空间只会显示图片中间部分。

```dart
new Image.network(
  imgUrl,
  width: 100.0,
  height: 100.0,
  fit: BoxFit.fitHeight,
  // color和 colorBlendMode：在图片绘制时可以对每一个像素进行颜色混合处理，color指定混合色，而colorBlendMode指定混合模式，
  color: Colors.blue,
  colorBlendMode: BlendMode.difference,
  repeat: ImageRepeat.repeatY, // 图片的重复规则
);
```

## | color

Flutter 中颜色设置的方式有很多种，其中最常用的有下列几种：

```javascript
Color c1 = Color(0xFF0099ff);
Color c2 = Color.fromRGBO(60, 170, 250, 1);
Color c3 = Color.fromARGB(255, 60, 170, 250);
Color c4 = Colors.blue;
Color c5 = Colors.red[300];
```

**_Color 是 Flutter 提供的一个颜色类。_**

1. Color() 调用 Color 构造函数时，传入一个颜色的 ARGB 值。`Color(0xFF0099ff)`
   FF 是透明度的十六进制表示方式，取值范围是：00 ～ FF（透明 ～ 不透明）；
   0099ff 是颜色 RGB(red/green/blue) 值的十六进制表示方式，

2. Color.fromRGBO() - 该方法接收 4 个参数，分别与 RGBO 对应：red、green、blue、opacity。`Color.fromRGBO(60, 170, 250, 1);`
   RGB 表示红绿蓝三种颜色，取值范围是：0 ～ 255；
   O 表示颜色透明度，取值范围是：0 ～ 1（透明 ～ 不透明）；

3. Color.fromARGB() - 该方法接收 4 个参数，与 ARGB 对应的是：alpha、red、green、blue。`Color.fromARGB(255, 60, 170, 250);`
   A 表示颜色透明度，取值范围是：0 ～ 255；
   RGB 表示红绿蓝三种颜色，取值范围是：0 ～ 255（透明 ～ 不透明）；

**_Colors 实际上就是 Material 库中对 Color 的进一步封装。它将一些常用颜色的十六进制值封装成了我们更熟悉的英文单词形式_**

颜色后面的中括号是用来设置当前颜色的深浅度，可取的值有 10 种：50、100、200 ... 900，值越大，对应的颜色越深。500 就等同于当前颜色自身。
注：并不是所有的颜色英文单词都能用，只能使用 Material 中封装了的颜色

Color c4 = Colors.red;
Color c5 = Colors.white;
Color c6 = Colors.red[50];
Color c6 = Colors.blue[900];

## | 设置单边及多边阴影

```javascript
// 设置上/左/右三边阴影
BoxShadow(
    color: Color(0xFF8FECFF),
    offset: Offset(-5, -5), // 阴影xy轴偏移量
    blurRadius: 10, // 阴影模糊程度
    spreadRadius: -4 // 阴影扩散程度
    ),
BoxShadow(
    color: Color(0xFF8FECFF),
    offset: Offset(5, -5), // 阴影xy轴偏移量
    blurRadius: 10, // 阴影模糊程度
    spreadRadius: -4 // 阴影扩散程度
    ),

// 设置左/上两边阴影
BoxShadow(
    color: Color(0xFF8FECFF),
    offset: Offset(-5, -5), // 阴影xy轴偏移量
    blurRadius: 10, // 阴影模糊程度
    spreadRadius: -4 // 阴影扩散程度
    )
// 设置上单边阴影
BoxShadow(
    color: Color(0xFF8FECFF),
    offset: Offset(0, -6), // 阴影xy轴偏移量
    blurRadius: 0, // 阴影模糊程度
    spreadRadius: -2 // 阴影扩散程度
    ),
// 设置下单边阴影
BoxShadow(
    color: Color(0xA9FF00B3),
    offset: Offset(0, 8), // 阴影xy轴偏移量
    blurRadius: 0, // 阴影模糊程度
    spreadRadius: -2 // 阴影扩散程度
    )
```

## | 文本样式设置

```javascript
// maxLines、overflow：指定文本显示的最大行数，超出显示省略号
Text("Hello world! I'm Jack. "*4, // 字符串重复四次
  maxLines: 1,
  overflow: TextOverflow.ellipsis,
);

Text(
"aaa",
textAlign: TextAlign.left, // 文本对齐方式
textScaleFactor: 1.5, // 代表文本相对于当前字体大小的缩放因子，相对于去设置文本的样式style属性的fontSize，它是调整字体大小的一个快捷方式。该属性的默认值可以通过MediaQueryData.textScaleFactor获得，如果没有MediaQuery，那么会默认值将为1.0。
// TextStyle用于指定文本显示的样式如颜色、字体、粗细、背景等
style: TextStyle(
  color: Colors.blue,
  fontSize: 18.0, // 精确指定字体大小
  height: 1.2, // 该属性用于指定行高，但它并不是一个绝对值，而是一个因子，具体的行高等于fontSize*height
  fontFamily: "Courier",
  background: new Paint()..color=Colors.yellow,
  decoration:TextDecoration.underline,
  decorationStyle: TextDecorationStyle.dashed),
)
// 去除文字双下划线：
decoration: TextDecoration.none
// 文字添加删除线：
decoration: TextDecoration.lineThrough,
decorationColor: Colors.red,
decorationStyle: TextDecorationStyle.solid
```

```javascript
// 通过TextSpan实现了一个基础文本片段和一个链接片段拼接
Text.rich(TextSpan(
  children: [
    TextSpan(
      text: "Home: "
    ),
    TextSpan(
      text: "https://flutterchina.club",
      style: TextStyle(
        color: Colors.blue
      ),
      recognizer: _tapRecognizer // 点击事件
    ),
  ]
))
```

在 Widget 树中，文本的样式默认是可以被继承的（子类文本类组件未指定具体样式时可以使用 Widget 树中父级设置的默认样式），因此，如果在 Widget 树的某一个节点处设置一个默认的文本样式，那么该节点的子树中所有文本都会默认使用这个样式，而 DefaultTextStyle 正是用于设置默认文本样式的。

```dart
DefaultTextStyle(
  //1.设置文本默认样式
  style: TextStyle(
    color:Colors.red,
    fontSize: 20.0,
  ),
  textAlign: TextAlign.start,
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: <Widget>[
      Text("hello world"),
      Text("I am Jack"),
      Text("I am Jack",
        style: TextStyle(
          inherit: false, // 2.不继承默认样式
          color: Colors.grey
        ),
      ),
    ],
  ),
);
```

## | 容器/设置边距

1. EdgeInsets.fromLTRB(double left, double top, double right, double bottom)：分别指定四个方向的填充。 `EdgeInsets.fromLTRB(20.0,.0,20.0,20.0)`
2. EdgeInsets.all(double value) : 所有方向均使用相同数值的填充。`EdgeInsets.all(16.0)`
3. EdgeInsets.only({left, top, right ,bottom })：可以设置具体某个方向的填充(可以同时指定多个方向)。`EdgeInsets.only(top: 10.0, bottom: 10)`
4. EdgeInsets.symmetric({ vertical, horizontal })：用于设置对称方向的填充，vertical 指 top 和 bottom，horizontal 指 left 和 right。`EdgeInsets.symmetric(vertical: 8.0)`

**_尺寸限制_**

**_ConstrainedBox_** 用于对子组件添加额外的约束。例如，如果你想让子组件的最小高度是 80 像素，你可以使用 const BoxConstraints(minHeight: 80.0)作为子组件的约束。

```javascript
ConstrainedBox(
  constraints: BoxConstraints(
    minWidth: double.infinity, // 宽度尽可能大
    minHeight: 50.0 // 最小高度为50像素
  ),
  child: Container(
      height: 5.0, // 即使将Container的高度设置为5像素，但是最终却是50像素，这正是ConstrainedBox的最小高度限制生效了
      child: redBox
  ),
)
```

**_BoxConstraints_** 用于设置限制条件，它的定义如下：

```dart
const BoxConstraints({
  this.minWidth = 0.0, //最小宽度
  this.maxWidth = double.infinity, //最大宽度
  this.minHeight = 0.0, //最小高度
  this.maxHeight = double.infinity //最大高度
})
```

BoxConstraints 还定义了一些便捷的构造函数，用于快速生成特定限制规则的 BoxConstraints，如 BoxConstraints.tight(Size size)它可以生成给定大小的限制。

**_SizedBox_** 用于给子元素指定固定的宽高

```dart
SizedBox((width: 80.0), (height: 80.0), (child: redBox));
// 实际上SizedBox只是ConstrainedBox的一个定制，上面代码等价于：
ConstrainedBox(
  constraints: BoxConstraints.tightFor(width: 80.0,height: 80.0),
  child: redBox,
)
// 也等价于：
BoxConstraints(minHeight: 80.0,maxHeight: 80.0,minWidth: 80.0,maxWidth: 80.0)
```

**_多重限制_**

多重限制时，对于 minWidth 和 minHeight 来说，是取父子中相应数值较大的。实际上，只有这样才能保证父限制与子限制不冲突。

```javascript
// 最终显示效果是宽90，高60
ConstrainedBox(
    constraints: BoxConstraints(minWidth: 60.0, minHeight: 60.0), //父
    child: ConstrainedBox(
      constraints: BoxConstraints(minWidth: 90.0, minHeight: 20.0),//子
      child: redBox,
    )
)
```

**_设置圆角_**

```javascript
// ClipRRect例子(可自定义圆角):
new ClipRRect(
  borderRadius: BorderRadius.circular(50),
  child: Container()
)
// ClipOval例子(默认全圆角):
new ClipOval(
  child: Container()
)
```

**_容器类组件_**

Padding：可以给其子节点添加填充（留白），和边距效果类似。如下设置 text 和 image 的间距

```dart
Container(
  width: 60,
  margin: EdgeInsets.only(bottom: 20),
  child: new Column(children: <Widget>[
    new Image.asset(iconList[i]['icon'],
        width: 30, height: 30, fit: BoxFit.fitWidth),
    Padding(
        padding: EdgeInsets.only(top: 5),
        child: Text(iconList[i]['title']))
  ]))
```

**_Flexible 和 Expanded_**

Flexible 是一个控制 Row、Column、Flex 等子组件如何布局的组件。但不强制子组件填充可用空间。

Expanded 组件可以使 Row、Column、Flex 等子组件在其主轴方向上展开并填充可用空间
Expanded 组件必须用在 Row、Column、Flex 内，并且从 Expanded 到封装它的 Row、Column、Flex 的路径必须只包括 StatelessWidgets 或 StatefulWidgets 组件(不能是其他类型的组件，像 RenderObjectWidget，它是渲染对象，不再改变尺寸了，因此 Expanded 不能放进 RenderObjectWidget)。

```dart
Row(
  children: <Widget>[
    Container( /// 此组件在主轴方向占据48.0逻辑像素
      width: 48.0
    ),
    Expanded(
      child: Container() /// 此组件会填满Row在主轴方向的剩余空间，撑开Row
    )
  ]
）
```

**_GridView_**

```javascript
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3, //横轴三个子 widget
    childAspectRatio: 1.0 //宽高比为 1 时，子 widget
  ),
  children:<Widget>[
    Icon(Icons.ac_unit),
    Icon(Icons.airport_shuttle),
    Icon(Icons.all_inclusive),
    Icon(Icons.beach_access),
    Icon(Icons.cake),
    Icon(Icons.free_breakfast)
  ]
);
```

## | 线性布局（Row/Colum）

- textDirection - 文字方向从左往右（TextDirection.ltr）；从右往左（TextDirection.rtl）
- MainAxisSize - 在主轴(水平)方向占用的空间，默认是 MainAxisSize.max，表示尽可能多的占用水平方向的空间，此时无论子 widgets 实际占用多少水平空间，Row 的宽度始终等于水平方向的最大宽度；而 MainAxisSize.min 表示尽可能少的占用水平空间，当子组件没有占满水平剩余空间，则 Row 的实际宽度等于所有子组件占用的的水平空间
- mainAxisAlignment - MainAxisAlignment.start / MainAxisAlignment.end / MainAxisAlignment.center
- verticalDirection - 纵轴（垂直）的对齐方向，默认是 VerticalDirection.down，表示从上到下。（VerticalDirection.up 表示从下到上）
- CrossAxisAlignment - 纵轴方向的对齐方式 CrossAxisAlignment.start / CrossAxisAlignment.end / CrossAxisAlignment.center
- children - 子组件数组

```dart
Row({
  TextDirection textDirection,
  MainAxisSize mainAxisSize = MainAxisSize.max,
  MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
  VerticalDirection verticalDirection = VerticalDirection.down,
  CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
  List<Widget> children = const <Widget>[],
})
```

[flutter 文档参考](https://book.flutterchina.club/chapter3/flutter_widget_intro.html)
