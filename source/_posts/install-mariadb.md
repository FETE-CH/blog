---
title: Linux Server 安装 MariaDB
date: 2017-11-21 10:41:25
tags: 
    - Linux
    - Debian
    - Java
    - MariaDB
    - MySQL
    - 关系型数据库
categories: 技术教程
reward: false
toc: true
---

作为一个开发人员，数据库的使用自然少不了，MySQL、Oracle 等等都是常用的关系型数据库，Oracle 是收费数据库，大部分中小型企业和个人开发者都更愿意使用免费、开源的MySQL。

但是自从 MySQL 被 Oracle 公司收归旗下之后，有将 MySQL 闭源收费的潜在风险和趋势。为了避免这个风险，开发者们纷纷转向由 MySQL 之父 Widenius 主导开发的分支版本——MariaDB。

<!-- more -->

### 关于 MariaDB
MariaDB 数据库管理系统是 MySQL 的一个分支，主要由开源社区在维护，采用 GPL 授权许可 MariaDB的目的是完全兼容 MySQL，包括 API 和命令行，使之能轻松成为 MySQL 的代替品。在存储引擎方面，使用 XtraDB 来代替 MySQL 的 InnoDB。MariaDB 由 MySQL 的创始人Michael Widenius 主导开发，他早前曾以10亿美元的价格，将自己创建的公司 MySQL AB 卖给了 SUN，此后 SUN 被甲骨文收购，MySQL 的所有权也落入 Oracle 的手中。

MariaDB 名称来自 Michael Widenius 的女儿 Maria 的名字。

### 安装 MariaDB

**注意：MariaDB 和 MySQL不能共存，如果已经安装了 MySQL 请先卸载！！！**

1. 安装 MariaDB 服务

    ```bash
    sudo apt-get mariadb-server mariadb-client
    ```
    确认后会开始下载和安装 MariaDB 和所需依赖。

    安装完成后，使用一下命令查看 MariaDB 的版本
    ```
    sudo mysql -v
    ```


2. 运行 MariaDB 安全配置向导

    ```bash
    sudo mysql_secure_installation
    ```
    会出现下面的信息

    ```
    NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
    SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

    In order to log into MariaDB to secure it, we'll need the current
    password for the root user.  If you've just installed MariaDB, and
    you haven't set the root password yet, the password will be blank,
    so you should just press enter here.

    Enter current password for root (enter for none): #设置root的密码，设置为你自己的密码
    OK, successfully used password, moving on...

    Setting the root password ensures that nobody can log into the MariaDB
    root user without the proper authorisation.

    You already have a root password set, so you can safely answer 'n'.

    Change the root password? [Y/n] #是否改变密码，选择 n 不改变
    ... skipping.

    By default, a MariaDB installation has an anonymous user, allowing anyone
    to log into MariaDB without having to have a user account created for
    them.  This is intended only for testing, and to make the installation
    go a bit smoother.  You should remove them before moving into a
    production environment.

    Remove anonymous users? [Y/n] #是否移除测试用户，选中移除
    ... Success!

    Normally, root should only be allowed to connect from 'localhost'.  This
    ensures that someone cannot guess at the root password from the network.

    Disallow root login remotely? [Y/n]  #是否允许 root 远程登录，根据实际情况选择
    ... Success!

    By default, MariaDB comes with a database named 'test' that anyone can
    access.  This is also intended only for testing, and should be removed
    before moving into a production environment.

    Remove test database and access to it? [Y/n] #删掉测试数据库 test，不需要的可以删掉
    - Dropping test database...
    ... Success!
    - Removing privileges on test database...
    ... Success!

    Reloading the privilege tables will ensure that all changes made so far
    will take effect immediately.

    Reload privilege tables now? [Y/n] #重新加载权限表，选择重新加载
    ... Success!

    Cleaning up...

    All done!  If you've completed all of the above steps, your MariaDB
    installation should now be secure.

    Thanks for using MariaDB
    ```

3. 更改默认配置文件

    输入
    ```bash
    sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
    ```
    找到第29行 `bind-address=127.0.0.1` ,将其注释，以开启外网访问
    ```
    # Instead of skip-networking the default is now to listen only on
    # localhost which is more compatible and is not less secure.
    #bind-address=127.0.0.1 #在这行前面加上#号，注释这一行
    ```
    保存后退出，重启服务，使配置生效
    ```
    sudo systemctl restart mysql
    ```

### 常用 MariaDB 服务命令

1.启动 MariaDB 服务
```bash
sudo systemctl start mysql
```

2.重启服务
```
sudo systemctl restart mysql
```

3.查看服务状态
```
sudo systemctl status mysql
```

4.设置mysql随系统服务启动
```
sudo update-rc.d  mysql defaults 
```

5.撤销随系统服务启动

```
sudo update-rc.d  -f mysql remove  
```

**注意：与mysql不同的是，MariaDB 需要获得操作系统管理员权限，才能登录数据库的 root 用户**

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/11/21/install-mariadb/](https://newkami.cn/2017/11/21/install-mariadb/)**