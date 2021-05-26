[toc]

## 1. vscode 扩展宿主意外终止 + command 'emmet.expandAbbreviation' not found

根据网络搜索结果猜测是插件冲突。

打开插件列表，有一个插件显示需要重新加载点击后，就可以了；

或者可以尝试一一禁用插件，找到问题再启用；一般在禁用启用之间就恢复好了；

## 2.在终端中可以使用`code .`vscode文件

VSCode 提供 code 命令直接从命令行中打开文件目录，此时需要先安装 code 命令。

1、首先打开 VSCode

2、使用 `command + shift + p `(注意window 下使用 `ctrl + shift + p` ) 然后搜索`code`，选择 install 'code' command in path。

## 3.vscode中快捷键

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

