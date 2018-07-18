---
layout:     post
title:      异步执行-宏任务与微任务 
summary: Event Loop 是 javascript 的执行机制
categories: Js
technique: true
---

> JavaScript 是单线程的语言  
> Event Loop 是 javascript 的执行机制


## 宏任务与微任务

一个宿主环境只有一个事件循环，但可以有多个任务队列。宏任务队列（macro task）与微任务队列（micro task）就是其中之二。   

**每次事件循环的时候，会先执行宏任务队列中的任务，然后再执行微任务队列中的任务。**  


<img src="https://raw.githubusercontent.com/Selenamona/Selenamona.github.io/master/assets/images/tasks.png" width="600"/>

- 宏任务：script（全局任务）, setTimeout, setInterval, setImmediate, I/O, UI rendering.  
- 微任务：process.nextTick, Promise, Object.observer, MutationObserver.


## javascript 事件循环

```javascript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})

process.nextTick(function() {
    console.log('6');
})

new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

=> 输出结果：1 7 6 8 2 4 3 5 9 11 10  12 

第一轮事件循环流程分析如下：

1. 整体 script 作为第一个宏任务进入主线程，遇到 console.log，输出1。
2. 遇到 setTimeout，其回调函数被分发到宏任务 Event Queue 中，记为 setTimeout1。
3. 遇到 process.nextTick()，其回调函数被分发到微任务 Event Queue 中，记为 process1。
4. 遇到 Promise，new Promise 直接执行，输出 7。then 被分发到微任务 Event Queue 中，记为 then1。
5. 又遇到了setTimeout，其回调函数被分发到宏任务 Event Queue中，记为 setTimeout2。

第一轮事件循环宏任务执行结束，输出了1和7。有可执行的微任务 process1 和 then1，输出6和8。

两个微任务执行结束，第一轮事件循环正式结束，开始执行新的宏任务 setTimeout1。

setTimeout1 中首先输出2。接下来 process.nextTick() 分发到微任务 Event Queue 中，记为process2。
new Promise 立即执行输出4，then 也分发到微任务 Event Queue 中，记为 then2

第二轮事件循环宏任务执行结束，执行两个微任务 process2 和 then2。 输出 3 和 5。

同理，第三轮事件循环结束，第三轮输出9，11，10，12。

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。

