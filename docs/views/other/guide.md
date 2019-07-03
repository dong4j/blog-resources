---
title: 使用 Vuepress 搭建博客
date: 2015-01-01
categories:
  - Other
tags: 
  - Other
---

::: tip
使用 vuepress 搭建自己的博客
:::

<!-- more-->

## Vuepress 介绍

官网: [https://vuepress.vuejs.org/](https://yq.aliyun.com/go/articleRenderRedirect?url=https://vuepress.vuejs.org/)

类似 hexo 一个极简的静态网站生成器, 用来写技术文档不能在爽. 当然搭建成博客也不成问题. 

## Vuepress 特点

- 响应式, 也可以自定义主题与 hexo 类似
- 内置 markdown (还增加了一些扩展), 并且可以在其使用 Vue 组件
- Google Analytics 集成
- PWA 自动生成 Service Worker

## 快速上手

### 安装

初始化项目

```shell
yarn init -y
# 或者 npm init -y
```

安装 vuepress

```shell
yarn add -D vuepress
# 或者 npm install -D vuepress
```

全局安装 vuepress

```shell
yarn global add vuepress
# 或者 npm install -g vuepress
```

新建一个 docs 文件夹

```shell
mkdir docs
```

设置下 package.json

```shell
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

### 写作

```shell
yarn docs:dev # 或者: npm run docs:dev
```

也就是运行开发环境, 直接去 docs 文件下书写文章就可以, 打开 `http://localhost:8080/` 可以预览

### 构建

build 生成静态的 HTML 文件, 默认会在 `.vuepress/dist` 文件夹下

```shell
yarn docs:build # 或者: npm run docs:build
```

## 基本配置

在 `.vuepress` 目录下新建一个 `config.js`, 他导出一个对象

一些配置可以参考[官方文档](https://yq.aliyun.com/go/articleRenderRedirect?url=https://vuepress.vuejs.org/config/#base) , 这里我配置常用及必须配置的

### 网站信息

```shell
module.exports = {
  title: '游魂的文档',
  description: 'Document library',
  head: [
    ['link', { rel: 'icon', href: `/favicon.ico` }],
  ],
}
```

### 导航栏配置

```javascript
module.exports = {
  themeConfig: {
    nav: [
      { text: '主页', link: '/' },
      { text: '前端规范', link: '/frontEnd/' },
      { text: '开发环境', link: '/development/' },
      { text: '学习文档', link: '/notes/' },
      { text: '游魂博客', link: 'https://www.iyouhun.com' },
      // 下拉列表的配置
      {
        text: 'Languages',
        items: [
          { text: 'Chinese', link: '/language/chinese' },
          { text: 'English', link: '/language/English' }
        ]
      }
    ]
  }
}
```

如图: 

![img](https://wx1.sinaimg.cn/large/99a97bd9ly1fr1oz3elibj20fg02bjr9.jpg)

### 侧边栏配置

可以省略`.md` 扩展名, 同时以 `/` 结尾的路径将会被视为 `*/README.md`

```javascript
module.exports = {
  themeConfig: {
    sidebar: {
      '/frontEnd/': genSidebarConfig('前端开发规范'),
    }
  }
}
```

上面封装的 `genSidebarConfig` 函数

```javascript
function genSidebarConfig(title) {
  return [{
    title,
    collapsable: false,
    children: [
      '',
      'html-standard',
      'css-standard',
      'js-standard',
      'git-standard'
    ]
  }]
}
```

支持侧边栏分组 (可以用来做博客文章分类) collapsable 是当前分组是否展开

```javascript
module.exports = {
  themeConfig: {
    sidebar: {
      '/note': [
        {
          title:'前端',
          collapsable: true,
          children:[
            '/notes/frontEnd/VueJS组件编码规范',
            '/notes/frontEnd/vue-cli脚手架快速搭建项目',
            '/notes/frontEnd/深入理解vue中的slot与slot-scope',
            '/notes/frontEnd/webpack入门',
            '/notes/frontEnd/PWA介绍及快速上手搭建一个PWA应用',
          ]
        },
        {
          title:'后端',
          collapsable: true,
          children:[
            'notes/backEnd/nginx入门',
            'notes/backEnd/CentOS如何挂载磁盘',
          ]
        },
      ]
    }
  }
}
```

## 默认主题修改

### 主题色修改

在`.vuepress` 目录下的创建一个 `override.styl` 文件

```javascript
$accentColor = #3eaf7c // 主题色
$textColor = #2c3e50 // 文字颜色
$borderColor = #eaecef // 边框颜色
$codeBgColor = #282c34 // 代码背景颜色
```

### 自定义页面类

有时需要在不同的页面应用不同的 css, 可以先在该页面中声明

```javascript
---
pageClass: custom-page-class
---
```

然后在 `override.styl` 中书写

```javascript
.theme-container.custom-page-class {
  /* 特定页面的 CSS */
}
```

## PWA 设置

设置 serviceWorker 为 true, 然后提供 Manifest 和 icons, 可以参考我之前的[《PWA 介绍及快速上手搭建一个 PWA 应用》](https://yq.aliyun.com/go/articleRenderRedirect?url=https://www.shen.ee/article/27276.html)

```javascript
module.exports = {
  head: [
    ['link', { rel: 'icon', href: `/favicon.ico` }],
    //增加manifest.json
    ['link', { rel: 'manifest', href: '/manifest.json' }],
  ],
  serviceWorker: true,
}
```

## 部署上线

### 设置基础路径

在 `config.js` 设置 base
例如: 你想要部署在 [https://foo.github.io](https://yq.aliyun.com/go/articleRenderRedirect?url=https://foo.github.io) 那么设置 base 为 `/`,base 默认就为 `/`, 所以可以不用设置
想要部署在 [https://foo.github.io/bar/,](https://yq.aliyun.com/go/articleRenderRedirect?url=https://foo.github.io/bar/,) 那么 `base` 应该被设置成 `"/bar/"`

```javascript
module.exports = {
  base: '/documents/',
}
```

`base` 将会自动地作为前缀插入到所有以 `/` 开始的其他选项的链接中, 所以你只需要指定一次. 

### 构建与自动部署

用 [gitHub](https://yq.aliyun.com/go/articleRenderRedirect?url=https://github.com) 的 pages 或者 [coding](https://yq.aliyun.com/go/articleRenderRedirect?url=https://coding.net/r/O5YOFA) 的 pages 都可以, 也可以搭建在自己的服务器上. 
将 `dist` 文件夹中的内容提交到 git 上或者上传到服务器就好

```javascript
yarn docs:build # 或者: npm run docs:build
```

> 另外可以弄一个脚本, 设置持续集成, 在每次 push 代码时自动运行脚本

deploy.sh

```shell
#!/usr/bin/env sh

# 确保脚本抛出遇到的错误
set -e

# 生成静态文件
npm run docs:build

# 进入生成的文件夹
cd docs/.vuepress/dist

# 如果是发布到自定义域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

# 如果发布到 https://<USERNAME>.github.io
# git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master

# 如果发布到 https://<USERNAME>.github.io/<REPO>
git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

cd -
```

## 注意事项 (坑)

- 把你想引用的资源都放在`.vuepress` 目录下的 `public` 文件夹
- 给 git 仓库绑定了独立域名后, 记得修改 `base` 路径
- 设置侧边栏分组后默认会自动生成 上 / 下一篇链接
- 设置了自动生成侧边栏会把侧边栏分组覆盖掉
- 设置 PWA 记得开启 SSL

## [vuepress-theme-reco](https://vuepress-theme-reco.recoluan.com/)

该主题几乎继承 `VuePress` 默认主题的一切功能, 所以本文档只负责介绍该主题扩展的功能, 如果您想要了解默认主题的一些功能, 请移步 [官方文档](https://v1.vuepress.vuejs.org/zh/theme/default-theme-config.html). 

### Branch

| branch   | vuepress | vuepress-theme-reco |
| -------- | :------: | :-----------------: |
| demo/0.x |   0.x    |         0.x         |
| demo/1.x |   1.x    |         1.x         |

### 安装

```bash
npm install vuepress-theme-reco -dev--save

# or

yarn add vuepress-theme-reco
```

### 使用

```shell
// 修改 /docs/.vuepress/config.js

module.exports = {
  theme: 'reco'
}  
```

### 分类和标签

#### 添加博客配置

```javascript
// change /docs/.vuepress/config.js

module.exports = {
  theme: 'reco',
  themeConfig: {
     // 博客设置
    blogConfig: {
      category: {
        location: 2,     // 在导航栏菜单中所占的位置, 默认2
        text: 'Category' // 默认文案 “分类”
      },
      tag: {
        location: 3,     // 在导航栏菜单中所占的位置, 默认3
        text: 'Tag'      // 默认文案 “标签”
      }
    }
  }  
}  
```

#### 写文章时添加分类和标签

```javascript
--- 
title: 【vue】跨域解决方案之proxyTable  
date: 2017-12-28
categories: 
 - frontEnd
tags: 
 - vue
---
```

::: tip

请注意,  `categories` 和 `categories` 要以数组的方式填写. 

:::


某些页面的侧边栏为 `false` 呢？因为您启用了分类, 这与自定义侧边栏功能有点冲突, 所以您全局打开自动侧边栏功能, 然后在不需要侧标记的地方关闭它. 

### 添加时间轴

#### 添加导航按钮

```javascript
// change /docs/.vuepress/config.js

module.exports = {
  theme: 'reco',
  themeConfig: {
    nav: [
      { text: 'TimeLine', link: '/timeLine/', icon: 'reco-date' }
    ]
  }    
}  
```

#### 添加所需的文件

**/docs/timeLine/README.md**

```javascript
---
isTimeLine: true
sidebar: false
isComment: false
---

## Time Line
```

#### 写文章时添加日期

```javascript
---
title: 【vue】跨域解决方案之proxyTable  
date: 2017-12-28
tags:
- vue
- webpack
---
```

### 评论 (valine)

带有内置了 valine 评论功能, 如果要打开此功能, 只需配置你的 `config.js`

```javascript
// 更改 /docs/.vuepress/config.js

module.exports = {
  theme: 'reco',
  themeConfig: {
    // valine
    valineConfig: {
      appId: '...',// your appId
      appKey: '...', // your appKey
    }
  }  
}
```

**参数**

|    参数     | 功能                                                         |   默认值   | 是否必填 |
| :---------: | ------------------------------------------------------------ | :--------: | :------: |
|    appId    | 从 LeanCloud 的应用中得到的 appId                            |     无     |   yes    |
|   appKey    | 从 LeanCloud 的应用中得到的 APP Key                          |     无     |   yes    |
| placeholder | 评论框占位提示符                                             | just go go |    no    |
|   notify    | 评论回复邮件提醒, 请参考[配置](https://github.com/xCss/Valine/wiki/Valine-评论系统中的邮件提醒设置) |   false    |    no    |
|   verify    | 验证码服务                                                   |   false    |    no    |
|   avatar    | Gravatar 头像展示方式, 更多信息请查看[头像配置](https://valine.js.org/avatar.html) |   retro    |    no    |
|   visitor   | 文章访问量统计                                               |    true    |    no    |
|  recordIP   | recordIP                                                     |   false    |    no    |



::: tip

如果 valine 的获取评论的接口报 `404` 错误的话, 不用担心, 这是因为你还没有添加评论, 只要存在 1 条评论, 就不会报错了, 这是 `leanCloud` 的请求处理操作而已. 

:::

### 加密功能

#### 项目加密

如果项目具有私密性, 不希望被公开, 只有填入密钥登录后（关闭标签后登录失效）, 才能进入内容页面. 以数组的格式设置 `keys`, 可以设置多个密码, 数组的值必须是字符串. 

```javascript
// 更改 /docs/.vuepress/config.js

module.exports = {
  theme: 'reco',
  themeConfig: {
    // 密钥
    keyPage: {
      keys: ['123456'],
      color: '#42b983', // 登录页动画球的颜色
      lineColor: '#42b983' // 登录页动画线的颜色
    }
  }  
}  
```

#### 文章加密

如果项目是公开的, 而某些文章可能需要加密, 需要在 `frontmatter` 以数组的格式设置 `keys`, 可以设置多个密码, 数组的值必须是字符串. 

```javascript
---
title: vuepress-theme-reco
date: 2019-04-09
author: reco_luan
keys:
 - '123456'
---
```

### Config.js 配置

#### 移动端优化

在移动端, 搜索框在获得焦点时会放大, 并且在失去焦点后可以左右滚动, 这可以通过设置元来优化. 

```javascript
module.exports = {
  head: [
    ['meta', { name: 'viewport', content: 'width=device-width,initial-scale=1,user-scalable=no' }]
  ]
}  
```

#### 图标

您可以在导航菜单中添加图标, 如下所示: 

```javascript
{ text: 'Tags', link: '/tags/', icon: 'reco-tag' }
```

该项目有内置图标供您选择

![icon.png](https://vuepress-theme-reco.recoluan.com/assets/img/icon.50d5f354.png)

#### 备案信息和项目开始时间

```javascript
module.exports = {
  themeConfig: {
    // 备案号
    record: '京ICP备17067634号-1',
    // 项目开始时间, 只填写年份
    startYear: '2017'
  }
}
```

#### 设置作者姓名

- 设置全局作者姓名

```javascript
module.exports = {
  themeConfig: {
    // author
    author: 'reco_luan'
  }
}
```

- 为单篇文章设置作者姓名

```bash
---
title: 你还没真的努力过, 就轻易输给了懒惰
date: 2015-04-23
categories: article
author: 渡渡
---
```

#### 华为文案

如果不希望显示 “华为” 文案, 可以这样关闭. 

```javascript
module.exports = {
  themeConfig: {
    huawei: false
  }
}
```

### 首页配置

主题的主页的默认风格偏文档, 并不像一个博客, 所以从 `vuepress-theme-reco@1.0.0-alpha.25` 开始, 增加博客风格首页布局. 

#### 对比

##### Home

![home.png](https://vuepress-theme-reco.recoluan.com/assets/img/1.441764d7.png)

##### Home-Blog

![home.png](https://vuepress-theme-reco.recoluan.com/assets/img/home-blog.88fbe64e.png)

#### 默认首页配置

##### heroImage

- 如果您的 heroImage 具有您的网站标题, 则可能需要设置值 `isShowTitleInHome` `false` 以使标题不显示. 

```bash
# this is your homepage

---
home: true
heroImage: /hero.png
isShowTitleInHome: false
---
```

- 如果你想改变 heroImage 的风格, 你可以设置值 `heroImageStyle` 来实现你想要的效果

```bash
# 这是你的主页 

---
home: true
heroImage: /hero.png
heroImageStyle: {
  maxHeight: '200px',
  display: block,
  margin: '6rem auto 1.5rem',
  borderRadius: '50%',
  boxShadow: '0 5px 18px rgba(0,0,0,0.2)'
}
---
```

##### 博客风格首页设置

- 指定 `type: 'blog'`

```js
// change /docs/.vuepress/config.js

module.exports = {
  theme: 'reco',
  themeConfig: {
    type: 'blog'
  }  
}  
```

- 设置首页的背景图片和头像

```bash
# 这是你的主页 

---
home: true
bgImage: '/bg.png'
faceImage: '/head.png'
---
```

### 添加摘要

####效果

![2.png](https://vuepress-theme-reco.recoluan.com/assets/img/11.24d12666.png)

#### markdown

![1.png](https://vuepress-theme-reco.recoluan.com/assets/img/12.0f90b412.png)

在 markdown 代码中, 您将看到注释, 注释前面的代码将显示在列表页面上的文章摘要中. 

### vuepress-theme-reco-cli

```bash
npm install vuepress-theme-reco-cli

reco-cli init my-blog

cd my-blog
npm install

npm run dev
```

if yarn, you can do this way:

```bash
yarn add vuepress-theme-reco-cli

reco-cli init my-blog

cd my-blog
yarn install

yarn dev
```



## Github Pages

github pages 分为 2 中

- 个人站点
- 项目站点

2 种站点的 url 有点区别, 在使用 vuepress 部署时遇到点坑, 这里记录一下

### 个人站点

个人站点在创建时, 必须使用 `username.github.io` 作为项目名, 这样不需要其他设置, 只需要 push html 代码即可直接部署

使用 vuepress 时, 不再需要设置 `base`

```javascript
title: "Black House",
  description: '代码千万行, 注释第一行, 编码不规范, 同事两行泪.',
  dest: './docs/dist',
  // 如果使用个人站点, 不需要配置 base
  // base: '/vue-blog/',
  head: [
    ['link', { rel: 'icon', href: '/favicon.ico' }],
    // 在移动端, 搜索框在获得焦点时会放大, 并且在失去焦点后可以左右滚动, 这可以通过设置元来优化
    ['meta', { name: 'viewport', content: 'width=device-width,initial-scale=1,user-scalable=no' }]
  ],
```

### 项目站点

项目站点 push 需要配置 github pages, url 为 `https://username.github.io/projectname/`

这时必须配置 `base`, 不然将导致页面样式错误