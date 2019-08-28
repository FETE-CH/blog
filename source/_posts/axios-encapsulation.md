---
title: 'Axios 实用封装'
date: 2019-01-02 15:11:01
tags:
  - 前端
  - JavaScript 
  - AJAX
  - axios
  - webpack
categories: 技术教程
---

# Axios 实用封装

Axios 在 vue 官方推荐后被越来越多的人使用，但是不得不说在项目直接使用 axios 实在是繁琐，每次都要写一堆的配置，而且将配置写在业务代码中十分地不雅，一般在项目中使用的时候都会将其再次封装，以便更好的使用。但是在网上找了一圈，都没发现让我满意的方案，不是不优雅，就是根本不实用。于是我自己实现了个简单但是非常实用的封装。

<!-- more -->

## 实现思路

我个人理想中的请求方案如下：

1. 实际请求与请求配置解耦
2. 请求配置可配置
3. 请求配置集中管理
4. 请求配置去『中心化』

可能你们会问，什么叫集中管理但去『中心化』？集中管理不难理解，但是如果集中到一个文件的话，可能项目初期并没有什么问题，但是一旦项目做大了，这个文件对于维护者来说简直就是灾难，所以需要将一个文件分成多个文件『集中』管理。

但是划分的规则是什么？以功能模块划分。这个在我们公司的项目中已经有了很好的实践，以下是文件结构：

```
.
├── src
|   ├── modules
|   |   ├── module1
|   |   |   ├── apis.js
|   |   |   └── ...
|   |   ├── module2
|   |   |   ├── apis.js
|   |   |   └── ...
|   |   └── ...
|   ├── apis.js
|   └── ...
└── ...
```

我们的封装就  写在 `src/apis.js` 文件中。

###  添加 axios 基本配置

具体配置项的意思这里就不写了，详见官网。`baseURL` 可根据公司的情况和环境变量来注入不用的值。

```javascript
import axios from "axios";

const Axios = axios.create({
    baseURL: "/",
    responseType: "json",
    withCredentials: true,
    headers: {
        Accept: "application/json",
        "Content-Type": "application/json"
    },
    validateStatus: status => {
        return (status >= 200 && status < 300) || status === 304;
    }
});
```

### 添加请求拦截器

请求拦截器中可以做一些骚操作，这里就简单地添加 `token`。

```javascript
Axios.interceptors.request.use(
    config => {
        /*FIXME*/
        // 此处根据公司情况替换获取 token 的方式
        const token = "xxx";
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
    },
    error => {
        return Promise.reject(error);
    }
);
```

###  获取不同模块下的 apis

`require.context` 是 webpack 动态引入文件的方法，如果你们公司不使用 webpack 进行打包，可使用 node 的 `fs` 模块。`require.context` 具体的使用方式见 [webpack 官网](https://webpack.js.org/guides/dependency-management/#require-context)

```javascript
let apis = {};
const context = require.context(`./modules`, true, /apis\.js$/);
context.keys().forEach(key => {
    const { default: api } = context(key);
    apis = Object.assign(apis, api);
});
```

### 封装发送请求方法

直接上代码：

```typescript
Object.keys(apis).forEach((key: string) => {
    const config = apis[key];
    
    /**
     * 实际发送请求的方法
     * @param restful   restful 参数，在使用 restful 风格的 URL 时需要
     * @param params    请求参数
     * @return {Promise}
     */
    function request(restful: object, params: object) {
        if (!config.transform) {
            if (config.restful) {
                const match = config.url.match(/(?<=\{)[^\}]+/g);
                if (match && match.length > 0) {
                    match.forEach((str: string) => {
                        if (!restful || (typeof restful) !== 'object' || !Object.keys(restful).includes(str)) {
                            let cancel = (message: string) => {
                            };
                            config.cancelToken = new CancelToken((c) => cancel = c);
                            cancel(`${key} 请求中 ${str} 参数未注入`);
                        } else {
                            config.url = config.url.replace(`{${str}}`, restful[str]);
                        }
                    });
                    config.transform = true;
                } else {
                    let cancel = (message: string) => {
                    };
                    config.cancelToken = new CancelToken((c) => cancel = c);
                    cancel('你似乎并不需要 restful 风格，请删除 restful:true，或赋值为 false');
                }
            }
            const parameter = !params ? restful : params;
            config.method = config.method || 'get';
            if (config.method === 'get' || config.method === 'delete') {
                config.params = parameter;
            } else if (config.method === 'post' || config.method === 'put' || config.method === 'patch') {
                config.data = parameter;
            }
        }
        return Axios.request(config);
    }
    apis[key] = request;
});

```

这里解决一个痛点：axios 不支持 restful。至少我翻了官网没看到有支持 restful，如果有哪位大佬知道 axios 原生支持 restful 的方法，请评论或邮件指导，感谢。

### 完整代码

完整代码可以看我的 github 上的[项目](https://github.com/FETE-CH/vue-falsework)。

## 如何使用

### 1. 编写请求配置

在各个模块下的 `apis.js` 文件中写入以下配置：

```javascript
export default {
    getTest: {
        // 请求的 url
        url: '/user/{id}',
        // 请求的方式，默认是 get 方式，可不写
        method:'get'
        // 是否支持 restful
        restful: true
    },
```

如果有请求的域名和基本配置中的不一致的，可以使用绝对 URL，记得带上网络协议

### 2. 引入封装后的 axios

在 `main.js` 中引入，并添加到 Vue 原型和 window 对象上，这样可以在 vue 实例和全局都可以使用

```javascript
import apis from './apis';

Vue.prototype.$apis = apis;
window.apis = apis;
```

### 3. 发送请求

在 vue 文件中可直接使用 `this.$apis`，如果 vue 实例不存在则可以使用 `window.apis`  

```javascript
this.$apis.getTest({userId: 1, id: 2}, {name: 'Tom'}).then((data: any) => {
    console.log(data);
}).catch((e: any) => {
    Promise.reject(e);
});
```

### 4. 让请求更加 hack

虽然使用 `Promise` 可以简单地获取数据和捕获错误，但是如果使用 `ES7` 中的 `async` 函数却不得不 `try/catch`，这里使用大佬的提供一个更加 hack 的方式，详见 [How to write async await without try-catch blocks in Javascript](https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/)

```javascript
async getTestData() {
    const [error, data] = await to(this.$apis.getTest({userId: 1, id: 2}, {name: 'Tom'}));
    if(error){
        ...
    }
    ...
}
```

## 最后

由于本人经验尚浅，可能存在一些 bug，如果发现请及时提出，与君共勉，谢谢。

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2019/01/02/axios-encapsulation/](https://newkami.cn/2019/01/02/axios-encapsulation/)**