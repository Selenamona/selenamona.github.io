---
layout:     post
title:      html/canvas 转图片并下载
summary:
categories: Utility
technique: true
---

## | 总结

1. 使用canvas的api时, 使用的都是绝对数值或坐标位置[不带单位]。若项目中使用rem适配，绘制canvas时，需将rem转换为px：获取设备宽度及UI稿大小换算百分比 将转换后的数值传入canvas的方法中！如：设备高度/canvas绘制高度 = 750/设计稿中div高度

2. 图片加载异步；canvas绘制异步

3. 插件实现[html2canvas](http://html2canvas.hertzen.com/)

```javascript
html2canvas(changeDOM,{
    allowTaint:true, // 允许污染，被污染的 canvas 是没法使用 toDataURL() 转 base64 流的
    useCORS:true, //【重要】开启跨域配置
    logging: false, // 是否开启日志
    canvas: canvas,
    width: changeDOM.offsetWidth,
    height: changeDOM.offwetHeight,
    onrendered:function(canvas){
        var dataURL = canvas.toDataURL("image/png");
        var img = new Image();
        img.src = dataURL;
        img.onload = function () {
            console.log(img)
        }
    }
})
// github上解决清晰度问题：hidpi-canvas-polyfill-master
```

## | canvas 绘制 html 实例

```javascript 
// 获取页面元素
var DOM = document.querySelectorAll(".gainRecord")[0];
var userTxt = document.querySelectorAll(".record_user")[0];
var fordTxt = document.querySelectorAll(".record_ford")[0];
var saoTxt = document.querySelectorAll(".sao")[0];
var addOneTxt = document.querySelectorAll(".addOne")[0];
var recordImg = document.querySelectorAll(".recordImg")[0];
var avatorImg = document.querySelectorAll(".share_avator")[0];
var codeImg = document.querySelectorAll(".code")[0];

// 设置canvas
var canvas = document.createElement("canvas");
var ctx = canvas.getContext("2d");

// 解决生成图片模糊问题
var devicePixelRatio = window.devicePixelRatio || 2; // 屏幕的设备像素比
// 浏览器在渲染canvas之前存储画布信息的像素比
var backingStoreRatio = context.webkitBackingStorePixelRatio ||
    context.mozBackingStorePixelRatio ||
    context.msBackingStorePixelRatio ||
    context.oBackingStorePixelRatio ||
    context.backingStorePixelRatio || 1;
// canvas的实际渲染倍率
var scale = devicePixelRatio / backingStoreRatio;

var width = 268;
var height = 382;
// 获取根据屏幕分辨率，来设置canvas的宽高以获得高清图片
canvas.width = width * ;  
canvas.height = height * scale;
canvas.style.width = width + "px";
canvas.style.height = height + "px";
ctx.scale(scale, scale);

// 添加白色背景
ctx.fillStyle = "#fff";
ctx.fillRect(0, 0, 268, 382);

// 渲染文字：font设置格式相同，防止文字样式覆盖
ctx.textBaseline = "top"; // 解决文字显示不全/位置偏离
ctx.font = "bold 15px/1.6 PingFangSC-Regular"
ctx.fillStyle = "#13171E";
ctx.fillText(userTxt.innerHTML, 55, 265);
ctx.font = "normal 12px/1.6 PingFangSC-Regular";
ctx.fillStyle = "#353537";
ctx.fillText(fordTxt.innerHTML, 55, 285);
ctx.font = "normal 10px/1.6 PingFangSC-Regular";
ctx.fillStyle = "#878A8E";
ctx.fillText(saoTxt.innerHTML, 124, 337);
ctx.fillText(addOneTxt.innerHTML, 104, 355);

// 渲染图片；在html img标签上添加crossorigin="anonymous"解决src跨域
var imgList = [
    { img: recordImg, x: 0, y: 0, width: 268, height: 250 },
    { img: avatorImg, x: 15, y: 264, width: 34, height: 34 },
    { img: codeImg, x: 203, y: 316, width: 50, height: 50 },
]
recordImg.onload = () => { // 图片加载异步
    imgList.map((ele, index) => {
        if (ele.img == avatorImg) { 
            // 将正方形图片切成圆形
            var width = ele.width;
            var height = ele.height;
            ctx.save();
            ctx.beginPath(); //开始路径画圆,剪切处理
            ctx.arc(width / 2 + ele.x, height / 2 + ele.y, width / 2, 0, Math.PI * 2, false);
            ctx.clip(); //剪切路径
            ctx.drawImage(ele.img, ele.x, ele.y, width, height);
            
            ctx.restore(); //恢复状态
        } else {
            ctx.drawImage(ele.img, ele.x, ele.y, ele.width, ele.height);
        }
    })
    // canvas 转成图片，放入DOM中
    var dataURL = canvas.toDataURL('image/jpeg', 1);
    var img = document.createElement("img");
    img.src = dataURL;
    img.setAttribute("id", "html_share_img");
    img.setAttribute('crossOrigin', 'anonymous')
    img.onload = function() { // 渲染图片成功，设置层级最高实现长按保存
        img.style.width = '268px';
        img.style.position = "fixed";
        img.style.top = '.76rem';
        img.style.left = '50%';
        img.style.transform = 'translate(-50%, 0)';
        img.style.opacity = 0;
        img.style.zIndex = 1999;
        document.body.appendChild(img);
    }
}
```


## | 图片下载

PC端`downloadFileByBase64()`实现点击下载   
移动端长按保存

```javascript 
function downloadFileByBase64(base64, name) {
    var myBlob = dataURLtoBlob(base64)
    var myUrl = URL.createObjectURL(myBlob)
    downloadFile(myUrl, name)
}

function dataURLtoBlob(dataurl) {
    var arr = dataurl.split(','),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new Blob([u8arr], { type: mime });
}

function downloadFile(url, name = 'What\'s the fuvk') {
    var a = document.createElement("a")
    a.setAttribute("href", url)
    a.setAttribute("download", name)
    a.setAttribute("target", "_blank")
    let clickEvent = document.createEvent("MouseEvents");
    clickEvent.initEvent("click", true, true); //initEvent 不加后两个参数在FF下会报错  事件类型，是否冒泡，是否阻止浏览器的默认行为
    a.dispatchEvent(clickEvent);
}
``` 

 
 