---
layout: post
title: 小程序音/视频
summary:
categories: WX
technique: true
---

### 问题

1. 播放重复音频，第二次播放没有声音，监听事件不执行；解决：第一次播放结束后，stop音频播放

### 音频&录音

***录音***

方法1、[wx.startRecord](https://developers.weixin.qq.com/miniprogram/dev/api/media/recorder/wx.startRecord.html)

  当主动调用 wx.stopRecord，或者录音超过1分钟时自动结束录音。

方法2、RecorderManager，最大支持录制10分钟，示例如下

```javascript
this.recorderManager = wx.getRecorderManager();
this.recorderManager.onStart(() => {
  console.log("recorder start");
});
this.recorderManager.onStop(res => {
  console.log("recorder stop", res);
  const { tempFilePath } = res;
  wx.getFileSystemManager().readFile({
    filePath: tempFilePath,
    encoding: 'base64', //编码格式
    success: res => { //成功的回调
      console.log(res.data)
    }
  })
  // wxfile://tmp_bb68f0449d3ff39733cb45c3f1d0b724.m4a
});

// 开始录音
startVoice() {
this.recorderManager.start(options);
},
// 结束录音
stopVoice() {
this.recorderManager.stop();
},
```


***播放音频***

```javascript
const innerAudioContext = wx.createInnerAudioContext()
innerAudioContext.autoplay = true
innerAudioContext.src = 'https://cdn.caomall.net/15290437711272352156.mp3'
innerAudioContext.onPlay(() => {
  console.log('开始播放')
})
innerAudioContext.onError((res) => {
  console.log(res.errMsg)
  console.log(res.errCode)
})
```

### 相机&录屏

***录屏***

超过30s或页面 onHide 时会结束录像

```javascript
this.ctx = wx.createCameraContext(); // 获取CameraContext 实例
const that = this;
// 获取 Camera 实时帧数据
this.listener = this.ctx.onCameraFrame(frame => {
  console.log(frame.data instanceof ArrayBuffer, frame.width, frame.height);
  const arrayBuffer = new Uint8Array(frame.data);
  const base64 = wx.arrayBufferToBase64(arrayBuffer);
  console.log(base64);
});
// 开始录像
startRecord() {
  this.ctx.startRecord({
    success: res => {
      console.log("startRecord");
    },
    timeoutCallback(res) { // 超过30s或页面 onHide 时会结束录像
      console.log(res, "startRecord end");
    }
  });
},
// 结束录像
stopRecord() {
  this.ctx.stopRecord({
    success: res => {
      console.log(res.tempVideoPath, "结束");
      // http://tmp/wx6089a9c50a67fe8a.o6zAJs79Isyn6LP7Fcr2lJR2aBSc.GkS472pxdY8O70aba5a03f91c2957a56f928ce4579cf.webm
      // this.setData({
      //   src: res.tempThumbPath,
      //   videoSrc: res.tempVideoPath
      // });
    }
  });
},
```

***拍照***

拍照IOS有防偷拍设置，即使手机设置静音也会有拍照声音

```javascript
this.ctx.takePhoto({
  quality: "high",
  success: res => {
    this.setData({
      src: res.tempImagePath
    });
  }
});
```

### 画面录制器

createMediaRecorder 用于WebGL动画录制，不能用于录屏

```javascript
const query = wx.createSelectorQuery()
const a = query.select('#target')
let MediaRecorder = wx.createMediaRecorder(a)

function loop() {
  console.log('loop recorder');
  MediaRecorder.requestFrame(loop) // 录制下一帧
}

MediaRecorder.on('start', () => {
  console.log('开始录制')
  wx.showToast({
    title: '开始录制',
  })
  loop();
})
MediaRecorder.on('stop', ({ tempFilePath, duration, fileSize }) => {
  console.log('结束录制', tempFilePath, duration, fileSize)
  wx.showToast({
    title: '结束录制',
  })
  this.listener.stop();
  wx.navigateTo({
    url: '/pages/videoplay/play?videoPath=' + tempFilePath,
  })
})

MediaRecorder.start();

setTimeout(() => {
  MediaRecorder.stop();
}, 20 * 1000)
```
