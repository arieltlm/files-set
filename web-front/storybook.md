# storybook

[官方文档](https://www.learnstorybook.com/intro-to-storybook/react/en/get-started/)

## 安装

在react项目中使用：

```bash
# 先创建自己的项目（可使用create-react-app):
npx create-react-app taskbox

cd taskbox

# 安装Storybook:
npx -p @storybook/cli sb init
```

安装完成之后，在项目中就会出现最基本的使用，在项目根目录下有`.storybook`文件，其下有`main.js`，并同时创建例子可查看`stories`文件夹，其中有两个文件，展示了`Welcome`和`Button`组件，使用的组件在`node_modules`中`'@storybook/react/demo'`中

`package.json`中自动添加了：

```json
"scripts": {
    "storybook": "start-storybook -p 6006",
    "build-storybook": "build-storybook"
},
```

**启动**：

```bash
npm run storybook
```



# 使用

## 1.default export

每个文件都有一个`default export`，其中暴露了要在左侧显示的分组和组件信息

```javascript
default export{
  title:'Button',// 左侧显示的名字
  component:Button, // 使用的组件
  excludeStories:'',// 排除一些此文件中的内容不显示，可用正则
}
```

然后此文件下，`export`几个组件，其分组下就有几个组件列表：

![btn](/Users/tlm/Desktop/文档图片/btn.png)

![btn-views](/Users/tlm/Desktop/文档图片/btn-views.png)

## 2. action

```javascript
import { action } from '@storybook/addon-actions'
```

```js
export const Text = () => <Button onClick={action('clicked')}>Hello Button</Button>
```

action主要是在点击事件时使用，可以在story所带的action看到点击的事件对象：

![act](/Users/tlm/Desktop/文档图片/act.png)

操作可帮助您在隔离构建UI组件时验证交互。 通常，您将无法访问应用程序上下文中的功能和状态。 使用`action()`将它们存储



# `webpack.config.js`



https://github.com/chromaui/learnstorybook.com/blob/master/.storybook/webpack.config.js



