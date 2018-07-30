---
layout:     post
title:      原型与继承
summary:  
categories: Js
technique: true
---



### 创建对象  

Javascript中创建对象方式：    
1. Object    
```javascript
    var girl = new Object();
    girl.name = "小美";
    girl.age = "18";
```
2. 字面量   
```javascript
    var girl = {
        name:"小美",
        age:"18",
    }
```

### 工厂模式  

若创建一个对象，上面两种方法很简单，但若创建多个具有相同属性的对象，就要把相同的代码重复n次。对于强迫症（懒）的童鞋恐怕就要烦躁了。因此想到了封装，也就是常说的工厂模式。   

```javascript
function girl(name, age) {
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    return obj;
}
var girl1 = girl("easy1", 20);    
var girl2 = girl("easy2", 20);    
``` 

### 构造函数

构造函数与普通函数区别在于调用方法。对于任意函数，使用new操作符调用，那么它就是构造函数。       
```javascript
function Girl(name, age) {
    this.name = name;
    this.age = age;
    this.say = function(){
        console.log("我叫" + this.name + ",今年" + this.age + "岁！")
    }
}

const girl1 = new Girl('娟娟',12);
const girl2 = new Girl('莉莉',18);
```
 
通过构造函数创建实例时，每创建一个实例，都要重新创建一个say方法，无疑造成了很大的浪费。

### 原型  

* 原型对象用来存放实例中共有的属性和方法。   

* 构造函数拥有一个prototype属性，指向原型对象。原型对象拥有一个constructor属性，指向构造函数（构造函数/原型对象==互指关系）。 

* 每个实例对象都包含一个指向原型对象的内部指针。【通过实例对象获取原型对象：Object.getPrototypeOf(obj) / obj.__proto__】。        
* 构造函数的prototype属性与它创建的实例对象的内部指针指向的是同一个对象，即 对象.__proto__ === 函数.prototype 。

![原型](https://github.com/Selenamona/Selenamona.github.io/blob/master/img/prototype.png?raw=true)


```javascript
function Girl(name, age) {
    this.name = name;
    this.age = age;
}

Girl.prototype.say = function(){
    console.log("我叫" + this.name + ",今年" + this.age + "岁！")
}

//通过这个构造函数创建实例并调用say方法:
const girl1 = new Girl('娟娟',12);
const girl2 = new Girl('莉莉',18);
girl1.say();   // 我叫娟娟,今年12岁！
girl2.say();   // 我叫莉莉,今年18岁！

//重写girl2 say方法:
girl2.say = function(){
    console.log(this.name + "我今年" + this.age + "岁啦！")
}
girl1.say();   // 我叫娟娟,今年12岁！
girl2.say();   // 莉莉我今年18岁啦！ 
```
  
* 每个实例可以重写原型中已经存在的属性来覆盖这个属性，并且不会影响到其他的实例。      
  
* 如果想要统一修改所有实例继承的属性，只需要直接修改原型对象中的属性即可。  

```javascript
Girl.prototype.say = function(){
    console.log(this.name + "的say方法：原型方法重写")
}
girl1.say()  // 娟娟的say方法：原型方法重写
girl2.say()  // 莉莉我今年18岁啦！
```   

### 原型链

JavaScript中所有的对象都是由它的原型对象继承而来。而原型对象自身也是一个对象，它也有自己的原型对象，这样层层上溯，就形成了一个类似链表的结构（顶级null），这就是原型链（prototype chain）。

> * 访问对象的属性时，JavaScript会首先在对象自身的属性内查找，若没有找到，则会跳转到该对象的原型对象中查找。   


### 继承  

通过原型链可以在JavaScript中实现继承。

1. `子类原型直接引用父类：Children.prototype = Parent.prototype`

    ```javascript
    function Parent(name){
    this.name = name;
    }

    Parent.prototype.say = function(){
    console.log("Hello, " + this.name + "!");
    }

    function Children(name){
    this.name = name;
    }

    Children.prototype = Parent.prototype; //子父类原型引用同一个对象

    Children.prototype.say = function(){   //修改子类prototype也会影响父类原型
    console.log("Hello, " + this.name + "! hoo~~");
    }

    var parent  = new Parent("父类");
    var children  = new Children("子类");

    parent.say();           // "Hello, 父类! hoo~~"          
    children.say();         // "Hello, 子类! hoo~~"
    ```

2. `子类原型间接引用父类`

    ```javascript
    function Parent(name){
    this.name = name;
    }

    Parent.prototype.say = function(){
    console.log("Hello, " + this.name + "!");
    }

    function Children(name){
    this.name = name;
    }
    
    //<!-- children 和 parent 的属性相同，可以使用apply
    //function Children(name){
        //Parent.apply(this, arguments);
    //} -->

    //方式1
    function F(){}  // empty  
    F.prototype = Parent.prototype;
    Children.prototype = new F();
    //方式2  ES5
    Children.prototype = Object.create(Parent.prototype);

    var parent  = new Parent("父类");
    var children  = new Children("子类");

    parent.say();           //"Hello, 父类!"                                   
    children.say();       //"Hello, 子类!"
    ```


> 确定原型和实例之间的关系:    
> - 实例 instanceof 原型 //true or false    
> - 原型.prototype.isPrototypeOf(实例) //true or false    





[深入理解 JavaScript 对象和数组拷贝](https://juejin.im/post/5a00226b5188255695390a74)