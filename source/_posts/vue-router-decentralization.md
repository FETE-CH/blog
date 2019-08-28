---
title: 'vue-router 去中心化'
date: 2019-01-11 09:43:00
tags:
  - 前端
  - JavaScript
  - Vue
  - vue-router
  - webpack
categories: 技术教程
---

# vue-router 去中心化

vue-router 是 [Vue.js](http://cn.vuejs.org/) 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。vue-router 的路由配置信息一般统一写在 `src/router.js` 中，这既是优点之一也是缺点之一。优点在于当小型项目或项目初期时，路由中心化可以很好地管理项目中零碎的路由，不用到处查找；但是一旦项目做大做复杂了，中心化的路由反而成了缺点。在成百上千的组件导入和路由信息前，查找具体某个路由真的非常困难，路由去中心化就起了非常重要的作用。

<!-- more -->

## 去中心化实现

### 1. 模块划分路由

将业务以模块划分，不同的模块实现不同的功能，这是大型项目比较理想的架构。同时每个模块只管理对应的路由，既简单又直观，可以很快地找到某个页面对应的路由。比如下图的文件结构：
```
.
├── src
|   ├── modules
|   |   ├── module1
|   |   |   ├── routes.js
|   |   |   └── ...
|   |   ├── module2
|   |   |   ├── routes.js
|   |   |   └── ...
|   |   └── ...
|   ├── router.js
|   └── ...
└── ...
```

在每个模块下的 `routes.js` 文件中编写路由配置，和官方示例中在 `router.js` 中的差不多

```javascript
const about = () => import('./views/About.vue');

export default [
    {
        path: '/about',
        name: 'about',
        component: about,
    },
    ...
];

```

### 2. 路由统一注册

虽然已经将路由配置分散到了响应的模块，但是如果不在 vue-router 中注册是不起作用的。如何将分散在不同模块下的路由配置统一注册呢？在上一篇文章里的 `require.context` 又见面啦~不知道的童鞋可以翻翻咱的上一篇文章，至于具体的使用方式见 [webpack 官网](https://webpack.js.org/guides/dependency-management/#require-context)。

```javascript
let routeList = [];
const context = require.context(`./modules`, true, /routes\.js$/);

context.keys().forEach(r => {
    const {default: routes} = context(r);
    routeList = routeList.concat(routes);
});
```

这里就已经获取到了全部的路由配置了，接下来注册就好了

```javascript
const router = new Router({
    routes: routeList,
});
```

## 总结

其实前端路由的管理一直有争议，中心化和去中心化也各有各的优点和缺点，至于如何择决，这要看个人的意愿和公司的项目架构了。

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2019/01/11/vue-router-decentralization/](https://newkami.cn/2019/01/11/vue-router-decentralization/)**