---
layout:     post
title:      移动端遮罩层、滚动、及touch兼容问题 
summary: 
categories: Project
technique: true
---

## | vue 遮罩层阻止默认滚动事件


给遮罩层添加 `@touchmove.prevent`

`<div class="zhezhao" @touchmove.prevent ></div>`

若无遮罩层标签，动态添加，且对部分标签有效。如下两个 class 内不阻止默认事件，除此之外其他标签组织默认事件：

```javascript
window.addEventListener('touchmove',function(e){
    if(e.target.className !== "picker-item" && e.target.className !== 'picker-item picker-selected'){
        e.preventDefault();
    }
},{ passive: false })
```

**JS方法：**

当遮罩层出现时：`document.body.style.position = 'fixed'`;
当遮罩层消失时：`document.body.style.position = 'static'`;

（ 或者 overflow: hidden; position 容易出现样式错位问题，overflow 有时不太好使）

**点击两次**

iview 下拉选择时，弹出的 option 未选择，鼠标移出隐藏下拉，点击其他事件点击两次才能有效。第一次点击隐藏 option，第二次点击才是事件。



## | 监听滚动 scroll 失效

解决办法：父辈元素高度不能固定。body/app
若父元素固定高度，`addEventListener('scroll',()=>{},true)` 加 true，pc 可以，移动端无效；

## | 移动端touchend兼容问题

1. 解决 touchend 在部分安卓手机上不执行

    ```javascript
    dom.addEventListener("touchstart", (event) => {
        // 判断默认行为是否可以被禁用
        if (event.cancelable) {
            // 判断默认行为是否已经被禁用
            if (!event.defaultPrevented) {
                event.preventDefault();
            }
        }
    }, false)
    ``` 


2. touchmove 设置touch范围：

    ```javascript
    gtouchmove(e) {
        let dom = this.$refs.fuBtn;
        let offLeft = dom.getBoundingClientRect().left; // 按钮左边距
        let offTop = dom.offsetTop;

        // pageX/pageY 相对滚动页面的xy坐标； clientX/clientY 相对屏幕的xy坐标；用pageX/pageY能防止安卓机页面滑动获取坐标位置不准确
        let moveX = e.touches[0].pageX; 
        let moveY = e.touches[0].pageY;
        let DOMWidth = dom.offsetWidth;
        let DOMHeight = dom.offsetHeight; 

        // 设置手指活动范围为绿色按钮位置，超出范围则停止
        if (moveX < offLeft || moveX > (offLeft + DOMWidth) || moveY < offTop || moveY > (offTop + DOMHeight)) {
            this.gtouchend();
        }
    },
    ```
 

