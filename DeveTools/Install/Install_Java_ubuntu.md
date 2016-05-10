#Install_Java_ubuntu

许多人比较厌恶Java，但是很有可能因为某些原因你需要安装Java，尽管你很讨厌它。在这篇文章中，我们将展示如何在Ubuntu 14.04安装Java（也可能在Linux Mint 17同样适用）。

#### JRE vs OpenJDK vs Oracle JDK

在我们继续了解如何安装Java之前，让我们快速地了解JRE、OpenJDK和Oracle JDK之间的不同之处。

- JRE（Java Runtime Environment），它是你运行一个基于Java语言应用程序的所正常需要的环境。如果你不是一个程序员的话，这些足够你的需要。
- JDK代表Java开发工具包，如果你想做一些有关Java的开发（阅读程序），这正是你所需要的。
- OpenJDK是Java开发工具包的开源实现，Oracle JDK是Java开发工具包的官方Oracle版本。尽管OpenJDK已经足够满足大多数的案例，但是许多程序比如[Android Studio](https://linux.cn/2)建议使用Oracle JDK，以避免UI/性能问题。

#### 检查Java是否已经安装在Ubuntu上

打开终端，使用下面的命令：

1. java -version

如果你看到像下面的输出，这就意味着你并没有安装过Java:

1. The program ‘java’ can be found in the following packages:
2. *default-jre
3. * gcj-4.6-jre-headless
4. * openjdk-6-jre-headless
5. * gcj-4.5-jre-headless
6. * openjdk-7-jre-headless
7. Try:sudoapt-get install


#### 在Ubuntu和Linux Mint上安装Java

看了各种类型‘Java’的不同之后，让我们看如何安装他们。



##### 在Ubuntu和Linux Mint上安装JRE

打开终端，使用下面的命令安装JRE：

1. sudoapt-get install default-jre


##### 在Ubuntu和Linux Mint上安装OpenJDK

在终端，使用下面的命令安装OpenJDK Java开发工具包：

1. sudoapt-get install default-jdk

特殊地，如果你想要安装Java 7或者Java 6等等，你可以使用openjdk-7-jdk/openjdk-6jdk，但是记住在此之前安装openjdk-7-jre/openjdk-6-jre。



##### 在Ubuntu和Linux Mint上安装Oracle JDK

使用下面的命令安装，只需一些时间，它就会下载许多的文件，所及你要确保你的网络环境良好：
```
1. sudo add-apt-repository ppa:webupd8team/java
2. sudoapt-get update
3. sudoapt-get install oracle-java8-installer
4. sudoapt-get install oracle-java8-set-default
```
如果你想安装Java 7(i.e Java 1.7)，在上面的命令中用java7代替java8。

我希望这篇文章能够帮助你学会在Ubuntu,Linux Mint和其它基于Ubuntu的Linux发行版本上安装Java，我们欢迎所有问题或者建议。

##More

[HOW TO INSTALL JAVA ON UBUNTU 14.04](http://itsfoss.com/install-java-ubuntu-1404/)

***

### ubuntu 安装jdk 的两种方式:

1:通过ppa(源) 方式安装.

2:通过官网下载安装包安装.

这里推荐第1种,因为可以通过 apt-get upgrade 方式方便获得jdk的升级

### 使用ppa/源方式安装

#### 1.添加ppa

sudo add-apt-repository ppa:webupd8team/java

sudo apt-get update

#### 2.安装oracle-java-installer

　jdk7

sudo apt-get install oracle-java7-installer

　jdk8

sudo apt-get install oracle-java8-installer

安装器会提示你同意 oracle 的服务条款,选择 ok

然后选择yes 即可

如果你懒,不想自己手动点击.也可以加入下面的这条命令,默认同意条款:

JDK7 默认选择条款

echo oracle-java7-installer shared/accepted-oracle-license-v1-1selecttrue | sudo /usr/bin/debconf-set-selections

JDK8 默认选择条款

echo oracle-java8-installer shared/accepted-oracle-license-v1-1selecttrue | sudo /usr/bin/debconf-set-selections

 接下会是等待(依个人网速定)

如果你因为防火墙或者其他原因,导致installer 下载速度很慢,可以中断操作.然后下载好相应jdk的tar.gz 包,放在:

   /var/cache/oracle-jdk7-installer             (jdk7) 

   /var/cache/oracle-jdk8-installer              (jdk8) 

下面,然后安装一次installer. installer 则会默认使用 你下载的tar.gz包

#### 3.设置系统默认jdk

JDk7

sudo update-java-alternatives -s java-7-oracle

JDK8

sudo update-java-alternatives -s java-8-oracle

如果即安装了jdk7,又安装了jdk8,要实现两者的切换,可以:

　　jdk8 切换到jdk7

sudo update-java-alternatives -s java-7-oracle

　　jdk7 切换到jdk8

sudo update-java-alternatives -s java-8-oracle

4.测试jdk 是是否安装成功:

java -version

javac -version

### 直接下载jdk压缩包方式安装(这里只介绍jdk7的,jdk8 的原理完全一致)

　分为下面5个步骤

   1.官网下载JDK

   2.解压缩,放到指定目录

   3.配置环境变量

   4.设置系统默认JDK

　5. 测试jdk

#### 1.官网下载JDK　　　

     地址: http://www.oracle.com/technetwork/articles/javase/index-jsp-138363.html

　　选择相应的 .gz包下载 

#### 2. 解压缩,放到指定目录(以jdk-7u60-linux-x64.gz为例)

　　创建目录:

sudomkdir /usr/lib/jvm

　加压缩到该目录:

sudotar -zxvf jdk-7u60-linux-x64.gz -C /usr/lib/jvm

#### 3.修改环境变量:　　

sudo vim ~/.bashrc

　文件的末尾追加下面内容:

#set oracle jdk environment  
export JAVA_HOME=/usr/lib/jvm/jdk1.7.0_60## 这里要注意目录要换成自己解压的jdk 目录
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH  

　使环境变量马上生效

 source ~/.bashrc

#### 4.设置系统默认jdk 版本

sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.7.0_60/bin/java 300sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.7.0_60/bin/javac 300sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/jdk1.7.0_60/bin/jar 300sudo update-alternatives --install /usr/bin/javah javah /usr/lib/jvm/jdk1.7.0_60/bin/javah 300sudo update-alternatives --install /usr/bin/javap javap /usr/lib/jvm/jdk1.7.0_60/bin/javap 300

　然后执行:

sudo update-alternatives --config java

    若是初次安装jdk,会有下面的提示     

   There is only one alternative in link group java (providing /usr/bin/java):   
    /usr/lib/jvm/jdk1.7.0_60/bin/java

　否者,选择合适的jdk

#### 5.测试jdk

java -version  
  
java version "1.7.0_60"

_  Java(TM) SE Runtime Environment (build 1.7.0_60-b19)   Java HotSpot(TM) 64-Bit Server VM (build 24.60-b09, mixed mode)_

  jdk 安装成功

#### ubuntu  两种下安装jdk7 jdk8 的方式介绍完毕

 参考文章: 

 1. http://www.webupd8.org/2012/01/install-oracle-java-jdk-7-in-ubuntu-via.html

 2. http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html

