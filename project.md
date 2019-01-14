---
layout: post
title: Project
---

 
## | 遇到的问题

**页面空白原因**

- ES6 兼容问题，安装插件 `babel-polyfill": "^6.26.0`
- vue-cli config 中文件不转译 ES6
- 外部引入 vux，样式影响
- 代码报错 

**token 获取**

背景：H5 嵌在原生，首页打包放原生本地，首页跳转页面调原生方法。

问题：首页获取到的token本地存储，其他页面获取不到。

解决办法：原生打开 H5 页面，路径后拼接参数，H5 本地存储。


## | 功能实现

**复制**

安装 "clipboard": "^2.0.4"

```html
<span ref="copy_el"><span id="adminUrl">1111</span></span>
<button 
    class="btnCopy" 
    data-clipboard-action="copy" 
    data-clipboard-target="#adminUrl"
>复制</button>
```
```javascript
import Clipboard from "clipboard"   
let clipboard = new Clipboard('.btnCopy');
clipboard.on('success', (e) => {  // 复制成功
    e.clearSelection();  // 清除选中样式（蓝色）
});
clipboard.on('error', (e) => { // 复制失败
    // todo...
});
```

**输入验证码**

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

```less
.code {
    margin: .36rem 0 .13rem;
    display: flex;
    justify-content: space-between;
    .num {
        display: flex;
        justify-content: space-around;
        position: relative;
        li {
            border: 1px solid #EEEEEE;
            border-radius: 2px;
            width: .34rem;
            height: .34rem;
            line-height: .34rem;
            margin-right: .03rem;
            text-align: center;
            font-family: PingFangSC-Medium;
            font-size: .18rem;
            color: #333333;
        }
        .ipt {
            position: absolute;
            width: 200%;
            height: 100%;
            background: transparent;
            left: -100%; 
            top: 0;
            text-indent: -999em;
        }
    } 
}
```






 