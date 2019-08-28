---
title: 'async/await 优雅的异常处理'
date: 2018-09-10 09:55:19
tags:
  - 前端
  - JavaScript
  - ES2017
categories: 学习笔记
---

# async/await 优雅的异常处理

## 背景

1. 在开始公司的新项目时，新来的同事提出目前公司的 ajax 请求处理并不合理 —— 不管什么情况返回结果的 HTTP 状态码都为 `200`; 应改根据不同情况返回不同的 HTTP 状态码。经过讨论，新项目采用上述方式返回处理结果，所以以往项目中根据返回结果的 `errcode` 判断是否错误的做法无法再适用，在接收 `400` 以上的结果时必须 catch.
2. 使用 `Promise.prototype.then().catch();`。
3. 使用 `async/await` 然后使用 `try/catch`。
4. 个人认为使用 `try/catch` 不够优雅。

<!-- more -->

## 有没有优雅的 `async/await` 异常处理？

这个问题有大佬比我先想到

- [How to write async await without try-catch blocks in Javascript](https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/)
- [如何在Javascript中优雅的使用Async和Await进行错误的处理?（译文）](https://blog.csdn.net/xjl271314/article/details/79566447)

还有对上文的引伸

- [从不用 try-catch 实现的 async/await 语法说错误处理](https://segmentfault.com/a/1190000011802045)
- [async/await 如何优美的处理异常？](https://cnodejs.org/topic/5a6857b89288dc8153288136)

## 解决方案

### 1. 方法定义

```javascript
function to(promise) {
    if (!promise || !Promise.prototype.isPrototypeOf(promise)) {
        return new Promise((resolve, reject) => {
            reject(new Error('参数必须是 promise'));
        }).catch((err) => {
            return [err, null];
        });
    }
    return promise.then(data => {
        return [null, data];
    }).catch(err => {
        return [err, null];
    });
}
```

### 2. 如何使用

```javascript
async function f() {
    const [error, data] = await to(...);
    if(error){
        ...
    }
    ...
}
```

## 最后

引用 segmentfault 用户 [边城](https://segmentfault.com/u/jamesfancy) 的结论

> 用什么方式取决于适用场景、团队约定和个人喜好等多种因素，在不同的情况下需要采用不同的处理方式，并不是说哪一种就一定好于另一种——合适的才是最好的！

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/09/10/async-exception-handling/](https://newkami.cn/2018/09/10/async-exception-handling/)**