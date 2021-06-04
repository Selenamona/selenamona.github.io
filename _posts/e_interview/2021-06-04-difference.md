<!--
 * @Descripttion:
 * @Author: Mona
 * @Date: 2021-06-04 15:14:46
 * @LastEditTime: 2021-06-04 15:42:02
-->
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

## | action 和 mutation 区别

1. 流程顺序：视图触发Action，Action 再触发 Mutation。
2. 角色定位： Mutation 专注于修改 State，理论上是修改 State 的唯一途径；Action 执行业务代码、异步请求。
3. 限制：Mutation 必须同步执行；Action 可以异步，但不能直接操作State。

## | v-if 和 v-show 有什么区别？

v-show 仅仅控制元素的显示方式，将 display 属性在 block 和 none 来回切换；而v-if会控制这个 DOM 节点的存在与否。当我们需要经常切换某个元素的显示/隐藏时，使用v-show会更加节省性能上的开销；当只需要一次显示或隐藏时，使用v-if更加合理。


## | computed 和 watch 区别

***侦听属性 watch***

1. 不支持缓存，数据变，直接会触发相应的操作；
2. watch 支持异步；
3. 监听的函数接收两个参数，第一个参数是最新的值；第二个参数是输入之前的值；
4. 当一个属性发生变化时，需要执行对应的操作；一对多；
5. 监听数据必须是 data中 声明过或者父组件传递过来的 props 中的数据，当数据变化时，触发其他操作，函数有两个参数 immediate：组件加载立即触发回调函数执行；deep: 深度监听，为了发现对象内部值的变化，复杂类型的数据时使用，例如数组中的对象内容的改变，注意监听数组的变动不需要这么做。注意：deep无法监听到数组的变动和对象的新增，参考vue数组变异,只有以响应式的方式触发才会被监听到。
6. 监听的对象也可以写成字符串的形式，当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。这是和computed最大的区别

***计算属性 computed***

1. 支持缓存，只有依赖数据发生改变，才会重新进行计算
2. 不支持异步，当 computed 内有异步操作时无效，无法监听数据的变化
3. computed 属性值基于data中声明过或者父组件传递的props中的数据通过计算得到的值
4. 如果一个属性是由其他属性计算而来的，这个属性依赖其他属性，是一个多对一或者一对一，一般用computed
5. 如果computed属性属性值是函数，那么默认会走get方法；函数的返回值就是属性的属性值；在computed中的，属性都有一个get和一个set方法，当数据变化时，调用set方法。

> 更多：

事实上 computed 会拥有自己的 watcher，它内部有个属性 dirty 开关来决定 computed 的值是需要重新计算还是直接复用之前的值。

以这样的一个例子来说：

```javascript
computed: {
  sum() {
    return this.count + 1
  }
}
```
首先明确两个关键字：
「dirty」 从字面意义来讲就是 脏 的意思，这个开关开启了，就意味着这个数据是脏数据，需要重新求值了拿到最新值。
「求值」 的意思的对用户传入的函数进行执行，也就是执行 return this.count + 1

在 sum 第一次进行求值的时候会读取响应式属性 count，收集到这个响应式数据作为依赖。并且计算出一个值来保存在自身的 value 上，把 dirty 设为 false，接下来在模板里再访问 sum 就直接返回这个求好的值 value，并不进行重新的求值。
而 count 发生变化了以后会通知 sum 所对应的 watcher 把自身的 dirty 属性设置成 true，这也就相当于把重新求值的开关打开来了。这个很好理解，只有 count 变化了， sum 才需要重新去求值。
那么下次模板中再访问到 this.sum 的时候，才会真正的去重新调用 sum 函数求值，并且再次把 dirty 设置为 false，等待下次的开启……
