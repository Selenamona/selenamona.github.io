---
layout:     post
title:      vue插件编写
summary: 四种常见vue插件编写方法，以及插件使用
categories: [Plugin, Vue]
technique: true
---

### 插件分类

vue插件的编写方法一般分为4类，如上图所示。主要注册与绑定机制如下：

1. 添加全局方法或属性 

    ```js
    export default {
        install(Vue, options) {
            Vue.myGlobalMethod = function () { 
            // 逻辑...
            }
        }
    }
    ```

2. 添加全局资源：指令/过滤器/过渡等  

    ```js
    export default {
        install(Vue, options) {
            Vue.directive('my-directive', { 
                bind (el, binding, vnode, oldVnode) {
                    // 逻辑...
                }
                ...
            })
        }
    }
    ```

3. 通过全局 mixin方法添加一些组件选项 

    ```js
    export default {
        install(Vue, options) {
            Vue.mixin({
                created: function () { 
                    // 逻辑...
                }
                ...
            })
        }
    }
    ```

4. 添加实例方法，通过把它们添加到 Vue.prototype 上实现

    ```js
    export default {
        install(Vue, options) {
            Vue.prototype.$myMethod = function (options) { 
                // 逻辑...
            }
        }
    }
    ```

install是注册插件主要调用的方法，包含了两个参数（Vue实例和自定义配置属性options），我们可以将以上代码存储到plugins.js中。


### 插件使用

在plugins.js中仅仅编写了一个插件的空壳子，假如现在需要全局注册该插件，可以在入口文件，比如main.js中注册：

```js
import Vue from 'vue'
import MyPlugin from './plugins/plugins.js'
Vue.use(MyPlugin);
```

通过全局方法 Vue.use() 即可使用该插件，其自动会调用install方法。Vue.use会自动阻止注册相同插件多次，届时只会注册一次该插件。





