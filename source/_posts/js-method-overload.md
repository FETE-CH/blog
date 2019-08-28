---
title: 'JavaScript 实现方法重载'
date: 2018-09-24 18:28:59
tags:
  - 前端
  - JavaScript
categories: 学习笔记
---

# JavaScript 实现方法重载

JS 原生并不支持方法重载，如果定义了两个相同名称的方法，总是执行最后定义的那个。但是原生不支持不代表不能实现，毕竟 JS 是一门灵活的动态语言。可以用以下思路实现：

1. 判断参数列表长度
2. 利用闭包特性

<!-- more -->

## 1. 判断参数列表长度

示例代码：

```javascript
function overload() {
    if (arguments.length === 1) {
        // TODO
    }else if(arguments.length === 2){
        // TODO
    }
}
```

## 2. 利用闭包特性

JQuery 之父 John Resig 的书《Secrets of the JavaScript Ninja》（中文译名：JavaScript 忍者秘籍）中提供了一个利用闭包特性的方法。

示例代码：

```javascript
/**
 * 处理函数重载的函数
 * @param object 需要重载的函数所在的对象
 * @param name 需要重载的函数名称
 * @param fn 重载函数的实现，参数列表末位参数是默认参数时，总是执行参数列表 length 为实参列表 length+1 的那个函数
 */
function overloadMethod(object, name, fn) {
    const old = object[name];
    object[name] = function () {
        if (fn.length === arguments.length) {
            return fn.apply(this, arguments);
        } else if (typeof old === 'function') {
            return old.apply(this, arguments);
        }
    };
}
```

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/09/24/js-method-overload/](https://newkami.cn/2018/09/24/js-method-overload/)**