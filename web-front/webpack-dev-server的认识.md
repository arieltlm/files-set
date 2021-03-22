[配置webpack-dev-server](https://blog.csdn.net/weixin_43684713/article/details/92839419):

webpack-dev-server是webpack官方提供的一个小型Express服务器。使用它可以为webpack打包生成的资源文件提供web服务。

**webpack-dev-server 主要提供两个功能：**

1.为静态文件提供web服务

2.自动刷新和热替换(HMR)

自动刷新指当修改代码时webpack会进行自动编译，更新网页内容

热替换指运行时更新各种模块，即局部刷新

[WEBPACK DEV SERVER](https://www.jianshu.com/p/941bfaf13be1/):

**第一** webpack-dev-server有两种模式支持自动刷新——iframe模式和inline模式

**第二** webpac-dev-server支持Hot Module Replacement，即模块热替换，在前端代码变动的时候无需整个刷新页面，只把变化的部分替换掉。使用HMR功能也有两种方式：命令行方式和Node.js API

【tlm时刻】：此处两次都提到了命令行方式和Node.js API，似乎都见过，但是具体什么区别呢？什么使用场景呢？

