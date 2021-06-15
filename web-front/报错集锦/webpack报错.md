# 1.启动webpack-dev-server时报错——Error: Cannot find module 'webpack-cli/bin/config-yargs'

原因：webpack 和webpack-dev-server版本不兼容

我相继安装的包：

>   "webpack": "5.39.0",
>    "webpack-cli": "4.7.2",
>    "webpack-dev-server": "3.11.2"

webpack5.x和webpack-dev-server3.x不应该使用`webpack-dev-server`启动项目，而应该使用`webpack serve --open`启动项目