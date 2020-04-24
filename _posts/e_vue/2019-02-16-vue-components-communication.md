---
layout:     post
title:      vue 组件通讯
summary:
categories: Vue
technique: true
---

## | props & $emit

- prop 只可以从上一级组件传递到下一级组件（父子组件），即所谓的单向数据流。
- prop 只读，不可被修改，所有修改都会失效并警告。

父值子：     
- （1.0/2.0）`:info="info"`；子组件用props接收：`props:["info"]`
- （1.0）父组件`this.$broadcast('属性', '值')`；子组件events中接收。  

子值父：   
- （1.0）子组件`this.$dispatch('属性', '值')`；父组件events中接收。   
- （2.0）子组件`this.$emit('属性', '值')`；父组件`@属性="父组件方法名"`，方法接收参数val，也就是子组件传递的值。

## | $parent & $children

- 父取子[数组]： this.$children[0].msgA  
- 子取父[对象]： this.$parent.msgB

## | provide & inject

provide/inject 是 vue2.2.0 新增的 api

- 父组件中通过 provide 来提供变量 `provide: { testaaa: "hahaha" }`
- 子组件中通过 inject 来注入变量 `inject: ["testaaa"]`

无论子组件嵌套有多深, 只要调用了inject 就可以注入 provide 中的数据；

## | sync & update 

通过sync修饰符，来实现子组件更新父组件状态，是目前写法上最方便的语法糖了。

1、父组件声明状态active 

`<compo :foo.sync="active"></compo>`

这种语法糖其实会被解析成

`<compo :foo="active" @update:foo="val => active = val"></compo>`

2、子组件修改父组件active的值:

`this.$emit('update:foo', newValue)`
 

## | ref 

## | eventBus


1. **基础版**
    - $emit时，必须已经$on，否则将无法监听到事件，也就是说对组件是有一定的同时存在的要求的。(注：路由切换时，新路由组件先created，旧路由组件再destoryed，部分情况可以分别写入这两个生命周期)。    
    - $on在组件销毁后不会自动解除绑定，若同一组件多次生成则会多次绑定事件，则会一次$emit，多次响应，需额外处理。    
    - 数据非“长效”数据，无法保存，只在$emit后生效。   

    ```javascript
    // bus
    const bus = new Vue(); 
    // 数据接收组件
    bus.$on('event1', (val)=>{});
    // 数据发出组件
    bus.$emit('event1', val);
    ```

2. **升级版**

    ```javascript
    // bus
    const bus = new Vue({
        data () {
        return {
            val1: ''
        }
        },
        created () {
            this.$on('updateData1', (val)=>{
                this.val1 = val
            })
        }
    })
    // 数据发出组件
    bus.$emit('updateData1', val)
    // 数据接收组件 == 使用computed接收数据
    computed () {
        val1 () {
        // 依赖并返回bus中的val1
        return bus.val1
        }
    }
    ```


## | vuex

## | localStorage && sessionStorage

## | $attrs & $listeners

## | 监听组件声明周期

通常我们监听组件生命周期会使用 $emit ，父组件接收事件来进行通知

```javascript
// 子组件
export default {
    mounted() {
        this.$emit('listenMounted')
    }
}
// 父组件
<template>
    <div>
        <List @listenMounted="listenMounted" />
    </div>
</template> 
```

其实还有一种简洁的方法，使用 @hook 即可监听组件生命周期，组件内无需做任何改变。同样的， created 、 updated 等也可以使用此方法。

```javascript
<template>
    <List @hook:mounted="listenMounted" />
</template>
``` 

[原文链接](https://juejin.im/post/5d267dcdf265da1b957081a3)