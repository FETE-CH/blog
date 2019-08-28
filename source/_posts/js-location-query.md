---
title: 'JS 不刷新页面添加或修改 URL query 参数'
date: 2018-12-05 10:23:54
tags:
  - 前端
  - JavaScript
categories: 学习笔记
---

# JS 不刷新页面添加或修改 URL query 参数

在浏览器地址栏添加 query 参数是记住页面中某些信息的一种方式，适用于在某些操作后反复刷新当前页操作后的效果还存在。

<!-- more -->

## 代码实现

添加或更新 query 参数函数：

```javascript
addOrUpdateQuery(key, value, type = 'pushState') {
    let url = type === 'location' ? location.href : location.hash;

    if (!url.includes('?')) {
        url = `${url}?${key}=${value}`;
    } else {
        if (!url.includes(key)) {
            url = `${url}&${key}=${value}`;
        } else {
            const re = `(\\?|&|\#)${key}([^&|^#]*)(&|$|#)`;
            url = url.replace(new RegExp(re), '$1' + key + '=' + value + '$3');
        }
    }

    if (type === 'location') {
        location.href = url;
    }

    if (type === 'pushState') {
        history.pushState({}, '', url);
    }
}
```

## 使用方式

不刷新（适用于前端项目）：

```javascript
addOrUpdateQuery('name', 'Tom');
```

地址栏更改且刷新（使用于一般 web 项目）：

```javascript
addOrUpdateQuery('name', 'Tom', 'location');
```

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/12/05/js-location-query/](https://newkami.cn/2018/12/05/js-location-query/)**
