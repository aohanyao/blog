#JDK安装
首先，更新包：

	yum update
检查服务器上是否已安装旧版本的Java：

	java -version



##下载安装JDK

前往Oracle Java下载页面，根据你的系统架构找到合适的版本。比如我的系统是Centos 6 x86，找到jdk-8u102-linux-i586.rpm，复制其下载地址，在服务器中下载：
	
	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/o

在你下载的目录中执行rpm包安装命令：

	rpm -ivh jdk-8u102-linux-i586.rpm

##环境变量设置

在/etc/profile.d/路径下新建一个文件，名为java.sh：

	vim /etc/profile.d/java.sh
写入以下语句：

	#!/bin/bash
	JAVA_HOME=/usr/java/jdk1.8.0_102/
	PATH=$JAVA_HOME/bin:$PATH
	export PATH JAVA_HOME
	export CLASSPATH=.
保存并关闭文件，执行以下命令使之可运行：

	chmod +x /etc/profile.d/java.sh


最后，执行以下命令来永久设置环境变量：

	source /etc/profile.d/java.sh