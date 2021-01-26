---
layout: post
title: 企业微信配置可信域名
summary:
categories: WX
technique: true
---

1、输入可信域名 -> 申请校验域名 -> 下载校验文件xxx.txt
2、校验文件放置在域名根目录下，确保可以访问，如：https://xxx.com/xxx.txt

* 域名根目录即域名所指静态文件存放目录


> 配置vue项目根目录下的txt文件打包到域名根目录下：

```javascript
// vue-cli2.0 copy custom static assets
new CopyWebpackPlugin([
  {
    from: path.resolve(__dirname, '../static'),
    to: config.build.assetsSubDirectory,
    ignore: ['.*']
  },
  {
    from: path.resolve(__dirname, '../MP_verify_NOpgblfxgXaX9OrQ.txt'),
    to: path.resolve(__dirname, '../dist/MP_verify_NOpgblfxgXaX9OrQ.txt'),
  }
])
// vue-cli3.0 及以上直接放置在public文件下，打包即可
```


> vue项目为二级域名情况下，无法通过前端项目打包校验文件，则需要服务器配置：比如服务器上前端项目dist静态文件地址为 `data1/web-static/app-adress`，可以在 data1 文件下上传 txt校验文件，前端项目发版配置打包生成后，拷贝 data1 中的 txt 文件至 `data1/web-static/app-adress` 目录下


[企业微信开发文档](https://work.weixin.qq.com/api/doc)
[错误码查询](https://open.work.weixin.qq.com/devtool/query)
