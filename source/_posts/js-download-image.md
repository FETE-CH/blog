---
title: 'JavaScript 实现前端下载图片'
date: 2019-01-28 14:44:12
tags:
  - 前端
  - JavaScript
  - Base64
categories: 学习笔记
---

# JavaScript 实现前端下载图片

前几天公司的项目有个需求，实现点击下载图片功能，当时我就就想着还不简单嘛，当即写下以下代码：

```javascript
const download = document.createElement('a');
download.setAttribute('download', downloadName);
download.clik();
```

结果出乎意料，浏览器并没有将图片下载，而是将图片在窗口中打开了。我查了下资料，发现像 Chrome 等现代浏览器会识别图片并将其打开。现在下载图片网上大部分做法是响应时添加下载头，浏览器识别到下载头就会将其下载，但是我这前端项目并做不到。那么，问题来了，如何实现前端下载图片？

<!-- more -->

## 1. 实现思路

1. 将图片装换成 **Data URLs**
2. 下载图片

### Data URLs

> Data URLs，即前缀为 `data:` 协议的的 URL，其允许内容创建者向文档中嵌入小文件。

> Data URLs 由四个部分组成：前缀(data:)、指示数据类型的MIME类型、如果非文本则为可选的base64标记、数据本身：
>
> ```
> data:[<mediatype>][;base64],<data>
> ```
>
> mediatype 是个 MIME 类型的字符串，例如 "image/jpeg" 表示 JPEG 图像文件。如果被省略，则默认值为 text/plain;charset=US-ASCII
>
> 如果数据是文本类型，你可以直接将文本嵌入 (根据文档类型，使用合适的实体字符或转义字符)。如果是二进制数据，你可以将数据进行 base64 编码之后再进行嵌入。

详细请看 MDN 对 [Data URLs](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/data_URIs) 的解释。

## 2. 代码实现

以下是获取图片 DataURL 的示例代码：

```javascript
/**
* 获取图片的 base64 编码 DataURL
* @param image JS 图像对象
* @return {string} 已编码的 DataURL
*/
getImageDataURL(image) {
    // 创建画布
    const canvas = document.createElement('canvas');
    canvas.width = image.width;
    canvas.height = image.height;
    const ctx = canvas.getContext('2d');
    // 以图片为背景剪裁画布
    ctx.drawImage(image, 0, 0, image.width, image.height);
    // 获取图片后缀名
    const extension = image.src.substring(image.src.lastIndexOf('.') + 1).toLowerCase();
    // 某些图片 url 可能没有后缀名，默认是 png
    return canvas.toDataURL('image/' + extension, 1);
},
```

执行上面的函数后会返回如下面的格式一样的字符串

```text
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAYAAACtWK6eAAAgAElEQVR4Xu1dB3hUxfb/bc3uppOeQOgIAioKCiqWJ/bysFd89vcXe0WfvSJ2wY6gPhX1WZ...
```

***注意：图片越大，转换后的 base64 编码越长，尽量避免对大图进行 DataURL 转换，防止转换后的长度超出浏览器限制***

以下是下载图片的示例代码：

```javascript
downLoad(downloadName, url) {
    const tag = document.createElement('a');
    // 此属性的值就是下载时图片的名称，注意，名称中不能有半角点，否则下载时后缀名会错误
    tag.setAttribute('download', downloadName.replace(/\./g, '。'));

    const image = new Image();
    // 设置 image 的 url, 添加时间戳，防止浏览器缓存图片
    image.src = url + '?time=' + new Date().getTime();
    //重要，设置 crossOrigin 属性，否则图片跨域会报错
    image.setAttribute('crossOrigin', 'Anonymous');
    // 图片未加载完成时操作会报错
    image.onload = () => {
        tag.href = getImageDataURL(image);
        tag.click();
    };
},
```

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2019/01/28/js-download-image/](https://newkami.cn/2019/01/28/js-download-image/)**