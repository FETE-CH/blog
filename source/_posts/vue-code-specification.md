---
title: '前端 Vue 开发规范'
date: 2018-11-27 11:06:44
tags:
  - JavaScript
  - 前端
  - Vue
categories: 技术教程
---

# 前端 Vue 开发规范

人人都有自己的开发习惯，但是各异的开发习惯不利于团队项目协作，所以制定此代码规范。本规范根据我和团队成员的个人习惯所制定，可能并不适用所有人。

<!-- more -->

## 1. 开发工具

- 统一使用 WebStorm

## 2. 代码规范

- 变量名能够顾名思义，使用驼峰命名法。
- 代码末尾必须加分号
- 代码缩进使用 4 个 **空格**
- CSS 使用 SCSS，必须添加 `scoped` 属性，如有必要使用全局 CSS，写在组件根元素的类名中
- 变量和方法必要时添加简要注释，工具函数添加 JSDoc 注释
- vue `template` 部分在一行长度允许下尽量使用闭合单标签，条件属性（`v-if/v-esle`）始终放在第一位，使用组件时属性换行。
- vue `script` 部分钩子顺序如下
    1. `name`（必要）
    2. `components`
    3. `mixins`（如有）
    4. `data`
    5. `props`
    6. `model`
    7. `computed`
    8. `watch`
    9. `methods`
    10. 生命周期钩子函数
- 使用 `async/await` 代替  `promise`
- 并发时使用 `promise.all`

## 3. 工具设置

### 1. WebStorm

拉取最新的代码，使 `.editorconfig` 文件与下面相同

```
[*]
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = false
insert_final_newline = true
indent_style = space
indent_size = 4

[{*.yml, *.json}]
indent_size = 2
```

点击设置 > 编辑器 > 代码样式，将 `Enable EditorConfig support` 勾选上，这会使得 `editorconfig` 的配置覆盖 IDE 的设置，点击 OK 确认生效。

![editorconfig](https://i.loli.net/2018/11/27/5bfd0b12b23b8.png)

之后写完代码只需要按 `command + option + L` 即可格式化代码。

### 2. VS code

至于 vs code 请自行搜索。

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2018/11/27/vue-code-specification/](https://newkami.cn/2018/11/27/vue-code-specification)**