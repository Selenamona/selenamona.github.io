---
layout:     post
title:      Typescript 类型别名 | 联合类型 | 交叉类型
summary:
categories: Ts
technique: true
---

## 类型别名

***定义类型别名***

1. 使用 type 关键字

```javascript
type TypeString = string;
let str: TypeString;
// str = 123; // error Type '123' is not assignable to  type 'string'
```

2. 类型别名也可以使用泛型

```javascript
type PositionType<T> = { x: T; y: T };
const position1: PositionType<number> = {
  x: 1,
  y: -1
};
const position2: PositionType<string> = {
  x: "right",
  y: "top"
};
```
类型别名只是为其它类型起了个新名字来引用这个类型，所以当它为接口起别名时，不能使用 extends 和 implements。接口和类型别名有时可以起到同样作用：

```javascript
type Alias = {  num: number; };
interface Interface { num: number; }
let alias: Alias = { num: 123 };
let interface: Interface = { num: 321 };
alias = interface;
```

什么时候用类型别名，什么时候用接口呢？
- 当你定义的类型要用于拓展，即使用 implements 等修饰符时，用接口。
- 当无法通过接口，并且需要使用联合类型或元组类型，用类型别名。


```ts
// 使用 type 创建类型别名。类型别名常用于联合类型
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```


## 联合类型

联合类型实际是几个类型的结合，但是和交叉类型不同，联合类型是要求只要符合联合类型中任意一种类型即可，它使用 | 符号定义


1. 字符串字面量类型：字符串字面量类型其实就是字符串常量，与字符串类型(string)不同的是它是具体的值。

```javascript
type Name = "Lison";
const name1: Name = "test"; // error 不能将类型“"test"”分配给类型“"Lison"”
const name2: Name = "Lison";
// 使用联合类型来使用多个字符串
type Direction = "north" | "east" | "south" | "west";
function getDirectionFirstLetter(direction: Direction) {
  return direction.substr(0, 1);
}
getDirectionFirstLetter("test"); // error 类型“"test"”的参数不能赋给类型“Direction”的参数
getDirectionFirstLetter("east");
```

2. 数字字面量类型
```javascript
type Age = 18;
interface Info {
  name: string;
  age: Age;
}
const info: Info = {
  name: "Lison",
  age: 28 // error 不能将类型“28”分配给类型“18”
};

```

## 交叉类型

交叉类型就是取多个类型的并集，使用 & 符号定义，被&符链接的多个类型构成一个交叉类型，表示这个类型同时具备这几个连接起来的类型的特点

```typescript
// 传入的两个参数分别是带有属性 name 和 age 的两个对象，所以它俩的交叉类型要求返回的对象既有 name 属性又有 age 属性。
const merge = <T, U>(arg1: T, arg2: U): T & U => {
  let res = <T & U>{}; // 这里指定返回值的类型兼备T和U两个类型变量代表的类型的特点
  res = Object.assign(arg1, arg2);
  return res;
};
const info1 = {
  name: "lison"
};
const info2 = {
  age: 18
};
const lisonInfo = merge(info1, info2);
// console.log(lisonInfo.address); // error 类型“{ name: string; } & { age: number; }”上不存在属性“address”
```
