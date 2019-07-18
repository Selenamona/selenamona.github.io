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

## | $parent & $children

- 父取子[数组]： this.$children[0].msgA  
- 子取父[对象]： this.$parent.msgB

## | provide & inject

provide/inject 是 vue2.2.0 新增的 api

- 父组件中通过 provide 来提供变量 `provide: { testaaa: "hahaha" }`
- 子组件中通过 inject 来注入变量 `inject: ["testaaa"]`

无论子组件嵌套有多深, 只要调用了inject 就可以注入 provide 中的数据；

## | ref 

## | eventBus

## | vuex

## | localStorage && sessionStorage

## | $attrs & $listeners


[原文链接](https://juejin.im/post/5d267dcdf265da1b957081a3)