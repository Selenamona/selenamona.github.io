---
layout:     post
title:      Vue 源码学习
summary: 根据源码 Vue.js v2.0.2，对常见数据项props、data、computed、methods、watch的工作原理进行解析 
categories: Vue
technique: true
---

### 项目结构

👉[Github-Vue](https://github.com/vuejs/vue)
<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/vue-original.jpg" width="600" title="源码结构"/>


1. 看到源码一个想到的是：`package.json`  
    ```javascript 
    "dev": "rollup -w -c scripts/config.js --environment TARGET:web-full-dev",
    ```   

2. 找到`scripts/config.js`，当运行 npm run dev 的时 process.env.TARGET 的值等于 `web-full-dev`，由此找到entry为`entry-runtime-with-compiler.js`
  
3. 既然使用new调用vue，vue应该是一个构造函数，那么就从entry开始寻找vue构造函数      
    - `entry-runtime-with-compiler.js` 
    - `./runtime/index` 
    - `core/index` 
    - `./instance/index`


### observer

#### index.js 

Observer => 订阅者；遍历对象所有属性，使用 Object.defineProperty 把指定属性转为 getter/setter，进行双向绑定；   
Observe => 在其内部实例化Observer类，并返回Observer的实例，以响应式数据__ob__属性的方式作为标记，存放该属性观察器)    
defineReactive =>   
    - Observer中调用walk方法，walk中调用defineReactive；   
    - defineReactive中每个属性新建dep实例，管理依赖；  
    - 递归去将data转化为getter/setter；   

#### array.js

获取原生数组原型，以此为原型用Object.create()创建新的数组对象，用Object.defineProperty重写该对象上数组的七个方法（push,pop,shift,unshift,reverse,splice,sort）（防污染原生数组方法），截获数组的成员发生的变化，执行原生数组操作的同时dep通知关联的所有观察者进行响应式处理。   

#### dep.js

Vue整个生命周期中，任何响应式数据都会绑定一个dep实例来管理其依赖。                                   
dep => 观察者和订阅者的桥梁，管理和保存观察者的地方；   

- dep 对象中包含：                               
    属性 - id, subs(收集相关的subs(订阅者)(即这个观察者的依赖))                     
    方法 - addSub, removeSub, depend  

#### Watcher.js

Watcher是一个观察者对象。依赖收集以后Watcher对象会被保存在Deps中，数据变动的时候会由于Deps通知Watcher实例，然后由Watcher实例回调cb进行实图的更新。  
  

> **tips:**       
    - `Object.getOwnPropertyNames()`接收一个对象作为参数，返回这个对象所有自身属性的属性名（包括不可枚举属性）组成的数组。若要-获取对象自身所有可枚举的属性，可使用`Object.keys()`;两者皆不可获取原型上的属性。   
    - `Object.hasOwnProperty()`用来判断一个属性是定义在对象本身而不是继承自原型链。   
    - `Object.isExtensible()`返回一个值，该值指示是否可向对象添加新属性。   

> 更多查看：**[Object 对象 >>](https://msdn.microsoft.com/zh-cn/library/dn342818(v=vs.94).aspx)**


******************************

相关参考：

[Vue2.1.7源码学习 / 最骚的就是你](https://www.cnblogs.com/libin-1/p/6845669.html)     
[LearnVue-in-Github（全） / answershuto](https://github.com/answershuto/learnVue)






  
  




