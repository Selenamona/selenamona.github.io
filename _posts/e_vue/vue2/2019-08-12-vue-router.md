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

## | 面试题汇总

### | 重定向页面 & 配置404页面

```javascript
// 重定向方法一
const router = new VueRouter({
    routes: [ { path: '/a', redirect: '/b' } ]
})
// 重定向方法二
const router = new VueRouter({
    routes: [  { path: '/a', redirect: { name: 'foo' }} ]
})
// 重定向方法三
const router = new VueRouter({
    routes: [
        {
            path: '/a',
            redirect: to =>{
                const { hash, params, query } = to
                if (query.to === 'foo') {
                    return { path: '/foo', query: null }
                }else{
                   return '/b'
                }
            }

        }
    ]
})
// 配置404页面
const router = new VueRouter({
    routes: [ { path: '*', redirect: {path: '/'} } ]
})
```

### | 路由模式以及区别

1、hash: 兼容所有浏览器，包括不支持 HTML5 History Api 的浏览器，例http://www.abc.com/#/index，hash值为#/index， hash的改变会触发hashchange事件，通过监听hashchange事件来完成操作实现前端路由。hash值变化不会让浏览器向服务器请求。
```javascript
// 监听hash变化，点击浏览器的前进后退会触发
window.addEventListener('hashchange', function(event){
    let newURL = event.newURL; // hash 改变后的新 url
    let oldURL = event.oldURL; // hash 改变前的旧 url
},false)
```

2、history: 兼容能支持 HTML5 History Api 的浏览器，依赖HTML5 History API来实现前端路由（例如 http://yoursite.com/user/id）。需要后台配置支持，初次访问或者刷新都会向服务器请求，如果没有请求到对应的资源就会返回404，所以路由地址匹配不到任何静态资源，则应该返回同一个index.html 页面，需要在nginx中配置。

3、abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式。


### | 全局导航守卫

router.beforeEach：全局前置守卫。
router.beforeResolve：全局解析守卫。
router.afterEach：全局后置钩子。不接受 next 的参数

```javascript
import VueRouter from 'vue-router';
const router = new VueRouter({
    mode: 'history',
    base: '/',
    routes,
    scrollBehavior(to, from, savedPosition) {
        if (savedPosition) {
            return savedPosition;
        } else {
            return { x: 0, y: 0 };
        }
    }
})
router.beforeEach((to, from, next) => {
    //...
    next();
})
router.beforeResolve((to, from, next) => {
    //...
    next();
})
router.afterEach((to, from) => {
    //...
});
```

### | 组件内使用的导航守卫

beforeRouteLeave：在失活的组件里调用离开守卫。
beforeRouteUpdate：在重用的组件里调用
beforeRouteEnter：在进入对应路由的组件创建前调用。

### | 完整的导航守卫流程

- 导航被触发。
- 在失活的组件里调用离开守卫beforeRouteLeave(to,from,next)。
- 调用全局的beforeEach( (to,from,next) =>{} )守卫。
- 在重用的组件里调用 beforeRouteUpdate(to,from,next) 守卫。
- 在路由配置里调用beforeEnter(to,from,next)路由独享的守卫。
- 解析异步路由组件。
- 在被激活的组件里调用beforeRouteEnter(to,from,next)。
- 在所有组件内守卫和异步路由组件被解析之后调用全局的beforeResolve( (to,from,next) =>{} )解析守卫。
- 导航被确认。
- 调用全局的afterEach( (to,from) =>{} )钩子。
- 触发 DOM 更新。
- 用创建好的实例调用beforeRouteEnter守卫中传给 next 的回调函数

> 路由导航守卫都是在Vue实例生命周期钩子函数之前执行的

### | router-link

<router-link>是Vue-Router的内置组件，在具有路由功能的应用中作为声明式的导航使用。
<router-link>有8个props，其作用是：

to：必填，表示目标路由的链接。当被点击后，内部会立刻把to的值传到router.push()，所以这个值可以是一个字符串或者是描述目标位置的对象。<router-link to="home">Home</router-link>
<router-link :to="'home'">Home</router-link>
<router-link :to="{ path: 'home' }">Home</router-link>
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
<router-link :to="{ path: 'user', query: { userId: 123 }}">User</router-link>
复制代码注意path存在时params不起作用，只能用query
replace：默认值为false，若设置的话，当点击时，会调用router.replace()而不是router.push()，于是导航后不会留下 history 记录。
append：设置 append 属性后，则在当前 (相对) 路径前添加基路径。
tag：让<router-link>渲染成tag设置的标签，如tag:'li,渲染结果为<li>foo</li>。
active-class：默认值为router-link-active,设置链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 linkActiveClass 来全局配置。
exact-active-class：默认值为router-link-exact-active,设置链接被精确匹配的时候应该激活的 class。默认值可以通过路由构造函数选项 linkExactActiveClass 进行全局配置的。
exact：是否精确匹配，默认为false。<!-- 这个链接只会在地址为 / 的时候被激活 -->
<router-link to="/" exact></router-link>
复制代码
event：声明可以用来触发导航的事件。可以是一个字符串或是一个包含字符串的数组，默认是click。

### | 切换路由后，新页面要滚动到顶部

```javascript
const router = new Router({
    mode: 'history',
    base: process.env.BASE_URL,
    routes,
    scrollBehavior(to, from, savedPosition) {
        if (savedPosition) {
            return savedPosition;
        } else {
            return { x: 0, y: 0 };
        }
    }
});
```

### | Vue路由怎么跳转打开新窗口

```javascript
const obj = {
    path: xxx,//路由地址
    query: {
       mid: data.id//可以带参数
    }
};
const {href} = this.$router.resolve(obj);
window.open(href, '_blank');
```
