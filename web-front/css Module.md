# css Module

[CSS Module](https://segmentfault.com/a/1190000015738767) 

[Css Modules用法教程](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)

**webpack配置**

加上 modules 即为启用，localIdentName 是设置生成样式的命名规则

```js
test: /\.less$/,
  use: [
    'style-loader',
    {
      loader: 'css-loader',
      options: {
        modules: true,
        localIdentName: '[name]__[local]-[hash:base64:5]',
      },
    },
  ],
},
 
// 或者下面的写法
module: {
  loaders: [
    // ...
    {
      test: /\.css$/,
      loader: "style-loader!css-loader?modules&localIdentName=[path][name]---[local]---[hash:base64:5]"
    },
  ]
}
```

通过这些简单的处理，CSS Modules 实现了以下几点：

- 所有样式都是 local 的，解决了命名冲突和全局污染问题
- class 名生成规则配置灵活，可以此来压缩 class 名
- 只需引用组件的 JS 就能搞定组件所有的 JS 和 CSS
- 依然是 CSS，几乎 0 学习成本

**样式默认局部**:

使用了 CSS Modules 后，就相当于给每个 class 名外加加了一个 :local，以此来实现样式的局部化，如果你想切换到全局模式，使用对应的 :global。

```css
.normal {
  color: green;
}

/* 以上与下面等价 */
:local(.normal) {
  color: green; 
}

/* 定义全局样式 */
:global(.btn) {
  color: red;
}

/* 定义多个全局样式 */
:global {
  .link {
    color: green;
  }m
  .box {
    color: yellow;
  }
}
```

**Class 的组合**--继承:

```css
.className {
  background-color: blue;
}

.title {
  composes: className;
  color: red;
}
```

