# Webpack 5相对于4变化

[Webpack 5 发布公告 (2020-10-10)](https://webpack.docschina.org/blog/2020-10-10-webpack-5-release/)

# 1.webpack5整体方向

这个版本的重点在于以下几点。

- 尝试用持久性缓存来提高构建性能。
- 尝试用更好的算法和默认值来改进长期缓存。
- 尝试用更好的 Tree Shaking 和代码生成来改善包大小。
- 尝试改善与网络平台的兼容性。
- 尝试在不引入任何破坏性变化的情况下，清理那些在实现 v4 功能时处于奇怪状态的内部结构。
- 试图通过现在引入突破性的变化来为未来的功能做准备，使其能够尽可能长时间地保持在 v5 版本上。

## 2.rules中使用oneOf

默认情况下，文件会去匹配rules下面的每一个规则，即使已经匹配到某个规则了也会继续向下匹配。而如果将规则放在 oneOf 属性中，则一旦匹配到某个规则后，就停止匹配了

```js
rules:[
    {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
    },
    {
        //  以下loader一种文件只会匹配一个 
        oneOf: [
            // 不能有两个配置处理同一种类型文件，如果有，另外一个规则要放到外面。
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: [
                    {
                        loader: "babel-loader",
                    },
                ],
            },
            {
                test: /\.css$/,
                use: [
                    "style-loader",
                    "css-loader",
                ],
            },
        ],
    },
]
```



## 3.缓存

在编译打包时可对文件做缓存，有两种方式，一种是解析文件的loader自身带有缓存功能（如babel-loader,vue-loader），第二种就是使用专门的loader（cache-loader）。
 开启缓存后，对于未改动的文件，webpack直接从缓存中读取而不用再次编译，大大加快构建速度。

```js
{
    test: /\.js$/,
    use: [
        //使用cache-loader，放在babel-loader前可对babel编译后的js文件做缓存。
        "cache-loader",
        {
            loader: "babel-loader",
            options: {
                presets: [
                    [
                        "@babel/preset-env",// 预设：指示babel做怎么样的兼容处理 
                    ]
                ],
                // 开启babel缓存，第二次构建时，会读取之前的缓存。
                cacheDirectory: true,
            }
        }

}
```

webpack5的长效缓存很有用

```js
module.exports = {
  cache: {
    // 1. 将缓存类型设置为文件系统
    type: 'filesystem',

    buildDependencies: {
      // 2. 将你的 config 添加为 buildDependency，以便在改变 config 时获得缓存无效
      // __filename变量获取当前文件的带有完整绝对路径的文件名
      config: [__filename],

      // 3. 如果你有其他的东西被构建依赖，你可以在这里添加它们
      // 注意，webpack、加载器和所有从你的配置中引用的模块都会被自动添加
    },
  },
};
```

[Webpack 5升级内容](https://blog.csdn.net/sendudu/article/details/109712699)

[Webpack5更新指南](https://www.jianshu.com/p/4e810ca6c132)

## 4.Tree Shaking(树摇)



```js
// 入口文件index.js
import test from "./test.js"
console.log(test.add(2, 3));

// 测试文件test.js
const add = (x, y) => x + y
const print = (msg) => {
    console.log(msg);
}
export default { add, print }

// 最终打包输出的bundle：main.js文件
!function(){"use strict";console.log(2+3)}();
```

没有被使用，所以在最终打包中被去除。
 这一点在Webpack4中还做不到，Webpack4中只会去除从未被使用的模块。带入上面的例子，如果test在index.js文件中没有被用到，才会被Tree Shaking。之所以这样，是因为Webpack4默认认为所有文件的代码都是有副作用的。如何告知Webpack你的代码是否有副作用，可通过package.json中的sideEffects字段。



```js
// 所有文件都有副作用
{
 "sideEffects": true
}
// 所有文件都没有副作用，
{
 "sideEffects": false
}
// 只有这些文件有副作用，所有其他文件都可以 Tree Shaking，但会保留这些文件
{
 "sideEffects": [
  "./src/file1.js",
  "./src/file2.js"
 ]
}
```

比如说Webpack5.0默认设置中认为样式文件是有副作用的，所以引入样式文件虽然没有被使用（样式文件肯定是不使用的）也不会被去除，但是如果设置了sideEffects：false，就会进行Tree Shaking将代码去除。

[官方描述-tree shaking变更](https://webpack.docschina.org/blog/2020-10-10-webpack-5-release/#nested-tree-shaking)
## 5. webpack5 的静态资源不需要再加载loader了，内置好了

```js
 /* 
    Webpack5.0新增资源模块(asset module)，它是一种模块类型，允许使用资源文件（字体，图标等）而无需     配置额外 loader。支持以下四个配置
    asset/resource 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现。
    asset/inline 导出一个资源的 data URI。之前通过使用 url-loader 实现。
    asset/source 导出资源的源代码。之前通过使用 raw-loader 实现。
    asset 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资     源体积限制实现。
    */
    // Webpack4使用file-loader实现
    {
        test: /\.(eot|svg|ttf|woff|)$/,
        type: "asset/resource",
        generator: {
            // 输出文件位置以及文件名
            filename: "fonts/[name][ext]"
        },
    },
    // Webpack4使用url-loader实现
    {
        //处理图片资源
        test: /\.(jpg|png|gif|)$/,
        type: "asset",
        generator: {
            // 输出文件位置以及文件名
            filename: "images/[name][ext]"
        },
        parser: {
            dataUrlCondition: {
                maxSize: 10 * 1024 //超过10kb不转base64
            }
        }
    },
```

以上五点参考[Webpack5.0学习总结-基础篇](https://juejin.cn/post/6971743815434993671)

## 6.使用`webapck-dev-server`启动项目报错

**Error: Cannot find module 'webpack-cli/bin/config-yargs'**

原因：webpack 和webpack-dev-server版本不兼容

我相继安装的包：

>   "webpack": "5.39.0",
>    "webpack-cli": "4.7.2",
>    "webpack-dev-server": "3.11.2"

webpack5.x和webpack-dev-server3.x不应该使用`webpack-dev-server`启动项目，而应该使用`webpack serve --open`启动项目

## 7.模块热替换HRM

```js
devServer: {
  contentBase: path.resolve(__dirname, "dist"),
    hot: true,//开启HMR功能
},
  // 注意：Webpack升级到5.0后，target默认值值会根据package.json中的browserslist改变，导致devServer的自动更新失效。所以development环境下直接配置成web。
  target: "web",
```

## 8.书写loader时获取options参数

之前使用loader-utils包来获取；webpack5开始使用`this.getOptions()`

```js
// 之前使用loader-utils包来获取
const loaderUtils = require('loader-utils');
module.exports = function(source) {
  const options = loaderUtils.getOptions(this);
  // webpack5开始使用this.getOptions()
  const options5 = this.getOptions()
  return source;
};
```

## 9.merge

```js
// 4.x版本
{
  "webpack-merge": "^4.2.2"
}
// webpack.config.js
const merge = require('webpack-merge')
const defaultConfig = require('../...')
const config = merge(defaultConfig, {

})
export default config

// 5.x版本
{
  "webpack-merge": "^5.7.3"
}
const webpackMerge = require('webpack-merge')
const defaultConfig = require('../...')
const config = webpackMerge.merge(defaultConfig, {

})
export default config
```

## 10. webpack插件编写

**插件的生命周期理解变化**：

比如`html-webpack-plugin`如果安装的4.x版本的，就会报错：

> DeprecationWarning: Compilation.assets will be frozen in future, all modifications are deprecated. BREAKING CHANGE: No more changes should happen to Compilation.assets after sealing the Compilation.
> Do changes to assets earlier, e. g. in Compilation.hooks.processAssets.
> Make sure to select an appropriate stage from Compilation.PROCESS_ASSETS_STAGE_

```js
// 4.x

compiler.hooks.emit.tapAsync('HtmlWebpackPlugin',
      /**
       * Hook into the webpack emit phase
       * @param {WebpackCompilation} compilation
       * @param {(err?: Error) => void} callback
      */
      (compilation, callback) => {
  //...
})

// 5.x
compiler.hooks.thisCompilation.tap('HtmlWebpackPlugin',
    /**
       * Hook into the webpack compilation
       * @param {WebpackCompilation} compilation
      */
    (compilation) => {
      compilation.hooks.processAssets.tapAsync(
        {
          name: 'HtmlWebpackPlugin',
          stage:
          /**
           * Generate the html after minification and dev tooling is done
           */
          webpack.Compilation.PROCESS_ASSETS_STAGE_OPTIMIZE_INLINE
        },
        /**
         * Hook into the process assets hook
         * @param {WebpackCompilation} compilationAssets
         * @param {(err?: Error) => void} callback
         */
        (compilationAssets, callback) => {
          // ...
 })
```

比如我在测试时写一个很简单的插件：

```js
apply(compiler){
  // 使用emit报警告
  /*BREAKING CHANGE: No more changes should happen to Compilation.assets after sealing the Compilation.
    Do changes to assets earlier, e. g. in Compilation.hooks.processAssets.
    Make sure to select an appropriate stage from Compilation.PROCESS_ASSETS_STAGE_*.*/
  compiler.hooks.emit.tap('AddWordPlugin', (compilation) => {
    const {assets } = compilation
    const content = assets['print.bundle.js'].source()
    console.log('AddWordPlugin ', content);
    assets['print.bundle.js'] = {
      source() {
        return '"build by tlm"\r\n' + content
      },
      size(){
        return content.length;
      }
    }
  })
```

和上面的问题一致，要处理编译的资源，不能在编译生成资源到 output 目录之前处理了，assets会被封存，需要进入compliation中进行处理；

具体如何改正——正在studying



**插件写法的另一个变化：**

```js
// 4.x
/**
* 监听名称为 event-name 的事件，当 event-name 事件发生时，函数就会被执行。
* 同时函数中的 params 参数为广播事件时附带的参数。
*/
compiler.plugin('event-name',function(params) {
  
});

// 5.x
// 必须通过hook方法了
compiler.hooks.emit.tap('AddWordPlugin', (params) => {
  
})
```



## 11.node配置

> 这些选项可以配置是否 polyfill 或 mock 某些 [Node.js 全局变量](https://nodejs.org/docs/latest/api/globals.html)和模块。这可以使最初为 Node.js 环境编写的代码，在其他环境（如浏览器）中运行。
>
> 此功能由 webpack 内部的 [`NodeStuffPlugin`](https://github.com/webpack/webpack/blob/master/lib/NodeStuffPlugin.js) 插件提供。如果 target 是 "web"（默认）或 "webworker"，那么 [`NodeSourcePlugin`](https://github.com/webpack/webpack/blob/master/lib/node/NodeSourcePlugin.js) 插件也会被激活

```js
// 4.x
node: {
  	console: false,
    global: true,
    process: true,
    __filename: 'mock',
    __dirname: 'mock',
    Buffer: true,
    setImmediate: true
    dns: 'mock',
    fs: 'empty',
    path: true,
    url: false
}
// 5.x
node:{ // 5.xnode中只有这三个配置
  global: false,
  __filename: false,
  __dirname: false,
},
resolve: {
      fallback:{
           module: false,
           dgram: false,
           dns: false,
           fs:false,
           http2: false,
           net: false,
       }
}

```
## 12.入口文件运行时

入口文件描述符允许为每个入口文件指定一个 运行时代码。 当指定时，将创建一个以该名称命名的代码块，其中仅包含该条目的运行时代码。 当多个条目指定相同的运行时代码时，该块将包含所有这些入口文件的共同运行时代码。 这意味着它们可以在同一个 HTML 页面中一起使用

```js
module.exports = {
  entry: {
    app: {
      import: './app.js',
      runtime: 'app-runtime',
    },
  },
};
```

## 13.[模块联邦](https://webpack.docschina.org/concepts/module-federation/)

Webpack 5 增加了一个新的功能 "模块联邦"，它允许多个 webpack 构建一起工作。 从运行时的角度来看，多个构建的模块将表现得像一个巨大的连接模块图。 从开发者的角度来看，模块可以从指定的远程构建中导入，并以最小的限制来使用。


**可参考文章：**

[Webpack 5 升级实验](https://zhuanlan.zhihu.com/p/81122986)

