---
layout:     post
title:      vue-cli 项目本地起 https
summary:    expressjs 配置自有证书 https 服务
categories: Vue
technique: true
---

## | /build/dev-server.js

**新增代码 1 ：**

```javascript
// [注意放在 path 变量定义后]
const https = require('https');
const fs = require('fs'); 
var privateKey  = fs.readFileSync(path.join(__dirname, './cert/private.pem'), 'utf8');
var certificate = fs.readFileSync(path.join(__dirname, './cert/file.crt'), 'utf8');
var credentials = {key: privateKey, cert: certificate};
```

**新增代码 2 ：**

在 `const port = process.env.PORT || config.dev.port` 后面添加一行    
`const httpsPort = process.env.httpsPort || config.dev.httpsPort`

注意：config/index.js  dev 对象加入 `httpsPort: process.env.PORT || 自定义端口号`

**新增代码 3 ：**

在 `server = app.listen(port)` 后面加入以下代码开启https服务：

```js
    server = app.listen(port);
    var httpsServer = https.createServer(credentials, app);
    httpsServer.listen(httpsPort, function() {
        console.log('HTTPS Server is running on: https://localhost:%s', httpsPort);
    });
```
## | 生成本地证书

- 在 build 目录下新建一个目录 cert
- 生成私钥key文件[git-bash 下执行] 
    `openssl genrsa 1024 > ./build/cert/private.pem`
- 通过上面生成的私钥文件生成CSR证书签名
    `openssl req -new -key  ./build/cert/private.pem -out csr.pem`
- 通过上述私钥文件和CSR证书签名生成证书文件
    `openssl x509 -req -days 365 -in csr.pem -signkey  ./build/cert/private.pem -out  ./build/cert/file.crt`

此时 build/cert 目录下生成两个文件 private.pem/file.crt



[expressjs 配置自有证书 https 服务](https://www.jianshu.com/p/27071f5a076f)
[vue-cli项目如何加入本地https](https://github.com/zp1112/blog/issues/4)
