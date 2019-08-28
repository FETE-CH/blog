---
title: Linux Server 安装 JDK8
date: 2017-11-20 9:02:56
tags: 
    - Linux
    - Debian
    - Java
    - JDK
categories: 技术教程
reward: false
toc: true
---

JDK 是 Java 语言的软件开发工具包，主要用于移动设备、嵌入式设备上的 java 应用程序。JDK 是整个java开发的核心，它包含了JAVA的运行环境（JVM+Java系统类库）和JAVA工具。

作为 Java 开发人员，在 Linux Server 下安装一些开发工具是必备技能，此文章详细记录了安装 JDK 的每一步的操作命令，以供以后参考。

<!-- more -->
### 卸载 OpenJDK

**注意：本博客使用 `apt-get` 命令！！！**

实际生产环境中基本都使用官方的 JDK,但是有些系统自带了 OpenJDK，需要先卸载。

1. 查看当前是否安装了 JDK

    ```bash
    java -version
    ```
    如果显示了 JDK 名称和版本信息，表示当前系统存在 OpenJDK；如果提示未找到命令，则可以直接进行安装。

2. 卸载 OpenJDK

	```bash
    sudo apt-get remove openjdk*  
  ```
	卸载完毕即可进行JDK安装。

### 安装官方 JDK8

#### 1. 下载 JDK8

	进入官网 [Java SE Development Kit 8 Downloads](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 下载合适版本的 JDK。
	
	![JDK版本](https://s1.ax2x.com/2017/12/08/T7o0H.png)

#### 2. 安装 JDK8

1. 利用FTP工具将下载好的文件上传到服务器中，解压文件
```bash
sudo tar -xzvf jdk-8u151-linux-x64.tar.gz
```

2. 将解压后的目录移动到根目录的 `opt` 目录下
```bash
sudo mv jdk-8u151 /opt
```

3. 进入 `opt` 目录下
```bash
cd /opt
```

4. 修改访问权限
```bash
sudo chmod 777 -R jdk-8u151
```

5. 修改系统配置文件
```bash
sudo vim /etc/profile
```

6. 在文件最后添加以下环境变量
```
JAVA_HOME=/opt/jdk-8u151
export JRE_HOME=$JAVA_HOME/jre
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
```

7. 使修改后的文件生效
```bash
sudo source /etc/profile
```

安装完毕，接下来进行测试。

### 测试
分别输入以下命令，如果均无报错，那JDK8就正常安装了。
```bash
java -version
java
javac
```

---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/11/20/install-jdk/](https://newkami.cn/2017/11/20/install-jdk/)**
