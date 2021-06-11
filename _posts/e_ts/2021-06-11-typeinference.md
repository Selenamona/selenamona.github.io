---
layout:     post
title:      Typescript 类型推论
summary:
categories: Ts
technique: true
---


## 基本的类型推论

根据右侧的值推断左侧变量的类型

```javascript
let name = "lison";
name = 123; // error 不能将类型“123”分配给类型“string”
```

## 多类型联合

当我们定义一个数组或元组这种包含多个元素的值的时候，多个元素可以有不同的类型，这种时候 TypeScript 会将多个类型合并起来，组成一个联合类型。此时的 arr 的元素被推断为string | number，也就是元素可以是 string 类型也可以是 number 类型，除此两种类型外的类型是不可以的。

```javascript
let arr = [1, "a"];
arr = ["b", 2, false]; // error 不能将类型“false”分配给类型“string | number”
```

## 上下文类型

上下文类型根据左侧的类型推断右侧的一些类型

```javascript
window.onmousedown = function(mouseEvent) {
  console.log(mouseEvent.a); // error 类型“MouseEvent”上不存在属性“a”
};

```
