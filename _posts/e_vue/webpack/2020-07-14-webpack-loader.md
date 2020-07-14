---
layout: post
title: Webpack 之 loader 配置详解
summary:
categories: Vue
technique: true
---

## | 简介

1. loader 是在 Webpack 中作用于指定格式的资源文件并将其按照一定格式转换输出。例如：less-loader 将 less 文件转换为 css 文件输出。

2. 单一职责，一个 Loader 只做一件事情，正因为职责越单一，所以 Loaders 的组合性强，可配置性好。

3. loader 支持链式调用，上一个 loader 的处理结果可以传给下一个 loader 接着处理，上一个 Loader 的参数 options 可以传递给下一个 loader，直到最后一个 loader，返回 Webpack 所期望的 JavaScript。

## | 规则配置 Rules

```javascript
rules: [ { test: /\.css$/, use: ['style-loader', 'css-loader']}],
```

**_Rule.test_** 筛选资源，符合条件的资源让这项规则中的 loader 处理，值可以是字符串、正则表达式、函数、数组。

1. 值为字符串时，可以为资源所在目录绝对路径 、资源的绝对路径。如：

```javascript
test: path.resolve(__dirname, 'src/css');
```

2. 值为函数时，接收的参数为资源的绝对路径。返回 true 表示该资源可以交给 user 选项里面的 loader 处理一下。如：

```javascript
test: function (path) { return path.indexOf('.css') > -1 }
```

3. 值为数组时，数组每一项可以为字符串、正则表达式、函数，只要符合数组中任一项条件的资源就可以交给 user 选项里面的 loader 处理一下。如：

```javascript
test: [/\.css$/, path.resolve(__dirname, 'src/css')];
```

**_Rule.include_** 符合条件的资源让这项规则中的 loader 处理，用法和 Rule.test 一样。

**_Rule.exclude_** 符合条件的资源要排除在外，不能让这项规则中的 loader 处理，用法和 Rule.test 一样。

**_Rule.issuer_** 用法和 Rule.test 一样，但是要注意是匹配引入资源的文件路径，如在 main.js 中引入 css/index.css

```javascript
// Rule.issuer 和 Rule.test、Rule.include 、Rule.exclude同时使用时候，也是“与”的关系。
issuer: /\main\.js$/,
//issuer: path.resolve(__dirname, 'main.js'),
//issuer: [/\main\.js$/ , path.resolve(__dirname, 'main.js')],
//issuer:function (content) {
    //return content.indexOf('main.js') > -1
//}
```

**_Rule.resource_** 筛选资源，符合条件的资源让这项规则中的 loader 处理。但配置 resource 选项后，test、include、exclude 选项不能使用。issuer 选项不生效。
resource 选项中有以下子选项：

1. test 选项，用法和 Rule.test 一样。
2. exclude 选项，用法和 Rule.exclude 一样。
3. include 选项，用法和 Rule.include 一样。
4. not 选项，值为数组，数组每一项可以为字符串、正则表达式、函数，只要符合数组中任一项条件的资源就不能交给 user 选项里面的 loader 处理一下。
5. and 选项，值为数组，数组每一项可以为字符串、正则表达式、函数，必须符合数组中每一项条件的资源才能交给 user 选项里面的 loader 处理一下。
6. or 选项，值为数组，数组每一项可以为字符串、正则表达式、函数，只要符合数组中任一项条件的资源就可以交给 user 选项里面的 loader 处理一下。

```javascript
rules: [
    {
        resource:{
            test:/\.css$/,
            include: path.resolve(__dirname, 'src/css'),
            exclude: path.resolve(__dirname, 'node_modules'),
        },
        use: ['style-loader', 'css-loader']
    },
],
```

**_Rule.resourceQuery_** 匹配资源引入路径上从问号开始的部分。例`import './ass/main.css?inline'`，要匹配?inline：

```javascript
resourceQuery:/inline/,
```

## | 规则配置 loads

**_Rule.use_** `·use: ['style-loader']`其实是 `use: [ { loader: 'style-loader'} ]`的简写。

use 的值还可以是函数，返回一个数组，参数为 info，info 中有以下内容

- compiler：当前 webpack 的编译器（可以是 undefined 值）。
- issuer：引入被处理资源的所在文件的绝对路径。
- realResource：被处理资源的绝对路径。
- resource：被处理资源的绝对路径，它常常与 realResource 替代，只有当资源名称被请求字符串中的!=!覆盖时才不近似。
- resourceQuery：被处理资源的绝对路径中？后面的部分。

```javascript
use: (info) => {
  return ['style-loader', { loader: 'css-loader' }];
};
```

**_Rule.loader_** `loader: 'css-loader'` 是 `use: [ { loader: 'css-loader'} ]`的简写。

```javascript
rules: [{ test: /\.css$/, loader: 'css-loader' }];
```

**_Rule.oneOf_** 当规则匹配时，只使用第一个匹配规则。例如说要处理 css 文件资源时，one.css 要用 url-loader 处理，two.css 要用 file-loader 处理。可以用 Rule.oneOf 来配置

```javascript
rules: [
  {
    test: /\.css$/,
    oneOf: [
      {
        resourceQuery: /one/, // one.css?one
        //test: /one\.css/,
        use: 'url-loader',
      },
      {
        resourceQuery: /two/, // two.css?two
        //test: /one\.css/,
        use: 'file-loader',
      },
    ],
  },
];
```

## | loader 的执行顺序

从右到左，从下到上执行。换句话来说，就是后写的先执行，跟栈一样后进先出，例如：

```javascript
use: ['style-loader', 'css-loader', 'less-loader'];
// 以上配置中，less-loader先执行，再执行css-loader，最后执行style-loader
```

**_控制 loader 的执行顺序_** 用 Rule.enforce 来控制，其有两个值：pre：优先执行；post：最后执行

```javascript
rules: [
  { test: /\.less$/, loader: 'less-loader', enforce: 'pre' },
  { test: /\.less$/, loader: 'css-loader' },
  { test: /\.less$/, loader: 'style-loader', enforce: 'post' },
];
// 此时，less-loader先执行，再执行css-loader，最后执行style-loader。
```

参考链接

[Webpack 之 loader 配置详解](https://juejin.im/post/5ef21379e51d457412313f91#heading-0)
