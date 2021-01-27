# vscode上遇坑记录

[toc]

# 1.eslint相关

## 1).suggestionActions

```js
// .eslintrc.js
module.exports = {
  //...
}
```

module底下一直报红，错误信息为：`File is a CommonJS module; it may be converted to an ES6 module.ts(80001) Parsing error: "parserOptions.project" has been set for @typescript-eslint/parser. The file does not match your project config: .eslintrc.js. The file must be included in at least o`

[stackoverflow解决办法](https://stackoverflow.com/questions/49582984/how-do-i-disable-js-file-is-a-commonjs-module-it-may-be-converted-to-an-es6):

```js
 // Enable/disable suggestion diagnostics for JavaScript files in the editor.
// 在编辑器中启用/禁用JavaScript文件的建议诊断。(干啥的？)
 "javascript.suggestionActions.enabled": true

```

或者

```js
 // Enable/disable suggestion diagnostics for TypeScript files in the editor.
  "typescript.suggestionActions.enabled": true,
```

## 2).tsconfigRootDir

多个文件第一个单词下报红，报错信息为：

`Parsing error: Cannot read file '.../tsconfig.json'.eslint`

原因是其读取的tscofig.json目录不对，修改方法[stackoverflow](https://stackoverflow.com/questions/64933543/parsing-error-cannot-read-file-tsconfig-json-eslint)：

```js
// .eslintrc.js中增加tsconfigRootDir: __dirname当前目录
module.exports = {
  // ...
  parserOptions: {
    project: "tsconfig.json",
    tsconfigRootDir: __dirname,
    sourceType: "module",
  },
  // ...
}
```



