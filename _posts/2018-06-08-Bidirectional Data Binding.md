---
layout:     post
title:      Vue 响应式原理
summary: vue 采用数据劫持结合发布者-订阅者模式，通过Object.defineProperty劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。 
categories: Js
technique: true
---

### 实现双向绑定的几种做法

1. **发布者-订阅者模式（backbone.js）**     
    一般通过sub, pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是 vm.set('property', value)。

2. **脏值检查（angular.js**  
    angular.js 是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过 setInterval() 定时轮询检测数据变动，当然Google不会这么low，angular只有在指定的事件触发时进入脏值检测。

3. **数据劫持（vue.js）** 
    vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。 


### 追踪变化

当把一个普通的 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 [Object.defineProperty](https://selenamona.github.io/2018/06/08/Object.defineProperty/) 把这些属性全部转为 getter/setter。

这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。

<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/bidirection-d-b-data.png" width="600"/>

> 注意：
  1. Vue初始化实例时对属性执行getter/setter转化过程，so属性须在data中才能是响应的。
  2. 不允许在已经创建的实例上动态添加新的根级响应式属性（data中没有，动态添加）。


### 根据Vue2.3源码分析   

vue数据响应式变化主要涉及 Observer, Watcher , Dep 这三个主要的类；因此要弄清Vue响应式变化需要明白这个三个类之间是如何运作联系的；以及它们的原理，负责的逻辑操作。   

#### Observer  

Observer类是将每个目标对象（即data）的键值转换成getter/setter形式，用于进行依赖收集以及调度更新。

  1. 首先将Observer实例绑定到data的ob属性上面去，防止重复绑定；
  2. 若data为数组，先实现对应的变异方法（这里变异方法是指Vue重写了数组的7种原生方法），再将数组的每个成员进行observe，使之成响应式数据；
  3. 否则执行walk()方法，遍历data所有的数据，进行getter/setter绑定，这里的核心方法就是 defineReative(obj, keys[i], obj[keys[i]])

**其中getter方法：**

先为每个data声明一个 Dep 实例对象，被用于getter时执行dep.depend()进行收集相关的依赖;      
根据Dep.target来判断是否收集依赖，还是普通取值。    

**在setter方法中:**

获取新的值并且进行observe，保证数据响应式；   
通过dep对象通知所有观察者去更新数据，从而达到响应式效果。   

在Observer类中，我们可以看到在getter时，dep会收集相关依赖，即收集依赖的watcher，然后在setter操作时候通过dep去通知watcher,此时watcher就执行变化。        

#### Watcher
Watcher是一个观察者对象。依赖收集以后Watcher对象会被保存在Dep的subs中，数据变动的时候Dep会通知Watcher实例，然后由Watcher实例回调cb进行视图的更新。   
  
#### Dep   

被Observer的data在触发 getter 时，Dep 就会收集依赖的 Watcher ，其实 Dep 就像刚才说的是一个书店，可以接受多个订阅者的订阅，当有新书时即在data变动时，就会通过 Dep 给 Watcher 发通知进行更新。    

#### Summary  

Vue 初始化渲染时，视图上绑定的数据就会实例化一个 Watcher，依赖收集就是是通过属性的 getter 函数完成的，Observer 、Watcher 、Dep 都与依赖收集相关。其中 Observer 与 Dep 是一对一的关系， Dep 与 Watcher 是多对多的关系，Dep 则是 Observer 和 Watcher 之间的纽带。依赖收集完成后，当属性变化会执行被 Observer 对象的 dep.notify() 方法，这个方法会遍历订阅者（Watcher）列表向其发送消息， Watcher 会执行 run 方法去更新视图。   

> **`详细参考`**：[深入理解Vue响应式原理（源码） / 前端日刊 ](https://funteas.com/topic/5a809f5847dc830a0e4690c2) 

### 1.x版本实现

[前端攻城狮该了解的 Vue.2x 响应式原理 / 1.x版本的实现](https://blog.csdn.net/GitChat/article/details/78516752)   
[Vue 源码解析：深入响应式原理 / DDFE](https://github.com/DDFE/DDFE-blog/issues/7)      

### Vue-Github

[VUE-github](https://github.com/vuejs/vue)
<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/bidirection-d-b-vue-original.jpg" width="600"/>

*****************************************

### 数组对象属性改变 

由于 JavaScript 的限制， Vue 不能检测以下变动的数组：
1. 利用索引直接设置一个项时，例如： vm.items[indexOfItem] = newValue
2. 修改数组的长度时，例如： vm.items.length = newLength

为了避免第一种情况，以下两种方式将达到像 vm.items[indexOfItem] = newValue 的效果， 同时也将触发状态更新：

```JS
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
// Array.prototype.splice`  
example1.items.splice(indexOfItem, 1, newValue)

// 避免第二种情况，使用 splice：
example1.items.splice(newLength)
```

Vue 不允许在已经创建的实例上动态添加新的根级响应式属性(root-level reactive property)。然而它可以使用Vue.set(object, key, value) 方法将响应属性添加到嵌套的对象上：
`Vue.set(vm.someObject, 'b', 2)`
您还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名：
`this.$set(this.someObject,'b',2)`

有时你想向已有对象上添加一些属性，例如使用 Object.assign() 或 _.extend() 方法来添加属性。但是，添加到对象上的新属性不会触发更新。在这种情况下可以创建一个新的对象，让它包含原对象的属性和新的属性：

```JS
代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
this.someObject = _.extend({ a: 1, b: 2 }, this.someObject)

三种解决方案：
方案一：利用Vue.set(object,key,val)
Vue.set(vm.obj,'k1','v1')

方案二：利用this.$set(this.obj,key,val)
this.$set(this.obj,'k1','v1')

方案三：利用Object.assign({}，this.obj)创建新对象
  this.obj.k1='v1'；
  this.obj = Object.assign({}, this.obj)
或
  this.obj = Object.assign({}, this.obj,{'k1','v1'})
```



**相关参考：**    
    
[Vue.js双向绑定的实现原理 / kidney](http://www.cnblogs.com/kidney/p/6052935.html#3624624)    
[剖析Vue原理&实现双向绑定MVVM](https://segmentfault.com/a/1190000006599500)    



