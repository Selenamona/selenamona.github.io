---
layout:     post
title:      异步执行顺序
summary: 宏任务与微任务
categories: Js
---


### 宏任务与微任务

一个宿主环境只有一个事件循环，但可以有多个任务队列。宏任务队列（macro task）与微任务队列（micro task）就是其中之二。   

**每次事件循环的时候，会先执行宏任务队列中的任务，然后再执行微任务队列中的任务。**  

- 宏任务：script（全局任务）, setTimeout, setInterval, setImmediate, I/O, UI rendering.  
- 微任务：process.nextTick, Promise, Object.observer, MutationObserver.


### 事件循环

```JS
for(var i = 0; i < 5; i++){
    setTimeout(function after() {
        console.log(i);
    }, 0);
}

55555
```

