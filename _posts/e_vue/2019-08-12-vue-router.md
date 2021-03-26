---
layout: post
title: Vue 路由
summary:
categories: Vue
technique: true
---


## | 路由懒加载

```javascript
// _import_development.js
module.exports = file => require('@/yixin/' + file + '.vue').default
// _import_production.js
module.exports = file => import('@/yixin' + file + '.vue')
// router -> index.js
import Vue from 'vue'
import Router from 'vue-router'
const _import = require('./_import_' + process.env.NODE_ENV)
Vue.use(Router)
export default new Router({
  routes: [
    {
      title:"测试页",
      path: '/abc',
      name: 'abc',
      component: _import('Abc')
    }
  ]
})
```

## | 路由传参

```javascript
// 方法一：query 传参页面刷新参数不会丢失，参数会再 url 上显示
this.$router.push({
    name: 'pageTwo',
    query: {
        userName: "王明",
        userId: "123",
        account: "wangming"
    }
})
// 方法二：params 传参页面刷新参数丢失，参数不会再 url 上显示
this.$router.push({
    name: 'pageTwo',
    params: {
        userName: "王明",
        userId: "123",
        account: "wangming"
    }
})
// 方法三：传参页面刷新参数不会丢失，参数会再 url 上显示
// 1.路由配置
{
    path: '/pageTwo/:id',
    name: 'pageTwo',
    component: pageTwo
}
// 2.页面跳转
this.$router.push({ path: 'pageTwo/name' })
// 3.获取
$route.params.id
```

## | 返回上一页
