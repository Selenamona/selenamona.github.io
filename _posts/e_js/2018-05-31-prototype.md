---
layout: post
title: 原型与继承
summary:
categories: Js
technique: true
---

面向对象（ Object-Oriented OO ）

### 创建对象

Javascript 中创建对象方式：

1. Object

```javascript
var girl = new Object();
girl.name = '小美';
girl.age = '18';
```

2. 字面量

```javascript
var girl = {
  name: '小美',
  age: '18',
};
```

### 工厂模式

若创建一个对象，上面两种方法很简单，但若创建多个具有相同属性的对象，就要把相同的代码重复 n 次。对于强迫症（懒）的童鞋恐怕就要烦躁了。因此想到了封装，也就是常说的工厂模式。

```javascript
function girl(name, age) {
  var obj = new Object();
  obj.name = name;
  obj.age = age;
  return obj;
}
var girl1 = girl('easy1', 20);
var girl2 = girl('easy2', 20);
```

### 构造函数

构造函数与普通函数区别在于调用方法。对于任意函数，使用 new 操作符调用，那么它就是构造函数。

```javascript
function Girl(name, age) {
  this.name = name;
  this.age = age;
  this.say = function () {
    console.log('我叫' + this.name + ',今年' + this.age + '岁！');
  };
}

const girl1 = new Girl('娟娟', 12);
const girl2 = new Girl('莉莉', 18);
```

通过构造函数创建实例时，每创建一个实例，都要重新创建一个 say 方法，无疑造成了很大的浪费。

### 原型

- 原型对象用来存放实例中共有的属性和方法。

- 构造函数拥有一个 prototype 属性，指向原型对象。原型对象拥有一个 constructor 属性，指向构造函数（构造函数/原型对象==互指关系）。

- 每个实例对象都包含一个指向原型对象的内部指针。【通过实例对象获取原型对象：Object.getPrototypeOf(obj) / obj.**proto**】。
- 构造函数的 prototype 属性与它创建的实例对象的内部指针指向的是同一个对象，即 对象.**proto** === 函数.prototype 。

![原型](https://github.com/Selenamona/Selenamona.github.io/blob/master/assets/images/prototype.png?raw=true)

```javascript
function Girl(name, age) {
  this.name = name;
  this.age = age;
}

Girl.prototype.say = function () {
  console.log('我叫' + this.name + ',今年' + this.age + '岁！');
};

//通过这个构造函数创建实例并调用say方法:
const girl1 = new Girl('娟娟', 12);
const girl2 = new Girl('莉莉', 18);
girl1.say(); // 我叫娟娟,今年12岁！
girl2.say(); // 我叫莉莉,今年18岁！

//重写girl2 say方法:
girl2.say = function () {
  console.log(this.name + '我今年' + this.age + '岁啦！');
};
girl1.say(); // 我叫娟娟,今年12岁！
girl2.say(); // 莉莉我今年18岁啦！
```

- 每个实例可以重写原型中已经存在的属性来覆盖这个属性，并且不会影响到其他的实例。

- 如果想要统一修改所有实例继承的属性，只需要直接修改原型对象中的属性即可。

```javascript
Girl.prototype.say = function () {
  console.log(this.name + '的say方法：原型方法重写');
};
girl1.say(); // 娟娟的say方法：原型方法重写
girl2.say(); // 莉莉我今年18岁啦！
```

### 原型链

JavaScript 中所有的对象都是由它的原型对象继承而来。而原型对象自身也是一个对象，它也有自己的原型对象，这样层层上溯，就形成了一个类似链表的结构（顶级 null），这就是原型链（prototype chain）。

> - 访问对象的属性时，JavaScript 会首先在对象自身的属性内查找，若没有找到，则会跳转到该对象的原型对象中查找。

### 继承

通过原型链可以在 JavaScript 中实现继承。

1. `子类原型直接引用父类：Children.prototype = Parent.prototype`

   ```javascript
   function Parent(name) {
     this.name = name;
   }

   Parent.prototype.say = function () {
     console.log('Hello, ' + this.name + '!');
   };

   function Children(name) {
     this.name = name;
   }

   Children.prototype = Parent.prototype; //子父类原型引用同一个对象

   Children.prototype.say = function () {
     //修改子类prototype也会影响父类原型
     console.log('Hello, ' + this.name + '! hoo~~');
   };

   var parent = new Parent('父类');
   var children = new Children('子类');

   parent.say(); // "Hello, 父类! hoo~~"
   children.say(); // "Hello, 子类! hoo~~"
   ```

2. `子类原型间接引用父类`

   ```javascript
   function Parent(name) {
     this.name = name;
   }

   Parent.prototype.say = function () {
     console.log('Hello, ' + this.name + '!');
   };

   function Children(name) {
     this.name = name;
   }

   //<!-- children 和 parent 的属性相同，可以使用apply
   //function Children(name){
   //Parent.apply(this, arguments);
   //} -->

   //方式1
   function F() {} // empty
   F.prototype = Parent.prototype;
   Children.prototype = new F();
   //方式2  ES5
   Children.prototype = Object.create(Parent.prototype);

   var parent = new Parent('父类');
   var children = new Children('子类');

   parent.say(); //"Hello, 父类!"
   children.say(); //"Hello, 子类!"
   ```

> 确定原型和实例之间的关系:
>
> - 实例 instanceof 原型 //true or false
> - 原型.prototype.isPrototypeOf(实例) //true or false

#### 理解 call/apply/bind

**_call_**

- 多个参数：第一个参数为 this 指向；
- 立刻调用

**_apply_**

- 两个参数：1、this 指向； 2、参数数组
- 立刻调用

**_bind_**

- 参数同 call
- 不会立刻调用。bind() 会生成一个新的函数，需要时再调用。

```javascript
var xw = {
  name: '小王',
  gender: '男',
  age: 24,
  say: function (school, grade) {
    alert(`${this.name},${this.gender},今年${this.age}，在${school}上${grade}`);
  },
};
var xh = {
  name: '小红',
  gender: '女',
  age: 18,
};
xw.say.call(xh, '实验小学', '六年级');
xw.say.apply(xh, ['实验小学', '六年级']);
xw.say.bind(xh)('实验小学', '六年级')();
```

> **_总结：_**
>
> 1. 共同点：call()、apply()和 bind()都是用来改变函数执行时的上下文，可借助它们实现继承；如果第一个参数不传，或者第一个传递的是 null/undefined，在 非严格模式下，this 指向 window；在 严格模式下，this 为 undefined
> 2. call()和 apply()唯一区别是参数不一样，call()是 apply()的语法糖；
> 3. bind()是返回一个新函数，供以后调用，而 apply()和 call()是立即调用。

> **_如何选用：_**
>
> 1. 如果不需要关心具体有多少参数被传入函数，选用 apply()；
> 2. 如果确定函数可接收多少个参数，并且想一目了然表达形参和实参的对应关系，用 call()；
> 3. 如果我们想要将来再调用方法，不需立即得到函数返回结果，则使用 bind();

```javascript
// 重写 call
((proto) => {
  function myCall(thisArg, ...args) {
    thisArg = thisArg == undefined ? window : thisArg;
    let type = typeof thisArg;
    if (!/^(object|function)$/.test(type)) {
      if (/^(symbol|bigint)$/) {
        thisArg = Object(thisArg);
      } else {
        thisArg = new thisArg.constructor(thisArg);
      }
    }
    let key = Symbol('key');
    thisArg[key] = this;
    let result = thisArg[key](...args);
    delete thisArg[key];
    return result;
  }
  proto.myCall = myCall;
})(Function.prototype);

// 重写 apply
(proto => {
    function myApply(thisArg, args) {
        thisArg = thisArg == undefined ? window : thisArg;
        let type = typeof thisArg;
        if (!/^(object|function)$/.test(type)) {
            if (/^(symbol|bigint)$/) {
                thisArg = Object(thisArg);
            } else {
                thisArg = new thisArg.constructor(thisArg);
            }
        }
        let key = Symbol('key');
        thisArg[key] = this;
        let result = thisArg[key](...args);
        delete thisArg[key];
        return result;
    }
    proto.myApply = myApply;
})(Function.prototype)

// 重写 bind
(proto) => {
  function myBind(thisArg, ...args) {
    let _this = this;
    thisArg = thisArg == undefined ? window : thisArg;
    let type = typeof thisArg;
    if (!/^(object|function)$/.test(type)) {
      if (/^(symbol|bigint)$/) {
        thisArg = Object(thisArg);
      } else {
        thisArg = new thisArg.constructor(thisArg);
      }
    }
    return function an(...innerArgs) {
      _this.call(thisArg, ...args.concat(innerArgs));
    };
  }
  proto.myBind = myBind;
})(Function.prototype);

// 重写 new
function Dog(name) {
    this.name = name;
}
Dog.prototype.bark = function () {
    console.log('wangwang');
}
Dog.prototype.sayName = function () {
    console.log('my name is ' + this.name);
}
//==========重写开始
function _new(Func, ...args) {
    let obj = Object.create(Func.prototype);
    let result = Func.call(obj, ...args);
    if (result !== null && /^(object|function)$/.test(typeof result)) return result;
    return obj;
}
//==========重写结束
let sanmao = _new(Dog, '三毛');
sanmao.bark(); //=>"wangwang"
sanmao.sayName(); //=>"my name is 三毛"
console.log(sanmao instanceof Dog); //=>true
```

[深入理解 JavaScript 对象和数组拷贝](https://juejin.im/post/5a00226b5188255695390a74)
