---
layout:     post
title:      常见面试题整理 
# summary: 为博客添加 Gitalk 评论插件
categories: 面试
---

### 原型

【知识点】变量提升、this指向、运算符优先级、原型、继承、全局变量污染、对象属性及原型属性优先级

```javascript
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

//请写出以下输出结果：
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

> 解析：   
1、定义了一个Foo函数；    
2、为Foo创建了一个叫getName的静态属性，并存储了一个匿名函数；   
3、为Foo的原型对象新创建了一个叫getName的匿名函数；   
4、通过函数变量表达式创建了一个getName的函数；   
5、声明一个叫getName函数。   

**第一问**：Foo.getName === 访问Foo函数上存储的静态属性 === 2 。     

**第二问**：直接调用 getName 函数。既然是直接调用那么就是访问当前上文作用域内的叫getName的函数，所以跟1 2 3都没关系。此题有无数面试者回答为5。此处有两个坑，一是变量声明提升，二是函数表达式。

**【变量声明提升】 ：所有声明变量或声明函数都会被提升到当前函数的顶部。** 

**【函数表达式】 ：var getName 与 function getName 都是声明语句，区别在于 var getName 是函数表达式，而 function getName 是函数声明。**    

因此，原题中代码最终执行时的是：

```javascript
function Foo() {
    getName = function () { alert (1); };
    return this;
}
var getName;//只提升变量声明function getName() { alert (5);}//提升函数声明，覆盖var的声明
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
getName = function () { alert (4);};//最终的赋值再次覆盖function getName声明
getName();//最终输出4
```

**第三问**：Foo().getName(); 先执行了Foo函数，然后调用Foo函数的返回值对象的getName属性函数。    

Foo函数的第一句getName = function () { alert (1); };  是一句函数赋值语句，注意它没有var声明，所以先向当前Foo函数作用域内寻找getName变量，没有。再向当前函数作用域上层，即外层作用域内寻找是否含有getName变量，找到了，也就是第二问中的alert(4)函数，将此变量的值赋值为 function(){alert(1)}。此处实际上是将外层作用域内的getName函数修改了。    

注意：此处若依然没有找到会一直向上查找到window对象，若window对象中也没有getName属性，就在window对象中创建一个getName变量。
之后Foo函数的返回值是this，this的指向是由所在函数的调用方式决定的。而此处的直接调用方式，this指向window对象。
遂Foo函数返回的是window对象，相当于执行 window.getName() ，而window中的getName已经被修改为alert(1)，所以最终会输出1
此处考察了两个知识点，一个是变量作用域问题，一个是this指向问题。    

**第四问**：直接调用getName函数，相当于window.getName() ，因为这个变量已经被Foo函数执行时修改了，遂结果与第三问相同，为1。  
  
**第五问**：new Foo.getName();此处考察的是js的运算符优先级问题。点（.）的优先级高于new操作，遂相当于是:new (Foo.getName)();所以实际上将getName函数作为了构造函数来执行，遂弹出2。    
 
**第六问**：new Foo().getName()，首先看运算符优先级括号高于new，实际执行为(new Foo()).getName()；遂先执行Foo函数，而Foo此时作为构造函数却有返回值，所以这里需要说明下js中的构造函数返回值问题。   
 
【构造函数的返回值】：   
在传统语言中，构造函数不应该有返回值，实际执行的返回值就是此构造函数的实例化对象。而在js中构造函数可以有返回值也可以没有。
    
1、没有返回值则按照其他语言一样返回实例化对象。   
    ==> function F（）{ }   
    ==> new F()    
    ==> F {}    
   
2、若有返回值则检查其返回值是否为引用类型。如果是非引用类型，如基本类型（string,number,boolean,null,undefined）则与无返回值相同，实际返回其实例化对象。    
    ==> function F（）{ return true; }
    ==> new F()
    ==> F {}

3、若返回值是引用类型，则实际返回值为这个引用类型。  
    ==> function F（）{ return {a:1}; }  
    ==> new F()   
    ==> Object {a:1}   

原题中，返回的是this，而this在构造函数中本来就代表当前实例化对象，遂最终Foo函数返回实例化对象。之后调用实例化对象的getName函数，因为在Foo构造函数中没有为实例化对象添加任何属性，遂到当前对象的原型对象（prototype）中寻找getName，找到了。遂最终输出3。
   
**第七问**：new new Foo().getName(); 同样是运算符优先级问题。最终实际执行为：new ((new Foo()).getName)();先初始化Foo的实例化对象，然后将其原型上的getName函数作为构造函数再次new。遂最终结果为3  

正确答案：2 4 1 1 2 3 3  

### 数组

#### 数组去重 

**方法一** ：定义一个变量数组 res 保存结果，遍历需要去重的数组，如果该元素已经存在在 res 中了，则说明是重复的元素，如果没有，则放入 res 中。（复杂度 O(n^2)）

```javascript
function unique(a) {
  var res = [];
  for (var i = 0, len = a.length; i < len; i++) {
    var item = a[i];
    for (var j = 0, jLen = res.length; j < jLen; j++) {
      if (res[j] === item)
        break;
    }
    if (j === jLen)
      res.push(item);
  }
  return res;
}

// 如不考虑浏览器兼容，可用 ES5 提供的 Array.prototype.indexOf 方法来简化代码：

function unique(a) {
  var res = [];
  for (var i = 0; i < a.length; i++) {
    var item = a[i];
    (res.indexOf(item) === -1) && res.push(item);
  }
  return res;
}

// 既然用了 indexOf，那么不妨再加上 filter：

function unique(a) {
  var res = a.filter(function(item, index, array) {
    return array.indexOf(item) === index;
  });
  return res;
}
```

**方法二**：法一是将原数组中的元素和结果数组中的元素一一比较，可以换个思路，将原数组中重复元素的最后一个元素放入结果数组中。（复杂度 O(n^2)）

```javascript
function unique(a) {
  var res = [];
  for (var i = 0, len = a.length; i < len; i++) {
    for (var j = i + 1; j < len; j++) {
      // 这一步十分巧妙，如果发现相同元素，则 i 自增进入下一个循环比较
      if (a[i] === a[j])
        j = ++i;
    }
    res.push(a[i]);
  }
  return res;
}
```

**方法三**：将数组用 sort 排序后，理论上相同的元素会被放在相邻的位置，那么比较前后位置的元素就可以了。（sort)

```javascript
function unique(a) {
  return a.concat().sort().filter(function(item, pos, ary) {
    return !pos || item != ary[pos - 1];
  });
}
```

**方法四** ：用 JavaScript 中的 Object 对象来当做哈希表，这也是几年前笔试时的解法，跟 sort 一样，可以去重完全由 Number 基本类型组成的数组。（object）

```javascript
function unique(a) {
  var seen = {};
  return a.filter(function(item) {
    return seen.hasOwnProperty(item) ? false : (seen[item] = true);
  });
}

// 还是和方法三一样的问题，因为 Object 的 key 值都是 String 类型，所以对于 1 和 "1" 无法分别，可以稍微改进下，将类型也存入 key 中。

function unique(a) {
  var ret = [];
  var hash = {};
  for (var i = 0, len = a.length; i < len; i++) {
    var item = a[i];
    var key = typeof(item) + item;
    if (hash[key] !== 1) {
      ret.push(item);
      hash[key] = 1;
    }
  }
  return ret;
}

// 虽然解决了讨厌的 1 和 "1" 的问题，但是还有别的问题！

var a = [{name: "hanzichi"}, {age: 30}, new String(1), new Number(1)];
var ans = unique(a);
console.log(ans); // => [Object, String]

// 但是如果数组元素全部是基础类型的 Number 值，键值对法应该是最高效的！
```

**方法五**：（ES6） ES6 部署了 Set 以及 Array.from 方法，太强大了！如果浏览器支持，完全可以这样：

```javascript
function unique(a) {
  return Array.from(new Set(a));
}
```

### 闭包


```javascript
function fun(n,o) {
    console.log(o)
    return {
        fun:function(m){
            return fun(m,n);
        }
    };
}
var a = fun(0); a.fun(1); a.fun(2); a.fun(3);//undefined,?,?,?
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
var c = fun(0).fun(1); c.fun(2); c.fun(3);//undefined,?,?,?
//问:三行a,b,c的输出分别是什么？

// 答案：
a: undefined,0,0,0
b: undefined,0,1,2
c: undefined,0,1,1

```



[一不小心就做错的JS闭包面试题](https://www.jb51.net/article/75450.htm)


[你有必要知道的25个JavaScript面试题](https://www.jb51.net/article/77140.htm)















