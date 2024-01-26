---
title: intro-to-serverless-functions
slug: intro-to-serverless-functions
author: [jianchengwang]
date: 2020-11-13
excerpt: "最近喵了下Introduction to Serverless Functions视频感觉还不错。下面主要根据视频作者Jason Lengstorf的ppt简单做下笔记，最底下会列出相关链接"
draft: false
tags: [devops]
---

最近喵了下 [Introduction to Serverless Functions](https://frontendmasters.com/courses/serverless-functions/) 视频感觉还不错。下面主要根据视频作者`Jason Lengstorf` 的`ppt` 简单做下笔记，最底下会列出相关链接。

[示例代码](https://github.com/jianchengwang/todo-web/raw/master/serverless/frontendmasters-serverless) [示例网站](https://demo-frontentedmaster-serverless-functions.netlify.app/)

## 前言

**serverless** 一般分为**bass**(Backend as a Service)和**fass**(Function as a Service)两种，今天我们主要学习一下**fass**，国内云厂商一般称为**云函数**。简单来说，就是依赖云厂商的平台建设，使我们只关注业务代码，不需要考虑服务器相关内容，也更容易的进行资源的弹性扩容，基于事件驱动，避免闲时资源浪费。

当然有利也有弊，**fass**基于**Data-shipping**架构，即：**将数据传输到代码所在处执行**，而不是**将代码传输到数据所在处执行**，因为数据一般都比代码的传输量要大得多。而目前主流的 Faas 平台都是『**将数据传输到代码所在处执行**』这种架构，所以这是 Faas 的最大缺陷，同时也不好维护状态，事务啥的。

所以，一般就作为一些公共基础建设，比如图片预处理，API网关数据拉取，这种**无状态**的**独立功能**函数。

## 准备工作

今天我们主要使用**node.js**构建我们的函数应用，所以你要先安装下**node.js v12 or higher**，

需要云服务商[Netlify](https://netlify.com)跟[Hasura](https://hasura.io)提供服务，所以需要你有这两个网站的账号，都提供免费额度使用，所以无需担心费用问题。

还需要，[omdbapi](http://www.omdbapi.com/)，提供模拟数据，所以创建一个**API KEY**即可，

另外有的网站可能需要科学上网，这个需要你自己解决了。

## 搭建本地环境

```shell
# install the Netlify CLI for local development
npm install -g netlify-cli@latest
# clone the starting point for development
git clone --branch start https://github.com/jlengstorf/frontendmasters-serverless.git
# recommended use vscode
code frontendmasters-serverless
# create functions dir
cd frontendmasters-serverless
mkdir functions
# cofnig functions location
vim netlify.toml
[build]
  command = "npm run build"
  publish = "_site"
  functions = "functions"
```

最终目录结构如下：

```shell
.eleventy.js
.env # 环境变量
.gitignore
_site
data # 静态数据
functions # 函数目录
netlify.toml # 配置文件
node_modules
package.json
package-lock.json
README.md
src # 网站代码
```

这个看你使用哪个云厂商提供服务了，目录跟配置项可能不一样，本示例使用的是[Netlify Functions](https://www.netlify.com/products/functions/)，

我们现在就来使用**serverless functions** 搭建一个简单的我看过的电影展示列表，网站静态页面跟样式已经有了，你可以启动服务看下效果

```shell
ntl dev
```

## Boop

```shell
cd functions
vim boop.js
```

```js
exports.handler = (event, context, callback) => {
  // "event" has information about the path, body, headers, etc. of the request
  console.log('event', event)
  // "context" has information about the lambda environment and user details
  console.log('context', context)
  // The "callback" ends the execution of the function and returns a response back to the caller
  return callback(null, {
    statusCode: 200,
    body: JSON.stringify({
      data: 'Boop!'
    })
  })
}
```

可以看到，**serverless functions** 函数结构很简单，就是声明一个方法，预处理，然后执行回调函数。

当然你直接使用`return` 

```js
exports.handler = async () => {
    return {
        statusCode: 200,
        body: 'Boop!'
    }
}
```

然后，我们访问`http://localhost:8888/.netlify/functions/boop`，即可看到返回的数据了

## 获取本地数据

现在我们要从本地，`data/movies.json` 获取数据，然后渲染在页面上，`functions` 目录下创建`movies.js`

```js
//  get movies from local json
const movies = require('../data/movies.json')
exports.handler = async () => {
    return {
        statusCode: 200,
        body: JSON.stringify(moviesWithRatings),
    }
}
```

然后`src/index.html` 请求数据，渲染页面即可

```html
<script>
async function initialize() {
    const movies = await fetch('/.netlify/functions/movies').then((response) =>
      response.json(),
    );

    const container = document.querySelector('.movies');
    const template = document.querySelector('#movie-template');

    movies.forEach((movie) => {
      const element = template.content.cloneNode(true);

      const img = element.querySelector('img');
      img.src = movie.poster;
      img.alt = movie.title;

      element.querySelector('h2').innerText = movie.title;
      element.querySelector('.tagline').innerText = movie.tagline;

      container.appendChild(element);
    });
  }
</script> 
```

## 获取请求参数

用过`event`参数获取，`functions` 目录下创建`movie-by-id.js`

```js
const movies = require('../data/movies.json')

exports.handler = async({ queryStringParameters }) => {
    const { id } = queryStringParameters;
    const moive = movies.find(m => m.id === id);

    if(!moive) {
        return {
            statusCode: 404,
            body: 'Not Found'
        }
    }

    return {
        statusCode: 200,
        body: JSON.stringify(moive)
    }
}
```

然后访问`http://localhost:8888/.netlify/functions/movie-by-id?id=tt2975590`

当然`event`还包含很多其他信息，有兴趣的话可以喵下官方文档，或者，把`event`返回出来看看。

## 拉取OMDBAPI数据

获取第三方接口，一般需要**API-KEY**提供凭证，一般配置在环境变量里面，

```shell
vim .env
OMDB_API_KEY=
```

这边使用`node-fetch`请求第三方接口

```shell
npm install node-fetch
```

我们这里，通过**OMDBAPI**获取影片的评分信息，调整`functions/movies.js`代码示例如下：

```js

const { URL } = require('url')
const fetch = require('node-fetch')

//  get movies from local json
const movies = require('../data/movies.json')

exports.handler = async () => {

    const movieScoreApi = new URL("https://www.omdbapi.com/");
    // add the secret API key to the query string
    movieScoreApi.searchParams.set('apiKey', process.env.OMDB_API_KEY)

    const promises = movies.map(movie => {
        movieScoreApi.searchParams.set('i', movie.id);
        return fetch(movieScoreApi)
        .then(response => response.json())
        .then(data => {
            const scores = data.Ratings;
            return {
                ...movie,
                scores
            }
        })
    })

    // awaiting all Promises lets the requests happen in parallel
    // see: https://lwj.dev/blog/keep-async-await-from-blocking-execution/
    const moviesWithRatings = await Promise.all(promises);

    return {
        statusCode: 200,
        body: JSON.stringify(moviesWithRatings),
    }
}
```

`src/index.html` 代码页面也做相应调整，这里就不多赘述。

## Hasura Graphql

`movies.json`的数据到达一定量的话，一般会存到数据库便于管理，这边使用**Hasura Graphql**，创建一张表`movies`表存储，并将`movies.json`数据填充几条到`movies`表中。

所以这边也要配置下**Hasura Graphql** 请求的环境变量：

```shell
vim .env
HASURA_API_URL=
HASURA_ADMIN_SECRET=
```

关于**Hasura Graphql**的使用，语法等可以参考官方文档[hasura graphql](https://hasura.io/docs/1.0/graphql/core/index.html)，

`HASURA_ADMIN_SECRET` 其实就是`NEW ENV VARS`即可，

首先我们写一个工具方法，用来调用**Hasura Graphql**接口，`functions/util/hasura.js`

```js
const fetch = require('node-fetch')

async function query({ query, variables = {} }) {
    const result = await fetch(process.env.HASURA_API_URL, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'X-Hasura-Admin-Secret': process.env.HASURA_ADMIN_SECRET,
        },
        body: JSON.stringify({ query, variables }),
    })
    .then(response => response.json())
    .catch(function(e) {
        console.log(e);
    });
    // TODO send back helpful information if there are errors

    console.info(result)
    return result.data;
}

exports.query = query;
```

然后再调整一下，`functions/movies.js`

```js

const { query } = require('./util/hasura');

exports.handler = async () => {

    // get movies from db
    const { movies } = await query({
        query: `
            query {
                movies {
                    id
                    title
                    tagline
                    poster
                }
            }
        `,
    });

    ...other code

    return {
        statusCode: 200,
        body: JSON.stringify(moviesWithRatings),
    }
}
```

然后再写个添加方法`functions/add-movie.js`

```js
const { query } = require('./util/hasura')

exports.handler = async function(event) {
    const { id, title, tagline, poster } = JSON.parse(event.body);

    const result = await query({
        query: `
            mutation CreateMovie($id: String!, $poster: String!, $tagline: String!, $title: String!) {
                insert_movies_one(object: {id: $id, poster: $poster, tagline: $tagline, title: $title}) {
                    id
                    poster
                    tagline
                    title
                }
            }
        `,
        variables: { id, title, tagline, poster },
    });

    return {
        statusCode: 200,
        body: JSON.stringify(result),
    };
}
```

调整下添加页面，`src/admin.html`

```html
<script>
  async function handleSubmit(event) {
    event.preventDefault();
    const data = new FormData(event.target);
    const result = await fetch('/.netlify/functions/add-movie', {
      method: 'POST',
      body: JSON.stringify({
        id: data.get('id'),
        title: data.get('title'),
        tagline: data.get('tagline'),
        poster: data.get('poster'),
      }),
    }).then((response) => {
      document.querySelector(
        '.message',
      ).innerText = `Response: ${response.status} — ${response.statusText}`;
    });
  }

  document.querySelector('#add-movie').addEventListener('submit', handleSubmit);
</script>
```

## Netlify Identify

网站一般要进行身份验证，然后不同身份认证有不同的操作权限。比如我的影片列表，我可以进行添加编辑操作，其他人只能进行浏览。所以，一般要引入身份认证，如果从头自己搞登录逻辑，可能比较繁琐，一般也是独立出一个认证的微服务。

这边简单的使用[Netlify Identify](https://docs.netlify.com/visitor-access/identity/)来进行网站的身份认证，如果你使用其他云厂商，这部分可以略过

此时需要我们先部署一个网站，你可以直接使用`netlify-cli`

```shell
ntl init
```

我这边不知道是因为网络原因还是啥的，`netlify-cli`认证不了，所以我直接登录[app.netlify](https://app.netlify.com/)操作了，部署后，可以直接[在线访问](https://demo-frontentedmaster-serverless-functions.netlify.app/)，

**app.netlify** 对应站点管理，对我们刚部署的站点启用`Identify`，

**Netlify Identify** 已经集成了UI界面，所以我们要引入 [netlify-identity-widget](https://github.com/netlify/netlify-identity-widget)

```html
<!-- include the widget -->
<script type="text/javascript" src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script >
```

这里，我只在`src/admin.html`  `src/login.html`引入了，

这边我想要实现的效果是，用户登录才能访问`src/admin`页面进行添加电影的操作，

首先，我们在`src/login.html` 添加如下代码：

```html
<div data-netlify-identity-button></div>
<script
  type="text/javascript"
  src="https://identity.netlify.com/v1/netlify-identity-widget.js"
></script>

<script>
  function handleLogin(user) {
    if (!user || !user.token) {
      return;
    }

    // if we get here, we have an active user; redirect to the admin page!
    window.location.pathname = '/admin/';
  }

  window.netlifyIdentity.on('init', handleLogin);
  window.netlifyIdentity.on('login', handleLogin);
</script>
```

调整`src/admin.html` 代码，

```html
<script
  type="text/javascript"
  src="https://identity.netlify.com/v1/netlify-identity-widget.js"
></script>
<script>

  function handleIdentityEvent(user) {
    if (user && user.token) {
      return;
    }

    window.location.pathname = '/login/';
  }

  netlifyIdentity.on('init', handleIdentityEvent);
  netlifyIdentity.on('logout', handleIdentityEvent);

  document.querySelector('.logout').addEventListener('click', (event) => {
    event.preventDefault();
    netlifyIdentity.logout();
  });
    
  ....
```

## 腾讯云函数

这里简单举个小例子，比如做一个返回json数据的接口，触发管理为**API网关触发器**，可以看到语言规范基本一致。

```js
'use strict';

exports.main_handler = (event, context, callback) => {
    console.log("Hello World")
    console.log(event)
    console.log(event["non-exist"])
    console.log(context)
    callback(null, require('data/fooddata.js'))
};
```

跟据自己的需要选择合适的云平台，当然也有很多完善云平台使用的`serverless`框架，

本篇仅作为一个简单的入门`demo`，回调方法也不仅仅是返回`JSON`，也有很多`OUTPUT`形式，各个云平台也会跟据自己现有的服务，提供各种集成机制，感兴趣的自己自行扩展阅读。

## 相关链接

[github:serverless](https://github.com/serverless/serverless)

[github:awesome-serverless](https://github.com/anaibol/awesome-serverless)

[Introduction to Serverless Functions](https://frontendmasters.com/courses/serverless-functions/)

[gihub:frontendmasters-serverless](https://github.com/jlengstorf/frontendmasters-serverless)

[netlify](netlify.com)

[hasura](http://hasura.io/)