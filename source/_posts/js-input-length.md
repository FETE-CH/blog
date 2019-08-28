---
title: 'JS 限制输入框输入字符长度，中文算 2 个字符，英文算 1 个字符，总字符长度不超过 N'
date: 2018-05-19 22:57:02
tags:
  - 前端
  - JavaScript
categories: 学习笔记
---
# JS 限制输入框输入字符长度，中文算 2 个字符，英文算 1 个字符，总字符长度不超过 N

前端时间公司的项目需求里有这个需求，当时改了好几遍，总是有 bug，现在当做笔记记录下来。如果中英文字符都当做一个字符数，只需设置 `input` 标签的 `maxlength` 属性就行了，但是分开算时，总长度就不确定了，需要动态计算。

<!-- more -->

## 实现思路

1. 为输入框添加事件，可以是 `keyup` 或 `input` 事件，`keydown` 和 `keypress` 事件不能监听到中文的输入
2. 计算已输入的字符串的『字符数长度』，中文占 2 位，英文占 1 位
3. 计算字符串『字符数长度』达到 N 或 N-1 时，字符串字符的个数（位数）`n`
4. 截取长度为 `n` 的字符串

## JavaScript 代码

示例代码：

```javascript
limitLength(e) {
    const input = e.target;
    const split = input.value.split('');
    const map = split.map((s, i) => (input.value.charCodeAt(i) >= 0 && input.value.charCodeAt(i) <= 128) ? 1 : 2);

    const maxLength = 32;
    let n = 0;
    const charLength = map.length > 0 && map.reduce((accumulator, currentValue, index) => {
        if (accumulator === maxLength || accumulator === maxLength - 1) {
            n = index;
        }
        return accumulator + currentValue;
    });
    if (charLength > maxLength) {
        input.value = split.slice(0, n).join('');
    }
}
```

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/05/19/js-input-length/](https://newkami.cn/2018/05/19/js-input-length/)**