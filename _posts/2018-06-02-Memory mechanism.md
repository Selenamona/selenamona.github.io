---
layout:     post
title:      JS内存机制
summary: 内存空间 / 内存生命周期（分配|使用|回收） / 内存泄露 / 深浅拷贝 
categories: Js
technique: true
---


### 内存空间

JS内存空间分为**栈(stack)**、**堆(heap)**。两者存储数据的机制不同，导致存储数据的类型也不同，**stack主要存储简单数据类型，heap主要存储引用数据类型**。
 
**stack**：内存空间由操作系统自动分配释放     
**heap**：动态分配的内存，大小不定也不会自动释放　　      

在计算机领域中，堆栈是两种数据结构，它们只能在一端（称为栈顶（top））对数据项进行插入和删除。          

**堆（heap）**：FIFO（First-in/First-out）先进先出原则，top端进，bottom端出    
**栈（stack）**：FILO（First-in/Last-out）先进后出原则，top端进，top端出

#### 基本数据类型   

`基本数据类型： Number String Boolean Null Undefined`   

**基本数据类型**的值存在于**栈内存**，值固定，内存空间大小可以分配。因为按值访问，所以可以直接访问、操作。

```javascript
var a = 20;
var b = a;
b = 30;
```
基本数据类型执行数据复制时，系统会自动为新的变量分配一个新的值，所以a与b其实已经是完全独立的两个变量，只是值一样而已。

![图解](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/baseType.jpg)

#### 引用数据类型   

`引用数据类型：Object Function Array`   

**引用数据类型**的值是保存在**堆内存**中的对象。JS不允许直接访问堆内存中的位置，因此我们不能直接操作对象的堆内存空间。*当访问引用类型的值时，首先从栈中获得该对象的地址指针，然后再从堆内存中取得所需的数据。*

```javascript
var m = { a:10,b:20};
var n = m;
n.a = 15;
console.log(m.a); 
```

引用类型数据执行复制时，虽然也是两个完全独立的变量，但两个变量的地址是一样的，也就是指向堆中同一个对象，所以当改变其中一个，另一个也会随之改变。

![图解](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/o1.jpg)
![图解](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/o2.jpg)

#### 浅拷贝
  **浅拷贝**拷贝对象属性的引用，若某属性依然为对象，拷贝双方修改值会彼此影响。   

  1. **遍历属性**
      ```javascript
      var a = {m:1,n:["A","B"]};
      function copy(obj){
        var newObj = {};
        for(var item in obj){
          newObj[item] = obj[item];
        }
        return newObj;
      }
      var b = copy(a);
      console.log(b)  // {m:1,n:["A","B"]}
      b.m = 2;
      b.n.push("C");
      console.log(a.m,b.m);  // 1   2
      console.log(a.n,b.n);  // ["A", "B", "C"]   ["A", "B", "C"]
      ```

      上面例子中拷贝了对象属性的值，因为m值为基本数据类型，所以b中m属性的改变不会影响a。而n值为引用数据类型，b中只是拷贝了n的地址，所以b.n和a.n依然指向同一个对象，b.n的改变会影响a。


  2. **`Object.assign()`**

      把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。

      ```javascript
      var a = {m:1,n:["A","B"]};
      var b = Object.assign({},a);
      console.log(b)  // {m:1,n:["A","B"]}
      b.m = 2;
      b.n.push("C");
      console.log(a.m,b.m);  // 1   2
      console.log(a.n,b.n);  // ["A", "B", "C"]   ["A", "B", "C"]
      ```
      
  > 数组拷贝：   
  > 1. Array.from()       
      ```javascript
        var a = [1,2,3,[4,5,6],{m:7,n:[8,9]}];
        var b = Array.from(a);
        console.log(b);
      ```
  > 2. ES6 扩展运算符（...）  
      ```javascript
        var a = [1,2,3,[4,5,6],{m:7,n:[8,9]}];
        var b = [...a];
        console.log(b);
      ```
  

#### 深拷贝
  **深拷贝**拷贝对象本身，完全拷贝一个相同的对象，无论属性怎么改变，不会相互影响。

  1. **`JSON.parse(JSON.stringify(obj))`**

      > 弊端：
        1. 抛弃对象的constructor（不管原来构造函数是什么，拷贝之后都会变成Object）；
        2. 只能处理能够被JSON直接表示的数据结构。

      ```javascript
      function A(){
        this.m = 1;
        this.n = ["A","B"];
      }
      var a = new A();
      var b = JSON.parse(JSON.stringify(a));
      console.log(a);
      console.log(b);
      ```

      ![demoJSON](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/JSON copy.jpg)

  2. **递归**

      ```javascript
      var a = {m:1,n:["A","B"]};
      function deepClone(initalObj, finalObj) {
          var obj = finalObj || {};
          for (var i in initalObj) {
              if (typeof initalObj[i] === 'object') {
                  obj[i] = (initalObj[i].constructor === Array) ? [] : {};
                  arguments.callee(initalObj[i], obj[i]);
              } else {
                  obj[i] = initalObj[i];
              }
          }
          return obj;
      }
      var b = deepClone(a,b);
      console.log(b);  // {m:1,n:["A","B"]}     
      b.m = 2;
      b.n.push("C");
      console.log(a);  // {m:1,n:["A","B"]}
      console.log(b);  // {m:2,n:["A","B","C"]}

      // 上述深拷贝方法当遇到两个互相引用的对象，会出现死循环的情况。
      // 解决：在遍历的时候判断是否相互引用对象，如果是则退出循环。
      function deepClone(initalObj, finalObj) {
          var obj = finalObj || {};
          for (var i in initalObj) {
              var prop = initalObj[i];
              // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
              if(prop === obj) {
                  continue;
              }
              if (typeof prop === 'object') {
                  obj[i] = (prop.constructor === Array) ? [] : {};
                  arguments.callee(prop, obj[i]);
              } else {
                  obj[i] = prop;
              }
          }
          return obj;
      };
      ```

  3. **Object.create()**

      创建一个具有指定原型且可选择性地包含指定属性的对象。    
      `Object.create(prototype, descriptors)`


### 内存的生命周期

JS环境中分配的内存一般有如下生命周期：

`内存分配（Allocate memory）`：内存由操作系统分配，允许应用程序使用。当申明变量、函数、对象时，系统会自动为他们分配内存。     
`内存使用（Use memory）`：即读写内存，也就是使用变量、函数等           
`内存回收（Release memory）`：释放明确不需要的内存，让其再次空闲和可用。         


#### 内存分配    
```javascript
var n = 374;            // allocates memory for a number
var s = 'sessionstack'; // allocates memory for a string 
var o = {               // allocates memory for an object and its contained values
  a: 1,
  b: null
};                
var a = [1, null, 'str']; // allocates memory for the array and its contained values
function f(a) {           // allocates a function (which is a callable object)
  return a + 3;
} 
// function expressions also allocate an object
someElement.addEventListener('click', function() {
  someElement.style.backgroundColor = 'blue';
}, false);

//一些函数调用也会导致对象分配
var d = new Date();                     // allocates a Date object
var e = document.createElement('div');  // allocates a DOM element

//方法可以分配新的值或对象
var s1 = 'sessionstack';
var s2 = s1.substr(0, 3);   // s2 is a new string
// Since strings are immutable, JavaScript may decide to not allocate memory, 
//but just store the [0, 3] range.
var a1 = ['str1', 'str2'];
var a2 = ['str3', 'str4'];
var a3 = a1.concat(a2); 
// new array with 4 elements being, the concatenation of a1 and a2 elements
```

#### 内存回收

> JavaScrip在对象被创建时分配内存，并在对象不再使用时“自动”释放内存，这个过程被称为**垃圾回收**。  

JavaScript有自动垃圾收集机制，垃圾收集器会跟踪内存分配和使用情况，以便找到何时何种情况下不再需要这些分配了的内存，它将自动释放内存。在局部作用域中，当函数执行完毕，局部变量也就没有存在的必要了，因此垃圾收集器很容易做出判断并回收。但是全局变量什么时候需要自动释放内存空间则很难判断，因此在我们的开发中，需要尽量避免使用全局变量，以确保性能问题。

由于发现一些内存是否“不再需要”事实上是不可判定的，所以垃圾收集在实施一般问题解决方案时具有局限性。

#### 垃圾收集算法及其局限性

**1. 引用计数垃圾收集**

  这是最简单的垃圾收集算法。如果有零个指向它的引用，则该对象被认为是“可垃圾回收的”。  

  > `内存引用`：在内存管理的上下文中，如果一个对象可以访问另一个对象（可以是隐式的或显式的），则称该对象引用另一个对象。例如, 一个 JavaScript 引用了它的 prototype (隐式引用)和它的属性值(显式引用)。

  ```javascript
  // 创建一个对象person，他有两个指向属性age和name的引用
  var person = {
      age: 12,
      name: 'aaaa'
  };
  // 虽然设置为null，但因为person对象还有指向name的引用，因此name不会回收
  person.name = null; 
  //原来的person对象被赋值为1，但因为有新引用p指向原person对象，因此它不会被回收
  var p = person; 
  person = 1;         
  //原person对象已经没有引用，很快会被回收 
  p = null;           
  ```

  > `周期问题`：若两个对象被创建并相互引用，这就创建了一个循环。在函数调用之后，它们会超出界限，所以它们实际上是无用的，并且可以被释放。然而，引用计数算法认为，由于两个对象中的每一个都被至少引用了一次，所以两者都不能被垃圾收集。

  ```javascript
  //demo1
  function f() {
    var o1 = {};
    var o2 = {};
    o1.p = o2; // o1 references o2
    o2.p = o1; // o2 references o1. This creates a cycle.
  }
  f();
  ```

  ![demo1](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/1.jpg)

**2. 标记和扫描算法**

  为了确定是否需要某个对象，本算法判断该对象是否可访问。

  标记和扫描算法经过这 3 个步骤：

  - 根节点：一般来说，根是代码中引用的全局变量。例如，在 JavaScript 中，可以充当根节点的全局变量是“window”对象。Node.js 中的全局对象被称为“global”。完整的根节点列表由垃圾收集器构建。

  - 然后算法检查所有根节点和他们的子节点并且把他们标记为活跃的（意思是他们不是垃圾）。**任何根节点不能访问的变量将被标记为垃圾**。

  - 最后，垃圾收集器释放所有未被标记为活跃的内存块，并将这些内存返回给操作系统。

  > 解决周期问题：在上面的第一个例子（demo1）中，函数调用返回后，两个对象不再被全局对象中的某个变量引用。因此，垃圾收集器会认为它们不可访问。即使两个对象之间有引用，从根节点它们也不再可达。

  ![demo2](https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/2.jpg)


### 内存泄露

#### 内存泄露

对于持续运行的服务进程（daemon），必须及时释放不再用到的内存。否则，内存占用越来越高，轻则影响系统性能，重则导致进程崩溃。不再用到的内存，没有及时释放，就叫做内存泄漏（memory leak）。
有些语言（比如 C 语言）必须手动释放内存，程序员负责内存管理。
```C  
char * buffer;
buffer = (char*) malloc(42);

// Do something with buffer

free(buffer);
```
malloc方法用来申请内存，使用完毕之后，必须自己用free方法释放内存。
这很麻烦，所以大多数语言提供自动内存管理，减轻程序员的负担，这被称为"垃圾回收机制"（garbage collector）。

**js常见内存泄露**

1. 意外的全局变量
2. 遗忘的计时器和回调函数
3. 闭包
4. 脱离DOM的引用

> 详细参考：[JavaScript 工作原理：内存管理 + 处理常见的4种内存泄漏 / LeviDing](https://juejin.im/post/5a2559ae6fb9a044fe4634ba) 

#### WeakSet / WeakMap

解决内存泄露：ES6推出了两种新的数据结构：**WeakSet** 和 **WeakMap**。它们对于值的引用都是不计入垃圾回收机制的。

> 详细参考：[JavaScript 内存泄漏教程 / 阮一峰](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)


### MORE

***************

[4 Types of Memory Leaks in JavaScript and How to Get Rid Of Them](https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/)








