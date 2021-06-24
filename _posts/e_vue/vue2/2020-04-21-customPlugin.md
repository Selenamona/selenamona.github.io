---
layout: post
title: 手动挂载组件(封装)
summary:  
categories: Vue
technique: true
---

四种挂载方式：

```javascript
var MyComponent = Vue.extend({
  template: '<div>Hello!</div>'
})

// 创建并挂载到 #app (会替换 #app)
new MyComponent().$mount('#app')

// 同上
new MyComponent({ el: '#app' })

// 或者，在文档之外渲染并且随后挂载
var component = new MyComponent().$mount()
document.getElementById('app').appendChild(component.$el)
```

实例：

```javascript
// index.js
import Vue from 'vue'
import Index from './index.vue' // 自定义组件

let messageInstance = null
let MessageConstructor = Vue.extend(Index)

let init = () => {
    messageInstance = new MessageConstructor()
    messageInstance.$mount()
    document.body.appendChild(messageInstance.$el)
}

let caller = (options) => {
    if (!messageInstance) {
        init(options)
    }
    messageInstance.add(options)
}

export default {
    // 返回 install 函数 用于 Vue.use 注册
    install(vue) {
        vue.prototype.$message = caller
    }
}


// main.js 
import Message from './index.js'
Vue.use(Message)

// 页面调用
this.$message({
    type: 'success',
    content: '成功信息提示',
    duration: 3000
})
```