---
title: 'Re：从零开始的博客搭建'
date: 2017-09-13 14:02:06
tags: 
    - Hexo
    - 博客搭建
    - Github
    - Linux
    - Markdown
categories: 技术教程
reward: true
toc: true
---

花了一个多星期，个人的博客总算是搭建完了，这算是这个博客第一篇正式的文章吧。

看到同学有个人博客，心里痒痒的，想着也搞一个去，但是网上的博客都是在别人服务器上，感觉太被动，我不喜欢。就去百度了下建站的过程，好像...并不难的样子![](https://i.loli.net/2017/09/15/59bbc012dd836.png)于是，开搞！

期间踩了无数坑，百度和谷歌了无数次，软件装了又卸，卸了又装，连服务器系统都重装3次![](https://i.loli.net/2017/09/15/59bbc1e2779e5.jpg)算是对的起标题的 "RE" 了。写这篇文章是打算记录下我搭建的过程，也算是个备忘录吧，个人的记性实在是不好，不知道什么时候就忘了![](https://i.loli.net/2017/09/15/59bbc012dd836.png)

<!-- more -->

### 搭建的事前准备
1. 一个独立域名
2. 一台linux计算机，不管是自己的还是别人的（服务器空间）
#### 购买域名
购买域名的目的是人别人更好的记住你的博客，虽然Hexo静态博客不绑定自己的域名也可以通过Github分配给你的永久连接访问,个人建议还是买一个独立域名，说不定别人就被你与(diao)众(zha)不(tian)同的域名吸引了呢~

域名首选以.com为后缀的，国际通用，简单好用，如何购买请自行百度或谷歌“域名购买”或“域名注册”，网上有非常多非常详细的教程，这里就不叙述了。
#### 购买服务器空间
由于Hexo博客是部署在Github的仓库上的，可以不使用服务器，但是我这里的小水管基本等同于没有，更重要的在我疯狂百度时领到了一张tx的代金券，so...就买了tx的云服务器，还送了个一年的域名![](https://i.loli.net/2017/09/15/59bbc012dd836.png)

所以，不想增加开销的可以跳过这步，使用自己的电脑搭建，但是需要用到Node.js，所以

<font color=#FF3C63 size=6 face="黑体">
不要使用Windows！！！

不要使用Windows！！！

不要使用Windows！！！
</font>

Windows版的Node.js根本就是残疾版，别问我什么知道...![](https://i.loli.net/2017/09/15/59bbc1e2779e5.jpg)

回归正题，服务器空间有一般有以下几种
- 虚拟主机
- VPS
- 云主机
- 独立服务器

想要了解各个的优缺点及差异，可以看看下面的介绍

[虚拟主机、VPS和云服务器的特点与差异性？](https://www.zhihu.com/question/31909406) ———— 知乎

[全面解析：虚拟主机、独立服务器、VPS及云主机](http://www.enkj.com/idcnews/Article/20140916/6110) ———— 亿恩IDC资讯

我买的就是属于云主机，多亏是云主机，让我省了不少事。

还有，构架在大陆的且用于网站服务器的空间是一定要备案的，不想麻烦的话可以购买香港或国外的空间。但是这里我们并不是用于网站的服务器作用，只是将markdown解析成静态html页面，然后再部署到github仓库上，所以并不需要备案。

服务器空间的选择实在是太多了，个人也没什么推荐，只知道亚马逊AWS可以免费使用一年，其他的可自行百度或谷歌。


### 正式开始
接下来就正开始我们的Hexo博客搭建。
#### 服务器设置
有了服务器之后需要对服务器进行一系列设置。

首先，重装系统，一个合适的系统会让你的操作更加得心应手。

![](https://i.loli.net/2017/09/16/59bc78e293aba.png)

选择合适自己的操作系统，我选了Debian 9，因为习惯了了Ubuntu，`apt-get` 命令实在是太方便了。重装系统后，root的密码是不变的，这点谨记。

设置安全组，让必要的端口暴露出来。tx的云主机为了安全,设置了安全组，只有在安全组之内的端口才能够使用。

![](https://i.loli.net/2017/09/16/59bc7c8e1bc9e.png)

关于更多安全组的信息，请参见 [帮助与文档](https://www.qcloud.com/document/product/416/7596) ———— 腾讯云

#### 远程登录服务器空间
一般使用SSH远程登录到服务器，Winows下常用的远程登录软件有PuTTY和Xshell，前者为自由软件，后者为商业软件，可以根据自己的喜好选择，这里选用Xshell作为介绍，PuTTY也是大同小异。

打开Xshell,新建会话

![](https://i.loli.net/2017/09/16/59bd46eda7e04.png)

确定之后就可以使用可以空间商给你提供的账号和密码登录了。

#### 创建新用户
一般来说，不建议使用root用户来搭建Hexo博客，很有可能会发现不可预料的错误，甚至是只有你才会发生的问题，别人都没有，百度和谷歌都找不到解决办法。为了避免这些问题，我们需要一个不是root用户但是有管理员权限的用户。

在root用户下，新建git用户(用户名可以自己取，但是要好记)
```bash
adduser git
```
修改系统文件权限
```bash
chmod 740 /etc/sudoers
```
使用Vim编辑器打开
```bash
vim /etc/sudoers
```
找到
```bash
# User privilege specification
root    ALL=(ALL:ALL) ALL
```
在这下面添加一行
```bash
git ALL=(ALL) ALL
```
保存后退出，更改回原来的权限
```bash
chmod 400 /etc/sudoers
```
然后为你的新用户设置密码
```bash
passwd git
```
好了，一个有权限的用户就新建完成了。
#### Hexo环境配置
切换git用户，后面的操作都在git下进行，遇到需要权限的只需要在前面加上 `sudo` 就行
```
su git
```
安装 Node.js 8
```bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```
安装 Git
```bash
sudo apt-get install git-all
```
#### Hexo安装及初始化
安装完上面的必要软件后就可以安装Hexo了。使用npm命令安装Hexo。
```bash
sudo npm install -g hexo-cli
```
如果出现报错，请尝试这些方法：[https://github.com/hexojs/hexo/issues/2505](https://github.com/hexojs/hexo/issues/2505)

选择初始化Hexo的目录，一般目录都会选择自己的家目录下。进入家目录
```bash
cd ~
```
初始化Hexo，Hexo将会在指定目录下新建所需要的文件，`<folder>` 即目录名。
```bash
hexo init <folder>
cd <folder>
sudo npm install
```
一般都会用 blog 作为目录名，所以
```bash
hexo init blog
cd blog
sudo npm install
```
新建完成后，blog的目录结构如下
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
如果你的目录结构和上面示例的一样，说明正确初始化了，如果发现目录或文件不全，说明Hexo没正确初始化，请删除整个blog目录重新初始化。

#### Hexo 博客预览
要想本地预览Hexo博客，必须安装本地服务器。Hexo 3.0 把服务器独立成了个别模块，必须先安装 `hexo-server` 才能使用。
```bash
cd blog
npm install hexo-server --save
```
**注意，不加 `--save` 会安装失败！**

启动Hexo本地服务
```bash
hexo server
```
如果发现不能启动服务，请返回上面仔细检查安全组内是否有`4000`端口，或者检查端口是否打开了，或者检查端口是否被其他程序占用！

启动服务之后可以使用 [http://localhost:4000](http://localhost:4000) 访问你的Hexo静态博客了。由于我们是在服务器空间上，所以把 `localhost` 换成 `你空间的公网IP` 就可以了。例如：[http://192.168.0.1:4000](http://192.168.0.1:4000)

怎么样？是不是很激动？有种<del>欣(da)喜(le)若(ji)狂(xue)</del>的感觉？别急，还没完成呢![](https://i.loli.net/2017/09/15/59bbc012dd836.png)

#### 写第一篇博文
博客已经成型，来写第一篇自己的博文吧。使用下面的命令创建新的文章，这会生成一个Markdown文件，默认在 `blog/sources/_posts` 目录下
```bash
hexo new "你好，Hexo"
```
使用Vim编辑它
```bash
vim /sources/_posts/你好，Hexo.md
```
然后使用下面的命令生成静态文件
```bash
hexo generate
```
当然，是可以简写的
```bash
hexo g
```
启动服务
```bash
hexo server
```
在浏览器输入地址，就可以看到自己写的文章啦~

#### 配置Github
接下来，要将Hexo部署到Guthub仓库上，让别人也可以访问你的博客，成为一个真正的网站。首先，需要进行配置。

1. 注册Github账号

	打开[Github](https://github.com/),申请一个账号，有账号的就可以跳过啦~

2. 新建仓库

	注册完成后登录Github，点击右上角的"＋"，d点击"New repository"

	![](https://i.loli.net/2017/09/19/59c0d32b556be.png)

	进去后，填写格式如下，把 `yourusername` 替换成的你用户名。

	**注意：格式一定是要是 `yourusername.github.io` 不然后面的步骤会报错！！！**

	![](https://i.loli.net/2017/09/19/59c0d6189e784.png)

3. 告诉Git你是谁

	要想部署到Github，Git必须先知道你是谁。
	```bash
	git config --global user.name"你的Github用户名" 
	git config --global user.email"你注册Github时填的邮箱"
	```

4. 设置部署路径

	进入blog目录，编辑配置文件`_config.yml`
	```bash
	cd blog
	vim _config.yml
	```
	拉到最后，找到 `deploy:` ，修改如下，把 `youruesername` 替换成你的Githu用户名
	```bash
	deploy: 
		type: git
		repo: https://github.com/yourusername/yourusername.github.io.git
		branch: master
	```
	**注意：配置文件中的内容，":"后面都有个空格，不能省略，不然会报错！！！**

#### 部署到Github
开始正式将Hexo部署到Github，完成这一步，一个真正的博客网站就搭建好了。

清除缓存文件和已生成的静态文件
```bash
hexo clean
```
生成静态文件
```bash
hexo generate
```
部署到Github
```bash
hexo deploy
```
合并简写
```bash
hexo d -g
```
或者
```bash
hexo g -d
```
这两个命令都是可以的。然后会提示你输入你的Github账号和密码，输入后没有任何报错，就证明部署好了。接下来在浏览器输入Github给你供的永久连接[http://yourusername.github.io](http://yourusername.github.io)就能进入你的博客啦~~这个连接除非Github服务器出问题了，不然是永久有效的。

不容易啊！快要泪流满面的对不对？我当初建好的时候可是大叫了出来，旁边的人看我眼神都不对了(玛德，zz！)![](https://i.loli.net/2017/09/15/59bbc1e2779e5.jpg)

### 域名绑定
虽然可以通过上面的永久链接访问你的博客，但是这一点都不个性化，也很繁琐，让你的博客有个自己独立的域名，是十分必要的。
#### 域名解析
进入你购买的域名的域名管理，选择解析

![](https://i.loli.net/2017/09/19/59c110a7c36d0.png)

然后点击"添加记录"，会有提示

![](https://i.loli.net/2017/09/19/59c111a9089b6.png)

这里要说下，这里记录类型有人填"A",也有人填"CNAME"。A类型就是填你的永久链接指向的IP地址，CANME类型就是直接填你的永久链接。但是，*Github分配给你的IP地址有可能会改变，但是链接是不会变！*个人建议还是填CNAME类型。这里就把两种都叙述下。

1. A 类型

	使用 `ping` 命令获得Github分配给你的ip地址
	```bash
	ping yourusername.github.io
	```
	例如：

	![](https://i.loli.net/2017/09/19/59c11486051d0.png)

	我这里获得的ip就是 `151.101.77.147` 

	添加两条记录，一条主机类型为"@"，一条主机类型为"www"

	![](https://i.loli.net/2017/09/19/59c1161dab7d0.png)

	![](https://i.loli.net/2017/09/19/59c1161dac9f2.png)

2. CNAME 类型

	直接添加两条记录，同样也是一条主机类型为"@"，一条主机类型为"www"

	![](https://i.loli.net/2017/09/19/59c116f609bcd.png)

	好了，这样记录就添加完成。

	然后修改DNS服务器，将DNS1，DNS2分别修改为
	```
	f1g1ns1.dnspod.net
	f1g1ns2.dnspod.net
	```

	域名解析就完成了，解析的域名会在10分钟甚至更长的时间内生效，请稍安勿躁。在等待的时候可先进行下面的操作。

#### 添加CANME
如果，你的域名解析完成，这时候你在浏览器输入你的域名想看看自己的博客，然而你会发现

![](https://i.loli.net/2017/09/19/59c11a8d872cc.png)

并不能进入自己的博客。这时候还需要进行一步操作。

在`blog/source`目录下,新建一个文件`CNAME`文件，*注意：文件名大写，没有后缀！*
```bash
cd blog/source/
vim CNAME
```
内容就是你的域名，*注意：不用加 `www` ！*

例如，我的域名
```
newkami.cn
```

然后更新到Gitub
```bash
hexo clean
hexo g -d
```

如果你的域名解析正常，就可以看的你的博客啦~如果显示无法访问此网站，则是你的域名还没解析完成，请耐心等待，如果显示的是Github 404界面，则是你的CNAME或其他的问题，请仔细检查上面的步骤！

### 博客性能优化
作为一个强迫症患者，看着自家的网站用着http协议被浏览器标识为"不安全"，简直不能再不爽了。想着我也去搞了个SSL证书，让自己的博客支持HTTPS协议。突然发现tx竟然能免费申请SSL证书！大喜！

![](https://i.loli.net/2017/09/20/59c2621570fb8.png)

经过4-5天漫长的等待，证书终于审核好了

![](https://i.loli.net/2017/09/20/59c2630fda4e9.png)

然后我就急忙着去配置了，结果提示

![](https://i.loli.net/2017/09/20/59c263903ff25.png)

诶？咋就这么麻烦呢?!要接入那咱就接入吧，心想着，结果，来了这么一出

![](https://i.loli.net/2017/09/20/59c264af8cbe6.png)

哇，tx你这是在玩我吧![](https://i.loli.net/2017/09/15/59bbc1e2779e5.jpg)

奈何Github仓库并不能备案，这条路也就彻底断了。在我快把百度和谷歌查烂的时候，发现了Cloudflare。

>Cloudflare以向客户提供网站安全管理、性能优化及相关的技术支持为主要业务。通过基于反向代理的内容传递网络(ContentDeliveryNetwork,CDN)及分布式域名解析服务(DistributedDomainNameServer)，Cloudflare可以帮助受保护站点抵御包括拒绝服务攻击(DenialofService)在内的大多数网络攻击，确保该网站长期在线，同时提升网站的性能、访问速度以改善访客体验。

以上摘自百度百科。

于是，我就着手把域名接入了Cloudflare。

1. 注册Cloudflare

	进入[Cloudflare](https://www.cloudflare.com)，注册一个账号

2. 解析域名

	注册完成后，填写你的域名，让Cloudflare去解析

3. 跟改DNS服务器

	然后，Cloudflare会要求你修改DNS，让你的网站的请求流至Cloudflare。进入你的域名管理，把DNS服务器修改成Cloudflare提供的DNS，当看到

	![](https://i.loli.net/2017/09/20/59c26c915b18c.png)

	你的域名就成功接入Cloudflare了。

4. 开启https
	在上面的功能区里，选择"加密"

	![](https://i.loli.net/2017/09/20/59c26d8fb3c30.png)

	将"总是使用HTTPS"设为开启

	![](https://i.loli.net/2017/09/20/59c26dcddad11.png)

	至此，你的网站就成功开启了https了，进入你的网站浏览器是这样显示的

	![](https://i.loli.net/2017/09/20/59c26eb593aae.png)

	怎么样？看见有个小绿锁是不是有种安心的感觉呢？


### 写在后面

从开始写到现在，过了好几天了，期间因为其他的事，写写停停，总算写完了。如果这篇文章哪怕有帮到你一点点，也算有意义了。

**第一次写教程，如有错误，请斧正！**

### 参考资料
[通过包管理器安装Node.js](https://nodejs.org/en/download/package-manager/)

[入门 - 安装Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

[Hexo文档](https://hexo.io/zh-cn/docs/index.html)

[博客从WordPress迁移到Hexo](https://segmentfault.com/a/1190000005624504#articleHeader9)

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/09/13/re-build-blog/](https://newkami.cn/2017/09/13/re-build-blog/)**
