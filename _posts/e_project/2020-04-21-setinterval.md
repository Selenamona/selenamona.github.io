---
layout:     post
title:      定时器常见问题
summary:
categories: Project
technique: true
---


## | 双定时器

定时器启用前一定要先清除！！！

```javascript
setTimeoutFn(){
    setTimeout(() => {
        this.second = this.second -1;
        if(this.second === 0){
            this.second = 10;
            this.ifFail = false;
        } else {
            this.setTimeoutFn();
        }
    }, 1000);
}
``` 

## 程序化的事件侦听器

- 通过 $on(eventName, eventHandler) 侦听一个事件
- 通过 $once(eventName, eventHandler) 一次性侦听一个事件
- 通过 $off(eventName, eventHandler) 停止侦听一个事件


***在页面挂载时定义计时器，需要在页面销毁时清除定时器***

```javascript
// 常用方法
export default {
    mounted() {
        this.timer = setInterval(() => {
            console.log(Date.now())
        }, 1000)
    },
    beforeDestroy() {
        clearInterval(this.timer)
    }
}

export default {
    mounted() {
        this.creatInterval('hello')
        this.creatInterval('world')
    },
    creatInterval(msg) {
        let timer = setInterval(() => {
            console.log(msg)
        }, 1000)
        this.$once('hook:beforeDestroy', function() {
            clearInterval(timer)
        })
    }
} 
```

[参考详情](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E7%A8%8B%E5%BA%8F%E5%8C%96%E7%9A%84%E4%BA%8B%E4%BB%B6%E4%BE%A6%E5%90%AC%E5%99%A8)