---
layout:     post
title:      Typescript 装饰器
summary:
categories: Ts
technique: true
---


## 基础

多个装饰器，会先执行装饰器工厂函数获取所有装饰器，然后再从后往前执行装饰器的逻辑

```javascript
  function setName() {
      console.log('get setName')
      return function(target) {
        console.log('setName')
      }
    }
    function setAge() {
      console.log('get setAge')
      return function(target) {
        console.log('setAge')
      }
    }
    @setName()
    @setAge()
    class Test { }
    // 打印出来的内容如下：
    /*
     'get setName'
     'get setAge'
     'setAge'
     'setName'
    */
```

## 类装饰器

类装饰器在类声明之前声明，要记着装饰器要紧挨着要修饰的内容，类装饰器应用于类的声明。
类装饰器表达式会在运行时当做函数被调用，它由唯一一个参数，就是装饰的这个类

```javascript
// 通过装饰器，我们就可以修改类的原型对象和构造函数
function addName(constructor: { new (): any }) {
  constructor.prototype.name = "lison";
}
@addName
class A {}
interface A { // 定义一个同名接口，通过声明合并解决定义的类 A 没有定义属性 name
  name: string;
}
const a = new A();
console.log(a.name); // "lison"
```
```javascript
// 如果类装饰器返回一个值，那么会使用这个返回的值替换被装饰的类的声明，所以我们可以使用此特性修改类的实现
function classDecorator<T extends { new (...args: any[]): {} }>(target: T) {
  return class extends target {
    newProperty = "new property";
    hello = "override";
  };
}
@classDecorator
class Greeter {
  property = "property";
  hello: string;
  constructor(m: string) {
    this.hello = m;
  }
}
console.log(new Greeter("world"));
// {
//     hello: "override"
//     newProperty: "new property"
//     property: "property"
// }

// 在这个例子中，我们装饰器的返回值还是返回一个类，但是这个类不继承被修饰的类了，所以最后打印出来的实例，只包含装饰器中返回的类定义的实例属性，被装饰的类的定义被替换了。
function classDecorator(target: any): any {
  return class {
    newProperty = "new property";
    hello = "override";
  };
}
@classDecorator
class Greeter {
  property = "property";
  hello: string;
  constructor(m: string) {
    this.hello = m;
  }
}
console.log(new Greeter("world"));
// {
//   hello: "override"
//   newProperty: "new property"
// }
```

## 方法装饰器

方法装饰器用来处理类中方法，它可以处理方法的属性描述符，可以处理方法定义。方法装饰器在运行时也是被当做函数调用，含 3 个参数：
1. 装饰静态成员时是类的构造函数，装饰实例成员时是类的原型对象；
2. 成员的名字；
3. 成员的属性描述符。

>  JS 的知识——属性描述符：对象可以设置属性，如果属性值是函数，那这个函数称为方法。每一个属性和方法在定义的时候，都伴随三个属性描述符configurable、writable和enumerable，分别用来描述这个属性的可配置性、可写性和可枚举性。这三个描述符，需要使用 ES5 才有的 Object.defineProperty 方法来设置

## 访问器装饰器

TS 不允许同时装饰一个成员的 get 和 set 访问器，只需要这个成员 get/set 访问器中定义在前面的一个即可。
访问器装饰器也有三个参数，和方法装饰器是一模一样的

```javascript
function enumerable(bool: boolean) {
  return function(
    target: any,
    propertyName: string,
    descriptor: PropertyDescriptor
  ) {
    descriptor.enumerable = bool;
  };
}
class Info {
  private name: string;
  constructor(name: string) {
    this.name = name;
  }
  @enumerable(false)
  get name() {
    return this.name;
  }
  @enumerable(false) // error 不能向多个同名的 get/set 访问器应用修饰器
  set name(name) {
    this.name = name;
  }
}
```

## 属性装饰器

属性装饰器声明在属性声明之前，它有 2 个参数，和方法装饰器的前两个参数是一模一样的。属性装饰器没法操作属性的属性描述符，它只能用来判断某各类中是否声明了某个名字的属性

```javascript
function printPropertyName(target: any, propertyName: string) {
  console.log(propertyName);
}
class Info {
  @printPropertyName
  name: string;
  @printPropertyName
  age: number;
}
```

## 参数装饰器
参数装饰器有 3 个参数，前两个和方法装饰器的前两个参数一模一样，参数装饰器的返回值会被忽略
1. 装饰静态成员时是类的构造函数，装饰实例成员时是类的原型对象；
2. 成员的名字；
3. 参数在函数参数列表中的索引。

```javascript

function required(target: any, propertName: string, index: number) {
  console.log(`修饰的是${propertName}的第${index + 1}个参数`);
}
class Info {
  name: string = "lison";
  age: number = 18;
  getInfo(prefix: string, @required infoType: string): any {
    return prefix + " " + this[infoType];
  }
}
interface Info {
  [key: string]: string | number | Function;
}
const info = new Info();
info.getInfo("hihi", "age"); // 修饰的是getInfo的第2个参数

```
