---
layout: post
title: 代码片段
---

> I hear and I forget. I see and I remember. I do and I understand.
> learning, trying, teaching.

### 解析 URL 获取参数
```javascript
function getQueryString(name) {
  var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
  var r = window.location.hash.split("?")[1].match(reg);
  if (r != null) return unescape(r[2]);
  return null;
}
getQueryString("idNo")
```

### 复制文本

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

### 输入验证码

```html
<!-- 忽略反斜杆 -->
<div class="code">
    <ul class="num">
        <li>{\{code.charAt(0)\}}</li>
        <li>{\{code.charAt(1)\}}</li>
        <li>{\{code.charAt(2)\}}</li>
        <li>{\{code.charAt(3)\}}</li>
        <li>{\{code.charAt(4)\}}</li>
        <li>{\{code.charAt(5)\}}</li>
        <input type="tel" maxlength="6" class="ipt" v-model="code" @input="codeIpt">
    </ul>
    <button @click="getCode" v-show="!ifSend">获取验证码</button>
    <button class="reSendMsg" v-show="ifSend">重新获取({{second}})</button>
</div>
```

```javascript
// 输入验证码
codeIpt(){
  this.code = this.code.replace(/[^\d]/, ''); // 只输入数字
},
// 获取验证码
getCode(){
  let timer = setInterval(() => {
      this.second = this.second - 1;
      if(this.second === 0) {
          this.second = 59;
          clearInterval(timer);
      }
  }, 1000)
  // 调用获取验证码的接口
}
```








