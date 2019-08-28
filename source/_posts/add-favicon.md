---
title: 'Hexo 博客添加浏览器图标'
date: 2017-09-23 10:27:06
tags: 
    - favicon
    - photoshop
    - 浏览器图标
categories: 技术教程
reward: false
toc: true
---

![自制favicon](https://i.loli.net/2017/09/23/59c5d0b403964.png)

许多人可能会想给自己的网站添加好看的浏览器图标(favicon)，而不是像这样的![](https://i.loli.net/2017/09/23/59c5cdd49b625.png)默认图标

怎么样给网站添加图标呢？其实非常简单，只需要几步就好了。

<!-- more -->

### favicon 是什么

在制作之前，先来了解一下favicon。

>所谓favicon，即Favorites Icon的缩写，顾名思义，便是其可以让浏览器的收藏夹中除显示相应的标题外，还以图标的方式区别不同的网站。当然，这不仅仅是Favicon的全部，根据浏览器的不同，Favicon显示也有所区别：在大多数主流浏览器如FireFox和Internet Explorer (5.5及以上版本)中，favicon不仅在收藏夹中显示，还会同时出现在地址栏上，这时用户可以拖曳favicon到桌面以建立到网站的快捷方式；除此之外，标签式浏览器甚至还有不少扩展的功能，如FireFox甚至支持动画格式的favicon等。

以上摘自[favicon.ico在线制作](http://www.faviconico.org/)

知道了什么是favicon，就正式开始制作吧~

### 使用 Photoshop 制作图标

打开photoshop

![photoshop启动界面](https://i.loli.net/2017/09/23/59c5d2bcab5ca.png)

新建一个正方形文档，尺寸随意，但不要过大，其他的默认设置。

![新建文档](https://i.loli.net/2017/09/23/59c5d5b288332.png)

然后就开始制作你自己的图标吧。

完成后先把psd保存下来，以便以后会用再次用到，再保存图片为`.png`格式。

**为什么要保存为png？**

我们先来看看这两个图标，![某神社](https://i.loli.net/2017/09/23/59c5d82c9ef0f.png)和![全国最大的同性交友网站](https://i.loli.net/2017/09/23/59c5d82c9fc45.png)有什么不同之处。

我的浏览器的主题是蓝色的，第二个图标能透出浏览器的颜色，第一个则不能。说明第二个图标的背景颜色是透明的，第一个的背景颜色则为白色。

png是支持透明度的格式，想让你的图标呈多边形甚至不规则形态，就得背景色透明且保存为png格式。

### 生成favicon.ico

其实用photoshop也是可以保存为ico格式的，但是必须下载插件。但是网上的插件大部分都是很久以前的版本的，新版本的photoshop不支持，所以这里使用[favicon.ico在线制作](http://www.faviconico.org/)这个网站来在线生成ico图标。

进入[favicon.ico在线制作](http://www.faviconico.org/)，选择你制作好的png图片，点击生成

![](https://i.loli.net/2017/09/23/59c6119010812.png)

生成好了之后点击下载就可以得到你的ico图标啦~

![](https://i.loli.net/2017/09/23/59c61269951e0.png)

### 添加favicon.ico到Hexo博客

将得到的ico图标重命名为`favicon.ico`。利用ftp上传到`blog/themes/你使用的主题/source`目录下，如图所示：

![](https://i.loli.net/2017/09/23/59c6154d55a79.png)

然后部署到Github

```bash
hexo d
```

现在用浏览器打开你的网站，就能在标签栏和收藏夹看到你网站的图标啦~

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/09/23/add-favicon/](https://newkami.cn/2017/09/23/add-favicon/)**
