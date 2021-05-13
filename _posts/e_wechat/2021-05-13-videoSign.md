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
