---
title: ext-webcomponents-vue
slug: ext-webcomponents-vue
author: [jianchengwang]
date: 2021-04-24
excerpt: "Extjs，是一个富客户端的前端框架，N年前挺火的，对于后端开发来说很友好，容易上手，而且提供了一系列开箱即用的UI组件，尤其是Grid组件，而且官网文档一应俱全，面面俱到，极大的提高了开发效率。"
draft: false
tags: [frontend]
---

[**Extjs**](https://www.sencha.com/products/extjs/)，是一个富客户端的前端框架，N年前挺火的，我刚实习那会，公司是使用extjs4作为前端的组件，那时候这个框架就已经有`mvc`的设计概念了，对于后端开发来说很友好，容易上手，而且提供了一系列开箱即用的UI组件，尤其是**Grid**组件，而且官网文档一应俱全，面面俱到，极大的提高了开发效率。不过后面因为开源协议，导致大量开发者流失，现在的前端也基本是Angular，React**，**Vue三足鼎立了。

不过官方Sencha公司闭门造车，自得其乐，如今Extjs也迭代到了7.0版本，而且也推出了ExtAgular，ExtReact，ExtWebComponents，拥抱如今的主流框架。

所以今天就带着对过去的缅怀，来体验一波ExtWebComponents，废话不多说，直接进入正文，

如果你懒得看正文，也可以直接怼示例项目，[ext-web-components-boilerplate-vue-cli](https://github.com/jianchengwang/todo-web/blob/master/vue/ext-web-components-boilerplate-vue-cli)

## Init Project

参照官方的文档: [getting_started_vue](https://docs.sencha.com/extwebcomponents/7.3.0/guides/getting_started/getting_started_vue.html)

**vue-cli构建项目**

```shell
// 安装vue
npm install -g @vue/cli
vue create ext-web-components-boilerplate-vue-cli
cd  ext-web-components-boilerplate-vue-cli
npm run serve
```

**引入extjs相关包**

```shell
npm install --save @sencha/ext-web-components-modern @sencha/ext @sencha/ext-modern @sencha/ext-modern-theme-material
npm install --save @sencha/ext-webpack-plugin
npm install --save @webcomponents/webcomponentsjs
```

你会发现`@webcomponents/webcomponentsjs`是无法安装的，这个要我们购买后，使用`npm login --registry=https://npm.sencha.com --scope=@sencha`登录才可以，

我们这里尝鲜，引入试用的npm包即可，详细可参照[ExtWebComponents Trial](https://www.sencha.com/products/extwebcomponents/evaluate/)

```json
{
 "name": "ext-web-components-modern-demo",
 "version": "7.2.0",
 "description": "ext-web-components-modern-demo",
 "scripts": {
  "start": "npx servor . index.html 8080 --reload --browse"
 },
 "devDependencies": {
  "@sencha/ext-web-components-modern": "~7.2.0",
  "@sencha/ext-modern-runtime": "~7.2.0",
  "servor": "~3.2.0"
 },
 "author": "",
 "license": "ISC",
 "repository": {}
}
```

以下是完整的`package.json`

```json
{
  "name": "ext-web-components-boilerplate-vue-cli",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.6.5",
    "vue": "^3.0.0"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "@vue/compiler-sfc": "^3.0.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^7.0.0",
    "@sencha/ext": "^7.2.0",
    "@sencha/ext-modern": "^7.2.0",
    "@sencha/ext-modern-theme-material": "^7.2.0",
    "@sencha/ext-webpack-plugin": "^7.2.0",
    "@webcomponents/webcomponentsjs": "^2.2.10",
    "@sencha/ext-web-components-modern": "~7.2.0",
    "@sencha/ext-modern-runtime": "~7.2.0",
    "servor": "~3.2.0"
  }
}
```

## vue.config.js

在Vue中配置webpack的`ext-webpack-plugin`

```js
const ExtWebpackPlugin = require('@sencha/ext-webpack-plugin');
const path = require('path');

module.exports = {
  // lintOnSave: false,
  devServer: {
    contentBase: 'build',
    hot: true,
    historyApiFallback: true,
    host: '0.0.0.0',
    port: '8080',
    disableHostCheck: false,
    compress: false,
    inline: true,
    stats: 'none',
  },
  outputDir: path.join(__dirname, 'build'),
  configureWebpack: {
    plugins: [
      new ExtWebpackPlugin({
        framework: 'web-components',
        toolkit: 'modern',
        emit: 'yes',
        browser: 'no',
        packages: [],
        profile: '',
        verbose: 'no',
        treeshake: 'no',
        environment: 'development'
      }),
    ]
  }
}
```

## main.js

```js
/*global Ext*/
import { createApp } from "vue";
import router from './router';
import App from './App.vue'
// import '@sencha/ext-web-components-modern/lib/ext-panel.component'; // 正式版本引入
import '../node_modules/@sencha/ext-modern-runtime/modern.engine.enterprise.js' // 试用版本只能引入js
import '../node_modules/@sencha/ext-web-components-modern/ext-web-components-modern.js' // 试用版本只能引入js

Ext.onReady(function () {
  const app = createApp(App);
  app.use(router);
  app.mount('#app');
});
```

## App.vue

我这边就展示简单的面板，顶部有个toolbar，里面有按钮，点击切换下面的路由视图。

```vue
<template>
  <ext-panel title="Ext-Web-Components Example" bodyPadding="20px">
    <ext-toolbar border="1">
      <ext-button text="Button" border="1" @tap="showComponent('Button')"></ext-button>
      <ext-button text="Grid" border="1" @tap="showComponent('Grid')"></ext-button>
      <ext-button text="Tree" border="1" @tap="showComponent('Tree')"></ext-button>
    </ext-toolbar>
    <router-view></router-view>
  </ext-panel>
</template>
<script>
export default {
  name: "app",
  data() {
    return {};
  },
  methods: {
    showComponent(componentName) {
      this.$router.push("/example/" + componentName);
    },
  },
};
</script>
```

这里要**注意**的是，文档里面事件都有个`on`前缀，比如`ontap`表示按钮点击事件，这样写的话，一般要在原生的html里面使用，如下，

```html
<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=10,user-scalable=yes">
</head>
<link rel="icon" href="data:;base64,iVBORw0KGgo=">

<script src="./node_modules/@sencha/ext-modern-runtime/modern.engine.enterprise.js" defer></script>
</link>
<script type="module" src="./node_modules/@sencha/ext-web-components-modern/ext-web-components-modern.js" defer></script>

<script>
  getNow = function() {
  document.querySelector('#updateDiv').innerText = new Date();
  }
</script>

<body>
  <ext-toolbar border="1">

    <ext-button text="get now" border="1" ontap="getNow"></ext-button>
    <div id='updateDiv' style="margin-left:20px;"></div>
  </ext-toolbar>
</body>
</html>
```

如果，你在vue组件里面使用的话，`ontap` 要写成 `@tap`，才能调用组件里面定义的方法。

## More

个人感觉，extjs强大的UI组件库提供了视觉一致的设计性，加上详细的开发文档，配合简单容易上手的vue，能极大的提高开发效率，开发管理后台的话很合适。不过毕竟是收费产品，个人开发的话试用版体验一下即可，企业开发的话，不差钱的话，可以到官方商店看下，[sencha store](https://www.sencha.com/store/)，目前是$799 /Per developer。

## 相关链接

[getting-started-vue](https://docs.sencha.com/extwebcomponents/7.3.0/guides/getting_started/getting_started_vue.html)

[ExtWebComponents  Evaluate](https://www.sencha.com/products/extwebcomponents/evaluate/)

[ExtWebComponents Docs](https://docs.sencha.com/extwebcomponents/7.3.0/)

[ExtWebComponents Example](https://examples.sencha.com/ExtWebComponents/7.3.0)

[github ext-web-components-boilerplate-vue-cli](https://github.com/jianchengwang/todo-web/blob/master/vue/ext-web-components-boilerplate-vue-cli)