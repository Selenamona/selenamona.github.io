---
layout:     post
title:      遮罩层以及滚动问题 
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