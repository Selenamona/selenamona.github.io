---
layout: post
title: 常用方法
---

> I hear and I forget. I see and I remember. I do and I understand.
> learning, trying, teaching.



## 复制文本

```html
<div class="copyBtn" :copy-label="复制内容" @click="copyText">复制</div>
```
```javascript
copyText() {
  const clipboard = new Clipboard(".copyBtn", {
    text: function(trigger) {
      return trigger.getAttribute("copy-label"); // 返回需要复制的内容
    }
  });
  clipboard.on("success", e => {
    e.clearSelection(); // 清除选中样式（蓝色）
    Vue.prototype.$toast("复制成功");
  });
  clipboard.on("error", () => {
    Vue.prototype.$toast("复制失败");
  });
}
```

##










