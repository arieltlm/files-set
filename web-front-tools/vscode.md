# vscode使用

## 1.使用技巧

### 1.1 此临时打开的文件，有.git文件，但是不想它显示git未提交状态：

设置中搜索git,将Git:Enable在本工作区勾选去掉

### 1.2 使用项目中配置的stylelint来规范scss时，配置vscode:

```json
{
    "editor.formatOnSave": false,
    "javascript.format.enable": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.tslint": true,
        "source.fixAll.eslint": true,
        "source.fixAll.stylelint": true,
    },
}
```

需要禁用premitter和beauty等

### 1.3 遇到问题使用ts之后，jsx中的代码格式有问题时，一按保存就弹出是否要配置信任域eslint:

解决办法：

```json
"[javascriptreact]": {
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.fixAll.tslint": true,
        "source.fixAll.stylelint": false,
    }
},
```

### 1.4 ToDo插件可配置参数

如下的配置可使得注释 `//`后面跟TODO或者FIXME就可以加上颜色并显示在左侧列表中

```json
"todo-tree.general.tags": [
    "TODO:",
    "FIXME:"
],
"todo-tree.highlights.defaultHighlight": {
    "gutterIcon": true
},
"todo-tree.highlights.customHighlight": {
    "TODO:": {
        "foreground": "#fff",
        "background": "#ffbd2a",
        "iconColour": "#ffbd2a"
    },
    "FIXME:": {
        "foreground": "#fff",
        "background": "#f06292",
        "icon": "flame",
        "iconColour": "#f06292"
    }
},
```
## 1.5 在终端中可以使用`code .`vscode文件

VSCode 提供 code 命令直接从命令行中打开文件目录，此时需要先安装 code 命令。

1、首先打开 VSCode

2、使用 `command + shift + p `(注意window 下使用 `ctrl + shift + p` ) 然后搜索`code`，选择 install 'code' command in path。

## 1.6 vscode中快捷键

- ctrl+shift+n  打开当前活动区的终端
- 打开终端快捷键：ctrl+~
- 起服务：live-server
- cmd+shift+p 选择prettier selection格式化代码
- Command+z撤销
- command+shift+z 恢复撤销
- cmd+p 可以直接搜索文件名
- cmd+r可以搜索最近的打开过的文件
- cmd+r 输入？ 有很多操作可查看
- shift+alt+f：代码格式化ctrl+shift+p后输入format code
- ctrl+G：跳到指定行
- fn+f12可以跳到函数定义处，或在函数名上右键可跳到定义处
- cmd+鼠标可以选中多个位置
- 点三下可以选中整行
- cmd+上 跳到最上面
- ctrl+shift+L 变小写
- 在终端中输入code . 可以快速在vscode中打开当前的文件

- shift+option + 左右键 可以在一个单词开始处跳到单词结尾处 选中
- shift+cmd + 左右键 可以在一行开始处跳到行尾处 选中

## 2 vscode遇坑

### 2.1 suggestionActions——eslint相关

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

### 2.2 tsconfigRootDir——eslint相关

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
## 2.3 vscode 扩展宿主意外终止 + command 'emmet.expandAbbreviation' not found

根据网络搜索结果猜测是插件冲突。

打开插件列表，有一个插件显示需要重新加载点击后，就可以了；

或者可以尝试一一禁用插件，找到问题再启用；一般在禁用启用之间就恢复好了；

