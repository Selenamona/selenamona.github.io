---
layout:     post
title:      VSCode 插件集合
summary:
categories: Guides
technique: true
---

TypeScript Importer | Types auto installer | Sort Typescript Imports | Vue TypeScript Snippets
Vue VSCode Snippets | AutoFileName | HTML Snippets | CSS Peek
Markdown PDF | Markdown Preview Github Styling | Polacode - 代码截图 | Emoji
SVG Viewer | vscode-pdf | vscode-icons | vscode-faker
cssrem | git-hidtory | Turbo Console Log


## | vscode 删除空行

1. Ctrl + F

2. 查找：`^\s*(?=\r?$)\n`

3. 替换为空


## | cssrem插件使用

1. 搜索安装插件 cssrem

2. 设置根字体，setting.json ; 改完重启

    ```json
    "cssrem.rootFontSize":100,  // 100px -> 1rem
    "cssrem.fixedDigits": 2,
    // "cssrem.autoRemovePrefixZero": 自动移除0开头的前缀，默认：true。
    ```

3. 项目中做 rem 适配

    ```javascript
    // 屏幕适配
    (function(doc, win) {
    var docEl = doc.documentElement
    var resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
    var recalc = function() {
        var clientWidth = docEl.clientWidth
        if (!clientWidth) return
        docEl.style.fontSize = 100 * (clientWidth / 375) + 'px'  // 100px -> 1rem
    }
    if (!doc.addEventListener) return
    win.addEventListener(resizeEvt, recalc, false)
    doc.addEventListener('DOMContentLoaded', recalc, false)
    })(document, window)
    ```
## | VSCode 一键生成.vue模版

1. 安装 Vetur 插件，识别 vue 文件
2. 新建代码片段： file -> preferences -> user snippets -> new Global snippets file -> 文件命名 vue.json
3. 添加自定义模板（如下）
4. "prefix": "vue"，定义快捷键；使用：输入 vue，按键盘的tab

```json
{
    "Print to console": {
        "prefix": "vue",
        "body": [
            "<!-- $1 -->",
            "<template>",
            "<div class='$2'>$5</div>",
            "</template>",
            "",
            "<script>",
            "//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）",
            "//例如：import 《组件名称》 from '《组件路径》';",
            "",
            "export default {",
            "//import引入的组件需要注入到对象中才能使用",
            "components: {},",
            "data() {",
            "//这里存放数据",
            "return {",
            "",
            "};",
            "},",
            "//监听属性 类似于data概念",
            "computed: {},",
            "//监控data中的数据变化",
            "watch: {},",
            "//方法集合",
            "methods: {",
            "",
            "},",
            "//生命周期 - 创建完成（可以访问当前this实例）",
            "created() {",
            "",
            "},",
            "//生命周期 - 挂载完成（可以访问DOM元素）",
            "mounted() {",
            "",
            "},",
            "beforeCreate() {}, //生命周期 - 创建之前",
            "beforeMount() {}, //生命周期 - 挂载之前",
            "beforeUpdate() {}, //生命周期 - 更新之前",
            "updated() {}, //生命周期 - 更新之后",
            "beforeDestroy() {}, //生命周期 - 销毁之前",
            "destroyed() {}, //生命周期 - 销毁完成",
            "activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发",
            "}",
            "</script>",
            "<style lang='scss' scoped>",
            "//@import url($3); 引入公共css类",
            "$4",
            "</style>"
        ],
        "description": "Log output to console"
    }
}
```

## | VSCode 保存文件后自动删除多余空格

files.trimTrailingWhitespace
