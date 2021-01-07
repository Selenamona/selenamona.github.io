---
layout: post
title: html/canvas 转图片并下载
summary:
categories: Utility
technique: true
---

## | 总结

1. 使用 canvas 的 api 时, 使用的都是绝对数值或坐标位置[不带单位]。若项目中使用 rem 适配，绘制 canvas 时，需将 rem 转换为 px：获取设备宽度及 UI 稿大小换算百分比 将转换后的数值传入 canvas 的方法中！如：设备高度/canvas 绘制高度 = 750/设计稿中 div 高度

2. 图片加载异步；canvas 绘制异步

3. 插件实现[html2canvas](http://html2canvas.hertzen.com/)

```javascript
html2canvas(changeDOM, {
  allowTaint: true, // 允许污染，被污染的 canvas 是没法使用 toDataURL() 转 base64 流的
  useCORS: true, //【重要】开启跨域配置
  logging: false, // 是否开启日志
  canvas: canvas,
  width: changeDOM.offsetWidth,
  height: changeDOM.offwetHeight,
  onrendered: function (canvas) {
    var dataURL = canvas.toDataURL('image/png');
    var img = new Image();
    img.src = dataURL;
    img.onload = function () {
      console.log(img);
    };
  },
});
// github上解决清晰度问题：hidpi-canvas-polyfill-master
```

4. 渲染图片：确保事件函数 onload 是在图片加载完成之前绑定上去的

5. 生成的最上层图片不能加 `pointerEvents:none`, 否则在微信中不能长按保存

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

PC 端`downloadFileByBase64()`实现点击下载
移动端长按保存

```javascript
function downloadFileByBase64(base64, name) {
  var myBlob = dataURLtoBlob(base64);
  var myUrl = URL.createObjectURL(myBlob);
  downloadFile(myUrl, name);
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

function downloadFile(url, name = "What's the fuvk") {
  var a = document.createElement('a');
  a.setAttribute('href', url);
  a.setAttribute('download', name);
  a.setAttribute('target', '_blank');
  let clickEvent = document.createEvent('MouseEvents');
  clickEvent.initEvent('click', true, true); //initEvent 不加后两个参数在FF下会报错  事件类型，是否冒泡，是否阻止浏览器的默认行为
  a.dispatchEvent(clickEvent);
}
```

## | 绘制文字折行/省略号

```javascript
// 绘制文字折行/省略号
const dealWords = (options) => {
  options.ctx.setFontSize(options.fontSize); //设置字体大小
  var allRow = Math.ceil(
    options.ctx.measureText(options.word).width / options.maxWidth
  ); //实际总共能分多少行
  var count = allRow >= options.maxLine ? options.maxLine : allRow; //实际能分多少行与设置的最大显示行数比，谁小就用谁做循环次数

  var endPos = 0; //当前字符串的截断点
  for (var j = 0; j < count; j++) {
    var nowStr = options.word.slice(endPos); //当前剩余的字符串
    var rowWid = 0; //每一行当前宽度
    if (options.ctx.measureText(nowStr).width > options.maxWidth) {
      //如果当前的字符串宽度大于最大宽度，然后开始截取
      for (var m = 0; m < nowStr.length; m++) {
        rowWid += options.ctx.measureText(nowStr[m]).width; //当前字符串总宽度
        if (rowWid > options.maxWidth) {
          if (j === options.maxLine - 1) {
            //如果是最后一行
            options.ctx.fillText(
              nowStr.slice(0, m - 1) + '...',
              options.x,
              options.y + (j + 1) * 18
            ); //(j+1)*18这是每一行的高度
          } else {
            options.ctx.fillText(
              nowStr.slice(0, m),
              options.x,
              options.y + (j + 1) * 18
            );
          }
          endPos += m; //下次截断点
          break;
        }
      }
    } else {
      //如果当前的字符串宽度小于最大宽度就直接输出
      options.ctx.fillText(
        nowStr.slice(0),
        options.x,
        options.y + (j + 1) * 18
      );
    }
  }
};

dealWords({
  ctx: ctx, //画布上下文
  fontSize: 11, //字体大小
  word: info.description, //需要处理的文字
  maxWidth: 240, //一行文字最大宽度
  x: 10, //文字在x轴要显示的位置
  y: 222, //文字在y轴要显示的位置
  maxLine: 3, //文字最多显示的行数
});
```

## | 解决小程序 getImageInfo 无法获取 base64 图片信息

```javascript
// 解决 getImageInfo 无法获取 base64 图片信息
function dealBase64(base64data, cb) {
  const fsm = uni.getFileSystemManager();
  const FILE_BASE_NAME = 'tmp_base64src'; // 自定义文件名
  const [, format, bodyData] =
    /data:image\/(\w+);base64,(.*)/.exec(base64data) || [];
  if (!format) {
    // format-图片后缀
    return new Error('ERROR_BASE64SRC_PARSE');
  }
  const time = new Date().getTime(); // 小程序中 base64 生成本地缓存文件不会被覆盖掉，所以需要手动清除缓存文件，或以时间戳为文件目录之一
  const filePath = `${wx.env.USER_DATA_PATH}/${time}.${format}`;
  const buffer = wx.base64ToArrayBuffer(bodyData);
  fsm.writeFile({
    filePath,
    data: buffer,
    encoding: 'binary',
    success() {
      cb && cb(filePath);
    },
    fail() {
      return new Error('ERROR_BASE64SRC_WRITE');
    },
  });
}
```

## | 小程序绘制图片

```javascript
// 绘制头像
drawBigImg(ctx, {
  URL: info.userAvatar,
  x: 10,
  y: 16,
  width: 27.7,
  height: 27.7,
  type: 'circle',
});
const drawBigImg = (ctx, config) => {
  return new Promise((resolve, reject) => {
    uni.getImageInfo({
      src: config.URL,
      success(image) {
        console.log('success', config.URL);
        let img = {
          x: config.x,
          y: config.y,
          width: config.width,
          height: config.height,
        };
        if (config.type == 'circle') {
          ctx.save();
          //开始路径画圆,剪切处理
          ctx.beginPath();
          ctx.arc(
            img.width / 2 + img.x,
            img.height / 2 + img.y,
            img.width / 2,
            0,
            Math.PI * 2,
            false
          );
          ctx.clip(); //剪切路径
          ctx.drawImage(image.path, img.x, img.y, img.width, img.height);
          ctx.restore(); //恢复状态
        } else {
          ctx.drawImage(image.path, img.x, img.y, img.width, img.height);
        }
        ctx.draw(true);
        resolve();
      },
      fail(err) {
        console.log(err);
        uni.showToast({
          // title: "图片加载失败",
          title: err.errMsg,
          duration: 2000,
          icon: 'none',
        });
        reject();
      },
    });
  });
};
```

## | 文字居中

```javascript
/**
 * 文字居中
 * @param {Object} _paint  // context对象
 * @param {Object} _text   // 文字
 * @param {Object} _fontSzie
 * @param {Object} _color
 * @param {Object} _height  // 绘制区域的高度
 * @param {Object} _width   // 绘制区域的宽度
 */
function canvas_text(_paint, _text, _fontSzie, _color, _width, _height) {
  _paint.save();
  _paint.font = _fontSzie;
  _paint.fillStyle = _color;
  _paint.textAlign = 'center';
  _paint.fillText(_text, _width, _height);
  _paint.restore();
}
```

## | 圆角/虚线

```javascript
// 绘制圆角区域
drawRoundedRect(ctx, 0, 86, 260, 306, 8, true, false);
function drawRoundedRect(ctx, x, y, width, height, r, fill, stroke) {
  ctx.save();
  ctx.beginPath();
  ctx.moveTo(x + r, y);
  // 上半部分圆角
  // ctx.arcTo(x + width, y, x + width, y + r, r); // right top
  // ctx.lineTo(x + width, y + height)
  // ctx.lineTo(x, y + height)
  // ctx.lineTo(x, y + r)
  // ctx.arcTo(x, y, x + r, y, r);

  // 全部圆角
  ctx.arcTo(x + width, y, x + width, y + r, r); // draw right side and bottom right corner
  ctx.arcTo(x + width, y + height, x + width - r, y + height, r); // draw bottom and bottom left corner
  ctx.arcTo(x, y + height, x, y + height - r, r); // draw left and top left corner
  ctx.arcTo(x, y, x + r, y, r);

  if (fill) {
    ctx.setFillStyle('#FFFFFF');
    ctx.fill();
  }
  if (stroke) {
    ctx.stroke();
  }
  ctx.restore();
}

// 绘制圆角矩形图片
drawRoundedImg('../../static/common/personcenter.png', ctx, 0, 0, 260, 150, 8);
// path-图片路径 r-圆角大小
function drawRoundedImg(path, ctx, x, y, width, height, r) {
  ctx.save();
  ctx.beginPath();
  ctx.moveTo(x + r, y);
  ctx.arcTo(x + width, y, x + width, y + r, r);
  ctx.lineTo(x + width, height);
  ctx.lineTo(x, height);
  ctx.lineTo(x, y + r);
  ctx.arcTo(x, y, x + r, y, r);
  ctx.clip();
  ctx.drawImage(path, x, y, width, height);
  ctx.restore();
}

// 绘制虚线
ctx.beginPath();
ctx.setLineDash([5, 1], 0);
ctx.setStrokeStyle('#D0D0D0');
ctx.setLineWidth(1);
ctx.moveTo(10, 301);
ctx.lineTo(249, 301);
ctx.stroke();
```
