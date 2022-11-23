---
title: build-mysite
slug: build-mysite
author: [jianchengwang]
date: 2020-12-23
excerpt: "之前个人站点使用的hexo构建，因为主题都是依赖别人的，感觉不是很好改，而且现在构建一个静态网站，也不是太难，所以这边就花个两三天时间，利用nuxt构建一波，这边简单做个记录"
draft: false
tags: [frontend]
---

之前个人站点使用的`hexo`构建，因为主题都是依赖别人的，感觉不是很好改，而且现在构建一个静态网站，也不是太难，所以这边就花个两三天时间，利用`nuxt`构建一波，这边简单做个记录

选择`nuxt`主要是基于vuejs，现在社区很活跃，提供了很多插件，比如`nuxt-content`可以方便渲染markdown文件，对于前端菜鸡的后端狗来说，上手容易，内置路由基本不需要配置，跟据目录结构生成动态路由，支持ssr等

后端服务使用`go`，上手也比较容易，性能不错

## create nuxt-app

```shell
yarn create nuxt-app my-site
```

这个脚手架默认会生成一些目录跟文件，具体看官方文档，

这里简单说明几个需要用到的目录跟文件，其实构建后每个目录都有个README.md有相关说明，这里赘述一下，

1. `assets`：静态资源目录，这个目录webpack会构建处理，可以放需要预处理的样式脚本等，比如scss，或者svg图片等，
2. `static`：不需要预处理的，比如引入现成的typo.css，或者第三方库打包好的js，这里的文件打包后会映射到站点跟目录下
3. `components`：vue组件目录
4. `store`：vuex状态目录
5. `layouts`：页面布局，其他页面，引入这个目录的文件就可以复用这个页面的布局，一般是网站的头部，底部啥的，
6. `pages`：站点的页面，这个目录下的文件会跟据目录名，文件名生成对应的路由，同时也支持动态路由，命名下划线开头`_`
7. `plugins`：公共方法，也可以放在这里，方便全局引入调用
8. `nuxt.config.js`：nuxt的配置文件，可以引入全局的css，plugins，第三方常见库等
9. `content`：这个目录就是我们来放置markdown文档的目录

## add nuxt-content

```shell
yarn add @nuxt/content
```

```js
export default {
  modules: ['@nuxt/content']
}
```

引入`nuxt-content`模块来获取，解析处理markdown文档，并且支持在markdown文件里面写html，引入vue组件等，访问API http://localhost:3000/_content/等

具体使用可以查看 [nuxt-content文档](https://content.nuxtjs.org/)

```js
async asyncData({ $content, params, error }) {
    const page = await $content("about", "index")
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: "Page not found", err });
      });
    return {
      page,
    };
  },
```

我们还可以通过在markdown文件中添加一个YAML前端内容块来添加自定义注入变量。它必须位于文件的顶部，并且必须是一种有效的YAML格式，设置在三个三虚线之间。这对于添加搜索引擎优化变量很有用，比如文章的标题、描述和图像。

这边举个例子，比如我有一个`girls`页面，上面要显示所有我喜欢的女孩子，这时候我可以在`content/girls/index.md`编辑内容如下，

```markdown
---
layout: girls
title: Lovely Girls
icon: icon-women-line
banner: <span title="我全都要！">大家都是我的天使！</span>
girls:
  - name: 御坂美琴
    avatar: https://blog.res.jianchengwang.info/girls/yubanmeiqin.png
    from: 某科学的超电磁炮
    reason: 电他!!!!

  - name: 冯宝宝
    avatar: https://blog.res.jianchengwang.info/girls/fengbaobao.jfif
    from: 一人之下
    reason: 埋他!!!!
  
  - name: 惠惠
    avatar: https://blog.res.jianchengwang.info/girls/huihui.jpg
    from: 为美好的世界献上祝福
    reason: 为万恶的动画人设献上爆炎!!!!

  - name: 三笠
    avatar: https://blog.res.jianchengwang.info/girls/sanli.jpg
    from: 进击的巨人
    reason: 刚毅且温柔的女汉子，哈!!!!

---

```

那么在`pages/girls/index.vue`获取

```js
async asyncData({ $content, params, error }) {
    const page = await $content("girls", "index")
    .fetch()
    .catch((err) => {
        error({ statusCode: 404, message: "Page not found", err });
    });
    return {
        title: page.title ? page.title : "Girls",
        banner: page.banner ? page.banner : "大家都是我的天使！",
        girls: page.girls,
    };
},
```

获取girls数据之后，跟据自己的需要渲染页面即可，比如你想做个图库，待办事项列表，还是歌单，书单其实都可以实现，

## dynamic routing

我这边如果是博客文章的话，目前分为`tech`，`life`，`store`三个目录，每个目录下是对应目录的markdown文件，所以我这边使用动态路由，`pages/_post/index.vue` 展示目录下的博客列表，`pages/_post/_id.vue`展示对应目录下的博文详情页面；后面因为还有标签页，标签页的博客列表其实也是可以复用的，所以其实，这两个文件足够了，以下是`_id.vue`的部分代码，我们可以通过参数`params.post`，`params.id` 获取我们动态路由的参数

```js
 async asyncData({ $content, params }) {
    let postPath = params.post;
    let postId = params.id;

    if (postPath === "tag") {
      const tag = postId;
      const docs = await $content("", { deep: true })
        .only([
          "title",
          "description",
          "img",
          "slug",
          "author",
          "createdAt",
          "path",
        ])
        .where({ tags: { $contains: tag } })
        .sortBy("createdAt", "asc")
        .fetch();
      return {
        postPath: postPath,
        docs,
        listTitle: "#" + tag,
        postId: undefined,
        tags: undefined,
      };
    } else {
      const doc = await $content(postPath, postId).fetch();
      console.info(doc.path);
      const [prev, next] = await $content(postPath)
        .only(["title", "slug", "path"])
        .sortBy("createdAt", "asc")
        .surround(postId)
        .fetch();
      return {
        doc,
        prev,
        next,
        postId,
        tags: undefined,
      };
    }
  },
```

图库albumns也是有使用动态路由`pages/albumns/_id.vue`，因为图库有不同相册，每个相册又有很多图片，

其他的页面比如about，girls等就是简单的获取数据，然后跟据各自页面的展示即可，

## add typo.css

`nuxt-config.js`引入markdown的样式文件，这里使用[typo.css](https://typo.sofi.sh/)，

```js
 // Global CSS (https://go.nuxtjs.dev/config-css)
  css: ['~assets/main.scss', '~static/css/typo.css'],
```

当然你也可以到[typora主题](https://theme.typora.io/)下载，里面有很多markdown的样式，跟据自己需要魔改下即可。

## add color-mode

```shell
yarn add --dev @nuxtjs/color-mode
```

```js
{
  buildModules: [
    '@nuxtjs/color-mode'
  ]
}
```

参考[nuxt/color-mode-module](https://github.com/nuxt-community/color-mode-module) 的example代码，这里不多赘述

## add google fonts

官网地址：[https://fonts.google.com/](https://fonts.google.com/)

跟据自己的需要生成字体样式链接，我这边中文使用**Noto+Serif+SC**，英文使用**EB+Garamond**，在`nuxt.config.js`加入样式链接即可

```js
link: [
    { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
    { rel: 'preconnect', href: 'https://fonts.gstatic.com' },
    { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@400;500;700&display=swap' },
    { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css2?family=EB+Garamond:ital,wght@0,400;0,700;1,400;1,700&display=swap' },
    ],
```

## add lightgallery

我这边albumns相册图片主要通过`lightgallery`展示，所以只要在`pages/albumns/_id.vue`引入lightgallery的js跟css即可，

```js
head: {
    script: [
      {
        src:
          "https://cdn.jsdelivr.net/npm/lightgallery.js@latest/dist/js/lightgallery.min.js",
        mode: "client",
      },
    ],
    link: [
      {
        rel: "stylesheet",
        href:
          "https://cdn.jsdelivr.net/npm/lightgallery.js@latest/dist/css/lightgallery.min.css",
      },
    ],
  },
};
```

页面挂载的使用调用，

```js
 mounted() {
    setTimeout(() => {
      let el = document.getElementById("lightgallery");
      window.lightGallery(el);
    }, 1000);
  },
```

## vue-lazyload
因为相册有很多图片，为了比较好的体验，一般需要延迟加载，这边使用[vue-lazyload](https://github.com/hilongjw/vue-lazyload)
```sh
 yarn add vue-lazyload
```

在`plugins`创建一个文件`vueLazyLoad.js`

```js
import Vue from 'vue'
import VueLazyLoad from 'vue-lazyload'

Vue.use(VueLazyLoad, {
  preLoad: 1.33, // 预加载的宽高比，4:3
  error: 'https://blog.res.jianchengwang.info/404.gif', // 加载失败时使用的图片
  loading: 'https://blog.res.jianchengwang.info/loading.gif', // 加载时的loading图
  attempt: 2 //尝试加载次数
})
```

在`nuxt.config.js`中配置插件：

```js
plugins: [
  { src: '~plugins/vueLazyLoad', ssr: false }
]
```

将需要懒加载处的`:src`属性改为`v-lazy`，刷新页面，即可看到效果

## add live2d-web

参考[jianchengwang/live2d_models](https://github.com/jianchengwang/live2d_models)

我之前hexo里也有用到看板娘，已经封装好了通用的js，在`nuxt-config.js`引入即可

```js
 script: [
     {
         src:
         "https://cdn.jsdelivr.net/gh/jianchengwang/live2d_models@main/assets/js/live2dv3.init.js",
         mode: "client",
     },
 ],
```

## go serve

之前服务端用node.js做一些简单的接口服务，

```js
  serverMiddleware: [
    // API middleware
    '~/server/index.js'
  ],
```

后面学习了`go`，所以就替换为go语言实现，

起先挂载`nuxt`生成的静态目录，使用`go embed`，

```go
package nuxtsite

import (
	"embed"
)

//go:embed dist
var FS embed.FS
```

```go
fe, err := fs.Sub(nuxtsite.FS, "dist")
if err != nil {
	log.Fatal("Failed to sub path `dist`: %v", err)
}
http.Handle("/", http.FileServer(http.FS(fe)))
```

不过，因为生成的`dits`里面包含`_nuxt`目录，这种下划线的前缀的目录被go认为是隐藏文件，所以`go embed`会忽略掉，感兴趣的可以自行了解[cmd/go: avoid surprising inclusion of "hidden" files when using //go:embed](https://github.com/golang/go/issues/42328)

所以这里换一种写法

```go
http.Handle("/", http.FileServer(http.Dir("./nuxtsite/dist")))
```

并且，`nuxt`使用`nuxt generate`生成`dist`目录，默认动态路由是没有生成静态文件的，详情请看[configuration-generate](https://www.nuxtjs.cn/api/configuration-generate)

所以这里要写个工具类，自动生成静态路由配置，

```js
const fs = require('fs')
const path = require('path')

const travel = function travel (dir, callback) {
  fs.readdirSync(dir).forEach((file) => {
    const pathname = path.join(dir, file)
    if (fs.statSync(pathname).isDirectory()) {
      travel(pathname, callback)
    } else {
      callback(pathname)
    }
  })
}

const genRoutes = function (dir) {
  const asyncRoutes = []
  travel(dir, function (pathname) {
    const targetDir = dir.replace('./', '')
    pathname = pathname.replace(targetDir, '').replace('\\', '/').replace('\\', '/').replace('.md', '')
    console.info(pathname)
    asyncRoutes.push(pathname)
  })
  return asyncRoutes
}

const genUtils = {
  travel,
  genRoutes
}

module.exports = genUtils
```

然后在`nuxt.config.js`进行配置

```js
generate: {
    routes () {
      const genUtils = require('./gen/genUtils')
      return genUtils.genRoutes('./content').map((path) => {
        return {
          route: path
        }
      })
    }
  }
```

## docker

这里就不都赘述了，`Dockerfile`文件如下，

```shell
FROM alpine:latest

RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone

# install git - apt-get replace with apk
RUN apk update && \
    apk upgrade && \
    apk add --no-cache bash git openssh

RUN mkdir -p /etc/mysite/nuxtsite
WORKDIR /etc/mysite

# 添加go二进制文件
ADD mysite /etc/mysite

# 添加静态文件dist目录
COPY ./nuxtsite/dist /etc/mysite/nuxtsite/dist

RUN chmod 655 /etc/mysite/mysite

ENTRYPOINT ["/etc/mysite/mysite"]
EXPOSE 8081
```



## github workflow

这个就不多说了，直接贴代码，仅供参考，

如果有自己的云服务器，建议直接在云服务器上配置下编译环境，然后写个执行脚本，通过`github workflow`调用执行，速度会快很多，类似之前的`webhooks`，只不过不用在云服务上部署个webhooks服务，

```yaml
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the action will run.
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
    paths-ignore:
      - README.md
      - .github/*


  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      - name: Setup Node
        uses: actions/setup-node@v1
        with:
          node-version: "12.x"

      - name: Build
        run: cd nuxtsite && yarn && yarn generate

      - name: Deploy Github Page
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./nuxtsite/dist
          publish_branch: gh-pages

      # - name: Deploy Ty
      #   uses: AEnterprise/rsync-deploy@v1.0  # 使用别人包装好的步骤镜像
      #   env:
      #     DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}   # 引用配置，SSH私钥
      #     ARGS: -e -c -r --delete   # rsync参数
      #     SERVER_PORT: ${{ secrets.SSH_PORT }}  # SSH端口
      #     FOLDER: ./dist/  # 要推送的文件夹，路径相对于代码仓库的根目录
      #     SERVER_IP: ${{ secrets.SSH_HOST }}  # 引用配置，服务器的host名（IP或者域名domain.com）
      #     USERNAME: ${{ secrets.SSH_USERNAME }}  # 引用配置，服务器登录名
      #     SERVER_DESTINATION: ${{ secrets.SERVER_DESTINATION }}   # 部署到目标文件夹

      - name: Deploy Ty By Shell
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SSH_HOST }}
          port: ${{ secrets.SSH_PORT }}
          username: ${{ secrets.SSH_USERNAME }}
          key: ${{ secrets.DEPLOY_KEY }}
          command_timeout: 200m
          script: |
            echo 'deploy begin'
            /root/_git/my-site/deploy/build.sh
            echo 'deploy done'
```

以下是`build.sh`脚本内容，

```shell
#!/bin/bash

# Get real path
BASEDIR=$(cd `dirname $0` && pwd)
cd ${BASEDIR}

# Log Location on Server.
LOG_LOCATION=${BASEDIR}
exec > >(tee -i $LOG_LOCATION/build.`date +%Y%m%d%H%M%S`.log)
exec 2>&1

cd ../
# 拉取代码
git pull origin main

# 打包nuxtsite
cd ./nuxtsite
yarn install
yarn generate

# 编译go
cd ../
go env -w GOPROXY=https://goproxy.cn,direct
go env -w GO111MODULE=on
go env -w CGO_ENABLED=0
go env -w GOARCH=amd64
go env -w GOOS=linux
go mod vendor
go build -tags netgo -o mysite main.go

# 打包docker镜像
docker build -t mysite:v0.0.1 .
docker tag mysite:v0.0.1 jianchengwang/mysite
docker login
docker push jianchengwang/mysite

# 部署
cd ${BASEDIR}
kill -9 `netstat -nlp | grep :8081 | awk '{print $7}' | awk -F"/" '{ print $1 }'`
docker pull jianchengwang/mysite:latest
docker-compose up -d
docker rmi $(docker images | grep "none" | awk '{print $3}')
```



## 相关链接

[github my-site](https://github.com/jianchengwang/my-site)

[nuxtjs](https://nuxtjs.org/)

[creating-blog-with-nuxt-content](https://nuxtjs.org/blog/creating-blog-with-nuxt-content/)

[going-dark-with-nuxtjs.color-mode](https://nuxtjs.org/blog/going-dark-with-nuxtjs-color-mode/)
