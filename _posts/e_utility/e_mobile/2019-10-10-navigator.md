---
layout:     post
title:      Navigator
summary:
categories: Mobile
technique: true
---

## | 属性说明


属性 | 说明
appCodeName |	浏览器的代码名。在所有以 Netscape 代码为基础的浏览器中，它的值是 "Mozilla"。为了兼容起见，在 Microsoft 的浏览器中，它的值也是 "Mozilla"，同时在safari浏览器的console里运行navigator.appCodeName得出的结果还是"Mozilla"，所以这个看起来并不实用，因为IE、chrome、safari返回的都是“Mozilla”。
appName	| 浏览器官方名称。由于兼容性问题，HTML5 规范允许该属性返回 "Netscape"。该属性并不一定能返回正确的浏览器名称。在基于 Gecko 的浏览器 （例如 Firefox）和基于 WebKit 的浏览器（例如 Chrome 和 Safari）中，返回的浏览器名称都是 "Netscape"。
appVersion |	浏览器的平台和版本信息。该特性已经从 Web 标准中删除，虽然一些浏览器目前仍然支持它，但也许会在未来的某个时间停止支持，请尽量不要使用该特性。
connection	| 提获取设备的网络连接信息，用来衡量网络流量速度。Network Information API
cookieEnabled	| 浏览器中是否启用 cookie【布尔值】。启用了 cookie，该属性值为 true。如果禁用了 cookie，则值为 false。
doNotTrack	| 返回用户的do-not-track设置,如果用户不允许网站,内容和广告等进行跟踪,则该值为yes。
geolocation	| 返回一个 Geolocation 对象，据之可访问设备的地理位位置信息。
hardwareConcurrency	| 指明当前浏览器环境所拥有的CPU核心数，这来自于操作系统提供的API来获取。
language	| 浏览器用户界面的语言。当未知的时，返回null。
languages	| 返回一个表示用户已知语言的数组，并按优先顺序排列。
maxTouchPoints	| 返回当前设备能够支持的最大同时触摸的点数。
mediaCapabilities| 让浏览器根据提供的视频格式和编码信息，以及比特率等信息择优播放。
mediaSession	| 允许页面为标准媒体交互提供自定义行为。
mimeTypes	| 返回浏览器支持的Mime类型
onLine | 	系统是否处于脱机模式【布尔值】
permissions	| 返回一个可用于查询或更新某些APIs 的权限状态的对象。
platform | 	运行浏览器的操作系统平台。可能的值有: "Win32", "Linux i686", "MacPPC", "MacIntel"等。
plugins	| 列举出浏览器安装的插件
product	| 返回当前浏览器的产品名称。在任意浏览器下都只返回'Gecko'，此属性只用于兼容的目的。
productSub	| 有关产品的额外信息。Returns the build number of the current browser (e.g., "20060909").
userActivation	 |  
userAgent	| 声明了浏览器用于 HTTP 请求的用户代理头的值。主要是各家浏览器厂商都想要自己的浏览器被其他的兼容，所以都会或多或少的加上一些其他的信息在里面。
vendor	| 浏览器供应商的名称。
vendorSub	| 浏览器供应商的版本。
webkitPersistentStorage	| 永久储存
webkitTemporaryStorage	| 临时储存