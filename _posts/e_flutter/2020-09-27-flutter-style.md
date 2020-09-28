---
layout: post
title: Flutter 样式
summary:
categories: Flutter
technique: true
---

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
// 去除文字双下划线：TextDecoration.none
Text(
"aaa",
style: TextStyle(decoration: TextDecoration.none),
)

// 文字添加删除线：
decoration: TextDecoration.lineThrough,
decorationColor: Colors.red,
decorationStyle: TextDecorationStyle.solid
```
