---
title: Linux Server 安装 Nginx 服务器
date: 2017-12-25 9:31:11
tags:
    - Linux
    - Nginx
    - 服务器
    - 负载均衡
categories: 技术教程
reward: false
toc: true
---

Nginx 是一个高性能的 HTTP 和反向代理服务器，也是一个IMAP/POP3/SMTP 服务器。Nginx 是由伊戈尔·赛索耶夫为俄罗斯访问量第二的 Rambler.ru 站点(俄文：Рамблер) 开发的，第一个公开版本0.1.0发布于2004年10月4日。其将源代码以类 BSD 许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。

Nginx 特点是占有内存少，并发能力强。事实上 Nginx 的并发能力确实在同类型的网页服务器中表现较好，国内的一些大型公司都在使用，如：百度、京东、新浪、网易、腾讯、淘宝等。

<!-- more -->

### 安装 Nginx
#### 1. 获取 Nginx

Nginx可在[官网](http://nginx.org/)免费下载。下面使用 `wget` 命令下载

```bash
cd ~/download
wget -c https://nginx.org/download/nginx-1.10.3.tar.gz
```

#### 2. 解压

使用 `tar` 命令解压
```bash
sudo tar -zxvf nginx-1.10.3.tar.gz
```

#### 3. 设置配置信息

设置配置信息前先安装一下所需依赖
```bash
sudo apt-get pcre-devel openssl openssl-devel
```
设置配置信息
```bash
cd nginx-1.10.3
sudo ./configure
```

#### 4. 编译和安装

使用 `make` 命令
```bash
sudo make
sudo make install
```
好了，安装完毕，Nginx 被默认安装在 `/usr/local/` 目录下。

### Nginx 的启动与停止
Nginx的命令在 `/usr/local/nginx/sbin` 目录下。

1. 启动

```bash
sudo ./nginx
```

2. 停止

```bash
sudo ./nginx -s stop
```

3. 刷新

```bash
sudo ./nginx -s reload
```


### Nginx 配置负载均衡
编辑 Nginx 的配置文件
```bash
cd /usr/local/nginx/conf/
sudo vim nginx.conf
```

在 `http` 的大括号内添加如下内容
```conf
upstream app{
    #server 后是 Tomcat 容器的所在的 Ip 地址，weight 是服务器权重
    server 10.0.153.187:8080  max_fails=2 fail_timeout=600s weight=10;
    server 10.0.153.202:8080  max_fails=2 fail_timeout=600s weight=5;
    #如果还有可以继续添加
    #...
    }
```

在 `server` 大括号内的 `location` 内添加
```conf
proxy_pass http://app;
```

启动 Nginx，没有报错就说明配置好了。

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/12/25/install-nginx/](https://newkami.cn/2017/12/25/install-nginx/)**