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
  fontWeight: FontWeight.bold,
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

**_设置圆角/圆形_**

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


// BorderRadius详解：
new BorderRadius.all(Radius.circular(4)) // 四个圆角都是半径为4
new BorderRadius.circular(4), // 四个圆角都是半径为4,和上面的效果是一样的
new BorderRadius.horizontal( left: Radius.circular(10)), //左边的两个角的半径为10
new BorderRadius.horizontal(left: Radius.circular(10), right: Radius.circular(4)),//左边的两个角半径为10，右侧两个角半径为4
new BorderRadius.vertical( top: Radius.circular(6)), //上边两个角半径为6
new BorderRadius.only(
                      topLeft: Radius.circular(10),
                      topRight: Radius.circular(4),
                      bottomLeft: Radius.circular(6),
                      bottomRight: Radius.circular(20)
                     ), //坐上角半径为10，右上角4，左下角为6，右下角为20

```

圆形：CircleAvatar

[flutter 文档参考](https://book.flutterchina.club/chapter3/flutter_widget_intro.html)
