---
layout:     post
title:      移动端浏览器类型以及版本号判断
summary:
categories: Utility
technique: true
---

## | 浏览器以及版本号区分

```javascript
var ua = navigator.userAgent.toLowerCase();
window.browser = {
    versions: function() {
        return {
            trident: ua.indexOf('trident') > -1, //IE内核
            presto: ua.indexOf('presto') > -1, //opera内核
            webKit: ua.indexOf('applewebkit') > -1, //苹果、谷歌内核
            gecko: ua.indexOf('gecko') > -1 && ua.indexOf('khtml') == -1, //火狐内核
            mobile: !!ua.match(/applewebkit.*mobile.*/), //是否为移动终端
            ios: !!ua.match(/\(i[^;]+;( u;)? cpu.+mac os x/), //ios终端
            android: ua.indexOf('android') > -1 || ua.indexOf('adr') > -1, //android终端
            iPhone: ua.indexOf('iphone') > -1, //是否为iPhone或者QQHD浏览器
            iPad: ua.indexOf('ipad') > -1, //是否iPad
            webApp: ua.indexOf('safari') == -1, // 是否web应用程序，没有头部与底部
            WeChat: ua.indexOf("micromessenger") > -1,  // 微信
            QQBuildIn: ua.match(/\sqq/i) == " qq" && ua.indexOf("micromessenger") < 0, // QQ 内置
            QQ: ua.indexOf("mqqbrowser") > -1 && ua.indexOf("micromessenger") < 0 && ua.match(/\sqq/i) != " qq",
            UC: ua.indexOf("ucbrowser") > -1, // UC
            Baidu: ua.indexOf("baidubrowser") > -1 || ua.indexOf("baiduboxapp") > -1,  // 百度
            QH360: ua.indexOf("qihoobrowser") > -1 || u.match(/\s360\s/i) == " 360 ",  // 360 极速
            Safari: ua.indexOf("safari") > -1 && ua.indexOf("iphone") > -1, // IOS Safari
        };
    }(),
    versionInfo() { // 判断浏览器，返回浏览器类型以及版本号
        let res = null;
        let uaVersion = {
            YdtApp: this.versions.android?["", "Android"]:["", "IOS"],
            WeChat: ua.match(/micromessenger\/([\d.]+)/),
            QQBuildIn: ua.match(/qq\/([\d.]+)/),
            QQ: ua.match(/mqqbrowser\/([\d.]+)/),
            UC: ua.match(/ucbrowser\/([\d.]+)/),
            Baidu: ua.match(/baidubrowser\/([\d.]+)/) || ua.match(/baiduboxapp\/([\d.]+)/),  // 百度浏览器/百度
            QH360: ua.match(/qihoobrowser\/([\d.]+)/) || ua.match(/browser\s([\d.\/\(\)]+)/),
            Safari: ua.match(/safari\/([\d.]+)/),
        }
        for(let key in uaVersion) {
            if(this.versions[key]) {
                res = { type: key, version: uaVersion[key] && uaVersion[key][1]};
                break;
            }
        }
        return res || { type: "others", version: "others"};
    },
}
```


网络流传判断 360 浏览器方法：[兼容性不咋滴]

```javascript
var is360 = (function(option, value){
    var mimeTypes = navigator.mimeTypes;
    for (var mt in mimeTypes) {
        if (mimeTypes[mt][option] == value) {
            return true;
        }
    }
    return false;
})("type", "application/vnd.chromium.remoting-viewer");
```


## | 区分 Android IOS

```javascript
if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
} else if (/(Android)/i.test(navigator.userAgent)) {
}
```
