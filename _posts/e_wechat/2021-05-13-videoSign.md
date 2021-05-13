---
layout: post
title: 小程序图片处理&pdf预览
summary:
categories: WX
technique: true
---


### PDF预览

```javascript
// 方法一
readPdf () {
  wx.downloadFile({
    url: 'https://aa.pdf',
    success: function (res) {
      const filePath = res.tempFilePath
      wx.openDocument({
        filePath: filePath,
        success: function (res) {
          console.log('打开文档成功')
        }
      })
    }
  })
}
// 方法二
<web-view src="https://aa.pdf"></web-view>
```

### 图片处理

本地图片路径转base64

```javascript
/**
 * 本地图片路径转base64
 * @param {String} filePath 图片本地路径
 * @returns
 */
const imgToBase64 = (filePath) => {
  return new Promise((resolve, reject) => {
    wx.getFileSystemManager().readFile({
      filePath: filePath,
      encoding: 'base64',
      success (res) {
        resolve(res.data)
        // resolve('data:image/png;base64,' + res.data)
      },
      fail (res) {
        reject(res)
      }
    })
  })
}
```

### 文本转语音播放

```javascript
// app.json
"plugins": {
  "WechatSI": {
    "version": "0.3.4",
    "provider": "wx069ba97219f66d99"
  }
}
// 使用
const plugin = requirePlugin('WechatSI')
const manager = plugin.getRecordRecognitionManager()
/**
  * 文本转语音
  */
textToSpeech () {
  const that = this
  plugin.textToSpeech({
    lang: 'zh_CN',
    tts: true,
    content: '本人声明：易鑫的或合作经销商的工作人员已向我详细解释且我已阅读、理解并同意融资租赁合同条款、融资租赁关系及本确认函内容，知晓并愿意承担每一项费用、支付及金额、确认签署的融资租赁合同非借款合同。',
    success: function (res) {
      console.log('succ tts', res.filename)
      that.url = res.filename
      that.text_audio_status()
    },
    fail: function (res) {
      console.log('fail tts', res)
    }
  })
},
/**
  *  用来监听文字转语音的播放情况
  */
text_audio_status: function () {
  wx.setInnerAudioOption({
    obeyMuteSwitch: false,
    success () {
      console.log('开启静音模式下播放音乐的功能')
    },
    fail () {
      console.log('静音设置失败')
    }
  })
  const innerAudioContext = wx.createInnerAudioContext()// 创建音频实例
  innerAudioContext.src = this.url // 设置音频地址
  // innerAudioContext.src = 'https://cdn.caomall.net/15290437711272352156.mp3' // 设置音频地址
  innerAudioContext.play() // 播放音频
  innerAudioContext.onPlay(() => {
    console.log('监听开始播放')
  })
  innerAudioContext.onEnded(() => {
    console.log('监听播报结束，可在结束中进行相应的处理逻辑')
    innerAudioContext.stop()
    // 播放停止，销毁该实例,不然会出现多个语音重复执行的情况
    console.log('销毁innerAudioContext实例')
    innerAudioContext.destroy()
  })
  innerAudioContext.onError(() => {
    console.log('监听语音播放异常')
    innerAudioContext.destroy()// 销毁播放实例
  })
},
```
