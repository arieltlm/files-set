# Webpack 5相对于4变化

## 1.rules中使用oneOf

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



## 2.缓存

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

## 3.Tree Shaking(树摇)

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

