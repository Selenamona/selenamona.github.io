---
layout:     post
title:      vue 项目 webpack 配置
summary: 
categories: Vue
technique: true
---

```
npm install -g vue-cli
vue init webpack <your project>
cd <your project>
npm install
npm run dev
```

## | 配置路径 @

```js
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: { 
      '@': resolve('src'),  
      'assets': resolve('src/assets'),
      'components': resolve('src/components'), 
    }
  },
```


**Vue 项目结构：**

├── build --------------------------------- webpack相关配置文件    
│   ├── build.js --------------------------webpack打包配置文件   
│   ├── check-versions.js ------------------------------ 检查npm/nodejs版本   
│   ├── dev-client.js ---------------------------------- 设置环境   
│   ├── dev-server.js ---------------------------------- 创建express服务器/配置中间件/启动可热重载的服务器/用于开发项目  
│   ├── utils.js --------------------------------------- 配置资源路径，配置css加载器   
│   ├── vue-loader.conf.js ----------------------------- 配置css加载器等   
│   ├── webpack.base.conf.js --------------------------- webpack基本配置   
│   ├── webpack.dev.conf.js ---------------------------- 用于开发的webpack设置   
│   ├── webpack.prod.conf.js --------------------------- 用于打包的webpack设置   
├── config ---------------------------------- 配置文件   
├── node_modules ---------------------------- 存放依赖的目录   
├── src ------------------------------------- 源码   
│   ├── assets ------------------------------ 静态文件   
│   ├── components -------------------------- 组件    
│   ├── main.js ----------------------------- 主js   
│   ├── App.vue ----------------------------- 项目入口组件   
│   ├── router ------------------------------ 路由   
├── package.json ---------------------------- node配置文件   
├── .babelrc--------------------------------- babel配置文件   
├── .editorconfig---------------------------- 编辑器配置   
├── .gitignore------------------------------- 配置git可忽略的文件   


## | 主要配置文件

```json
 "scripts": {
        "dev": "node build/dev-server.js",
        "start": "node build/dev-server.js",
        "build": "node build/build.js"
  }
```

当执行 npm run dev 时，执行的是 dev-server.js   
当执行 npm run build 时，执行的是 build.js  

#### dev-server.js

利用 express 实现了一个本地开发的服务

- 引入配置文件     
- 引入相关插件     
- 创建 express 实例     
- 配置 webpack-dev-middleware 和 webpack-hot-middleware     
- 配置静态资源路径，并挂到 express 服务上     
- 启动服务器，并判断是否自动打开默认浏览器     
- 监听端口     

```javascript
require('./check-versions')() // 检查node和npm的版本
var config = require('../config') //引入相关配置 config/index.js
if (!process.env.NODE_ENV) { // 检查Node的环境变量，如果没有则使用配置文件中设置的环境
  process.env.NODE_ENV = JSON.parse(config.dev.env.NODE_ENV)
}
 
//opn -- A better node-open. Opens stuff like websites, files, executables. Cross-platform.
//这里用来打开默认浏览器，打开dev-server监听的端口
var opn = require('opn') //一个可以强制打开浏览器并跳转到指定url的插件
var path = require('path')//使用Node自带的文件路径工具
var express = require('express')
var webpack = require('webpack')
//express中间件，用于http请求代理到其他服务器
var proxyMiddleware = require('http-proxy-middleware') 
//判断当前环境，选择导入的webpack配置
var webpackConfig = process.env.NODE_ENV === 'testing'
  ? require('./webpack.prod.conf')
  : require('./webpack.dev.conf')

// default port where dev server listens for incoming traffic

var port = process.env.PORT || config.dev.port //默认的dev-server的监听端口
// automatically open browser, if not set will be false
var autoOpenBrowser = !!config.dev.autoOpenBrowser//是否自动打开浏览器，默认是false
// Define HTTP proxies to your custom API backend
// https://github.com/chimurai/http-proxy-middleware
//定义http代理到你的自定义的API后端
var proxyTable = config.dev.proxyTable
var app = express()   //使用express启动一个服务
var compiler = webpack(webpackConfig) //启动webpack进行编译

//使用compiler相应的文件进行编译和绑定，编译后的内容将存放在内存中
var devMiddleware = require('webpack-dev-middleware')(compiler, {
  publicPath: webpackConfig.output.publicPath,
  quiet: true
})
//用于实现热重载
var hotMiddleware = require('webpack-hot-middleware')(compiler, {
  log: false,
  heartbeat: 2000
})
// force page reload when html-webpack-plugin template changes
//当html-webpack-plugin提交页面之后，使用热重载强制页面重载
compiler.plugin('compilation', function (compilation) {
  compilation.plugin('html-webpack-plugin-after-emit', function (data, cb) {
    hotMiddleware.publish({ action: 'reload' })
    cb()
  })
})

// proxy api requests
//将 proxyTable 中的请求配置挂在到启动的 express 服务上
Object.keys(proxyTable).forEach(function (context) {
  var options = proxyTable[context]
  if (typeof options === 'string') {
    options = { target: options }
  }
  app.use(proxyMiddleware(options.filter || context, options))
})

// handle fallback for HTML5 history API //一个解决单页的重定向错误的插件
//使用 connect-history-api-fallback 匹配资源，如果不匹配就可以重定向到指定地址
// https://www.npmjs.com/package/connect-history-api-fallback
app.use(require('connect-history-api-fallback')())

// serve webpack bundle output 
app.use(devMiddleware) //将暂存到内存中的 webpack 编译后的文件挂在到 express 服务上

// enable hot-reload and state-preserving
// compilation error display
app.use(hotMiddleware)  // 使用热重载中间件

// serve pure static assets //配置静态资源路径
var staticPath = path.posix.join(config.dev.assetsPublicPath, config.dev.assetsSubDirectory)
app.use(staticPath, express.static('./static'))//将静态文件挂到express服务器上
var uri = 'http://localhost:' + port //设置应用的url

var _resolve
var readyPromise = new Promise(resolve => {
  _resolve = resolve
})

console.log('> Starting dev server...')
devMiddleware.waitUntilValid(() => {  //devMiddleware valid之后，启动服务
  console.log('> Listening at ' + uri + '\n')
  // when env is testing, don't need open it
  //如果不是测试环境，自动打开浏览器并跳到我们的开发地址
  if (autoOpenBrowser && process.env.NODE_ENV !== 'testing') {
    opn(uri)
  }
  _resolve()
})
var server = app.listen(port)  //监听配置的端口

module.exports = {
  ready: readyPromise,
  close: () => {
    server.close()
  }
}
```

#### build.js

执行 npm run build 时执行的 js 脚本。主要是加载生产环境下的 webpack 配置，并根据 webpack 配置重新构建出生产环境下使用的压缩项目，最终资源文件会生成到根目录下 dist 文件中。

- webpack 编译
- 输出信息

```javascript
require('./check-versions')()// 检查 Node 和 npm 版本
process.env.NODE_ENV = 'production'//指定生产环境
var ora = require('ora')//控制台loading动画
var rm = require('rimraf')
var path = require('path')
var chalk = require('chalk')//控制台高亮显示的插件
var webpack = require('webpack')
var config = require('../config')
var webpackConfig = require('./webpack.prod.conf')
//在控制台输出building for production...
var spinner = ora('building for production...')
//开始loading动画
spinner.start()
//获取输出文件路径
rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
  if (err) throw err//如果回调函数出现错误就抛出异常
  //开始webpack编译  
  webpack(webpackConfig, function (err, stats) {
    //停止loading动画
    spinner.stop()
    //如果错误抛出错误
    if (err) throw err//编译失败就抛出异常
    //完成输出相关信息 
    process.stdout.write(stats.toString({//标准输出流
      colors: true,
      modules: false,
      children: false,
      chunks: false,
      chunkModules: false
    }) + '\n\n')

    console.log(chalk.cyan('  Build complete.\n'))
    console.log(chalk.yellow(
      '  Tip: built files are meant to be served over an HTTP server.\n' +
      '  Opening index.html over file:// won\'t work.\n'
    ))
  })
})
```

#### check-version.js

在 build 之前的一个 check 步骤，作为构建生产项目包之前的一个校验处理。

#### dev-client.js

加载了热重载插件，并在监听到事件的 action 变化为 reload 时，实现网页 reload。

#### utils.js

utils.js 主要是对外给响应的 webpack 配置使用的工具方法，包含了对外提供静态资源 asset 路径，对外提供各种 loaders 的配置，如cssLoaders, css/postcss/less/sass/scss/stylus/styl 等预处理或后处理器配置等。

#### webpack.base.conf.js

- 配置编译入口和输出路径
- 模块 resolve 的规则
- 配置不同类型模块的处理规则


```javascript
var path = require('path')
var utils = require('./utils') //封装了一些方法的工具
var config = require('../config')//使用 config/index.js
var vueLoaderConfig = require('./vue-loader.conf')
function resolve (dir) {//绝对路径
  return path.join(__dirname, '..', dir)
}
module.exports = {
  entry: { //编译文件入口
    app: './src/main.js'
  },
  output: {
    path: config.build.assetsRoot,//使用config/index.js中build的assetsRoot作为输出根路径
    filename: '[name].js',//输出的文件名
    // webpack编译输出的发布路径
    publicPath: process.env.NODE_ENV === 'production'
      ? config.build.assetsPublicPath
      : config.dev.assetsPublicPath
  },
  //模块resolve的规则（文件解析）
  resolve: {
    extensions: ['.js', '.vue', '.json'],  // 自动补全的扩展名,能够使用户在引入模块时不带扩展 
    alias: { // 默认路径代理，例如 import Vue from 'vue$'，会自动到'vue/dist/vue.esm.js'中寻找
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src')
    }
  },
  //配置不同类型模块的处理规则
  //用于定义不同文件应使用何种loaders（加载器）进行加载解析
  module: {
    rules: [
    // src和test文件夹下的.js和.vue文件使用eslint-loader
      {
        test: /\.(js|vue)$/,
        loader: 'eslint-loader',
        enforce: 'pre',
        include: [resolve('src'), resolve('test')],
        options: {
          formatter: require('eslint-friendly-formatter')
        }
      },
      //所有的.vue文件使用vue-loader
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: vueLoaderConfig
      },
      //src和test下的.js文件使用babel-loader
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: [resolve('src'), resolve('test')]
      },
      //所有的图片文件使用url-loader
      {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
      },
      //所有的音频文件使用url-loader
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('media/[name].[hash:7].[ext]')
        }
      },
      //所有的字体文件使用url-loader
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
        }
      }
    ]
  }
}
```

#### webpack.dev.conf.js

- 合并基础的 webpack 配置
- 使用 styleLoaders
- 配置 Source Maps
- 配置 webpack 插件

```javascript
var utils = require('./utils')
var webpack = require('webpack')
var config = require('../config')
var merge = require('webpack-merge')
var baseWebpackConfig = require('./webpack.base.conf')
//生成html文件并自动注入依赖文件的插件， script & link
var HtmlWebpackPlugin = require('html-webpack-plugin')
//一个输出webpack警告，错误的插件
var FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')

// add hot-reload related code to entry chunks
//添加热重载相关的代码到entry chunks
Object.keys(baseWebpackConfig.entry).forEach(function (name) {
  baseWebpackConfig.entry[name] = ['./build/dev-client'].concat(baseWebpackConfig.entry[name])
})
//合并webpack.base.conf
module.exports = merge(baseWebpackConfig, {
  module: {
    //使用styleLoaders处理样式文件
    rules: utils.styleLoaders({ sourceMap: config.dev.cssSourceMap })
  },
  // cheap-module-eval-source-map is faster for development
  //配置Source Maps

  devtool: '#cheap-module-eval-source-map',
  //配置webpack插件
  plugins: [
    new webpack.DefinePlugin({
      'process.env': config.dev.env
    }),
    // https://github.com/glenjamin/webpack-hot-middleware#installation--usage
    new webpack.HotModuleReplacementPlugin(), //热重载插件
    //在编译出现错误时，使用 NoEmitOnErrorsPlugin 来跳过输出阶段。这样可以确保输出资源不会包含错误。
    new webpack.NoEmitOnErrorsPlugin(),
    // https://github.com/ampedandwired/html-webpack-plugin
    new HtmlWebpackPlugin({  //自动生成html文件
      filename: 'index.html', //生成的文件名
      template: 'index.html',
      inject: true
    }),
    new FriendlyErrorsPlugin()  //友好的错误提示
  ]
})
```

**配置Source Maps**

source-map 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source
map，但是它会减慢打包文件的构建速度；
cheap-module-source-map
在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
eval-source-map 使用eval打包源文件模块，在同一个文件中生成干净的完整的source
map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项；
cheap-module-eval-source-map 这是在打包文件时最快的生成source map的方法，生成的Source
Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

#### webpack.prod.conf.js

- 合并基础的 webpack 配置
- 配置 webpack 的输出
- 配置 webpack 插件
- 配置 gzip 模式
- 配置 webpack-bundle-analyzer，分析打包后生成的文件结构


```javascript
var path = require('path')
var utils = require('./utils')
var webpack = require('webpack')
var config = require('../config')
var merge = require('webpack-merge')
var baseWebpackConfig = require('./webpack.base.conf')
//可以将单个文件或整个目录复制到构建目录中
var CopyWebpackPlugin = require('copy-webpack-plugin')
// 一个可以插入 html 并且创建新的 .html 文件的插件
var HtmlWebpackPlugin = require('html-webpack-plugin')
// 抽取css，js文件,与webpack输出的bundle分离
var ExtractTextPlugin = require('extract-text-webpack-plugin')
var OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin')

var env = process.env.NODE_ENV === 'testing'
  ? require('../config/test.env')
  : config.build.env
//合并webpack.base.conf
var webpackConfig = merge(baseWebpackConfig, {
  module: {
    rules: utils.styleLoaders({
      sourceMap: config.build.productionSourceMap,
      extract: true
    })
  },
   //通过判断配置值，判断是否生产source-map
  devtool: config.build.productionSourceMap ? '#source-map' : false,
  output: { 
    path: config.build.assetsRoot,//指定生产环境输出路径
    //编译输出带hash的文件名,可以指定hash长度(chunkhash:6)
    filename: utils.assetsPath('js/[name].[chunkhash].js'),
    // 没有指定输出名的文件输出的文件名
    chunkFilename: utils.assetsPath('js/[id].[chunkhash].js')
  },
  //相关插件
  plugins: [
    // http://vuejs.github.io/vue-loader/en/workflow/production.html
    new webpack.DefinePlugin({
      'process.env': env
    }),
    //压缩js插件
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      },
      sourceMap: true
    }),
    // extract css into its own file
    //从bundle中抽取css文件
    new ExtractTextPlugin({
      filename: utils.assetsPath('css/[name].[contenthash].css')
    }),
    // Compress extracted CSS. We are using this plugin so that possible
    // duplicated CSS from different components can be deduped.
    //压缩抽取的css文件
    new OptimizeCSSPlugin({
      cssProcessorOptions: {
        safe: true
      }
    }),
    // generate dist index.html with correct asset hash for caching.
    // you can customize output by editing /index.html
    // see https://github.com/ampedandwired/html-webpack-plugin
    //用于生成dist/index.html，加入hash用于缓存。hash不改变不进行更新
    new HtmlWebpackPlugin({
      filename: process.env.NODE_ENV === 'testing'
        ? 'index.html'
        : config.build.index,
      template: 'index.html',
      inject: true,// 是否注入 html
      minify: {// 压缩的方式
        removeComments: true,//移除带html的注释
        collapseWhitespace: true,//移除空格
        removeAttributeQuotes: true//移除属性的引号
        // more options:
        // https://github.com/kangax/html-minifier#options-quick-reference
      },
      // necessary to consistently work with multiple chunks via CommonsChunkPlugin
      chunksSortMode: 'dependency'//资源按照依赖关系去插入
    }),
    // split vendor js into its own file
    //分离第三方js到单独的文件中
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      minChunks: function (module, count) {
        // any required modules inside node_modules are extracted to vendor
        return (
          module.resource &&
          /\.js$/.test(module.resource) &&
          module.resource.indexOf(
            path.join(__dirname, '../node_modules')
          ) === 0
        )
      }
    }),
    // extract webpack runtime and module manifest to its own file in order to
    // prevent vendor hash from being updated whenever app bundle is updated
    new webpack.optimize.CommonsChunkPlugin({
      name: 'manifest',
      chunks: ['vendor']
    }),
    // copy custom static assets
    new CopyWebpackPlugin([
      {
        from: path.resolve(__dirname, '../static'),
        to: config.build.assetsSubDirectory,
        ignore: ['.*']
      }
    ])
  ]
})
// 配置gzip模式
if (config.build.productionGzip) {
  var CompressionWebpackPlugin = require('compression-webpack-plugin')

  webpackConfig.plugins.push(
    new CompressionWebpackPlugin({
      asset: '[path].gz[query]',//目标资源名称
      algorithm: 'gzip',,//压缩方式
      test: new RegExp(
        '\\.(' +
        config.build.productionGzipExtensions.join('|') +
        ')$'
      ),//所有匹配该正则的资源都会被处理。默认值是全部资源。
      threshold: 10240,//只有大小大于该值的资源会被处理。单位是 bytes。默认值是 0。
      minRatio: 0.8//只有压缩率小于这个值的资源才会被处理。默认值是 0.8。
    })
  )
}
//配置webpack-bundle-analyzer，分析打包后生成的文件结构
if (config.build.bundleAnalyzerReport) {
  var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
  webpackConfig.plugins.push(new BundleAnalyzerPlugin())
}

module.exports = webpackConfig
```

#### config/index.js

```javascript
var path = require('path')
module.exports = {
  build: {//生产环境配置
    env: require('./prod.env'),  // 使用 config/prod.env.js 中定义的编译环境
    //输入的index.html的路径
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist'),// 编译输出的静态资源根路径
    assetsSubDirectory: 'static',// 编译输出的二级目录
    //编译发布上线路径的根目录 可配置为资源服务器域名或 CDN 域名
    assetsPublicPath: '/', 
    //是否使用SourceMap
    productionSourceMap: true,
    // Gzip off by default as many popular static hosts such as
    // Surge or Netlify already gzip all static assets for you.
    // Before setting to `true`, make sure to:
    // npm install --save-dev compression-webpack-plugin
    // 是否开启Gzip
    productionGzip: false,
    //Gzip的压缩文件类型
    productionGzipExtensions: ['js', 'css'],
    // Run the build command with an extra argument to
    // View the bundle analyzer report after build finishes:
    // `npm run build --report`
    // Set to `true` or `false` to always turn it on or off
    bundleAnalyzerReport: process.env.npm_config_report
  },
  //开发环境的配置
  dev: {
    env: require('./dev.env'),
    port: 8080, // 运行测试页面的端口
    autoOpenBrowser: true, //是否自动打开浏览器
    assetsSubDirectory: 'static',// 编译输出的二级目录
    assetsPublicPath: '/',
    proxyTable: {},
    // CSS Sourcemaps off by default because relative paths are "buggy"
    // with this option, according to the CSS-Loader README
    // (https://github.com/webpack/css-loader#sourcemaps)
    // In our experience, they generally work as expected,
    // just be aware of this issue when enabling this option.
    cssSourceMap: false// 是否开启 cssSourceMap
  }
}
```


## 参考链接
[WebpackOptions](https://github.com/webpack/webpack/blob/master/schemas/WebpackOptions.json)

