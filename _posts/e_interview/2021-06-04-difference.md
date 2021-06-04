<!--
 * @Descripttion:
 * @Author: Mona
 * @Date: 2021-06-04 15:14:46
 * @LastEditTime: 2021-06-04 15:23:27
-->
---
layout: post
title: 面试题类型之比较不同
summary:
categories: interview
technique: true
---

## typeof & instanceof

typeof 和 instanceof 都是用来判断变量类型的，两者的区别在于：

1. typeof 判断所有变量的类型，返回值为6个字符串，分别为 string、Boolean、number、function、object、undefined。

2. typeof 对于丰富的对象实例如 null、array、object 以及函数实例（new + 函数），只能返回"Object"字符串，得不到真正的数据类型。

3. instanceof 用来判断对象，代码形式为obj1 instanceof obj2（obj1是否是obj2的实例），obj2必须为对象，否则会报错！其返回值为布尔值。

4. instanceof 可以对不同的对象实例进行判断，判断方法是根据对象的原型链依次向下查询，如果obj2的原型属性存在obj1的原型链上，（obj1 instanceof obj2）值为true。

```javascript
var a = [34,4,3,54],
b = 34,
c = 'adsfas',
d = function(){console.log('我是函数')},
e = true,
f = null,
g;

console.log(typeof(a));//object
console.log(typeof(b));//number
console.log(typeof(c));//string
console.log(typeof(d));//function
console.log(typeof(e));//boolean
console.log(typeof(f));//object
console.log(typeof(g));//undefined
```
