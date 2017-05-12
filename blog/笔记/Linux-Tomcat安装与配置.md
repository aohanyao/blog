#Tomcat的安装与配置

##安装

1、基础环境安装配置，如操作系统（我使用Centos6.3）、网络及主机基本配置等

2、yum安装tomcat：
 	yum -y install tomcat6 tomcat6-webapps tomcat6-admin-webapps tomcat6-docs-webapp tomcat6-javadoc

yum安装tomcat会自动安装相关的软件，如jre环境等，因此不需要单独安装jre。

3、yum安装后的tomcat目录说明：

	配置文件目录：/etc/tomcat6
	安装程序主目录：/var/lib/tomcat6/

在Centos使用yum安装后，Tomcat相关的目录都已采用符号链接到/usr/share/tomcat6目录，包含webapps等，这很方便我们配置管理

 	ll /usr/share/tomcat6
	总用量 4
	drwxr-xr-x. 2 root root   4096 10月 19 00:44 bin
	lrwxrwxrwx. 1 root tomcat   12 10月 19 00:44 conf ->/etc/tomcat6
	lrwxrwxrwx. 1 root root     23 10月 19 00:44 lib ->/usr/share/java/tomcat6
	lrwxrwxrwx. 1 root root     16 10月 19 00:44 logs ->/var/log/tomcat6
	lrwxrwxrwx. 1 root root     23 10月 19 00:44 temp ->/var/cache/tomcat6/temp
	lrwxrwxrwx. 1 root root     24 10月 19 00:44 webapps ->/var/lib/tomcat6/webapps
	lrwxrwxrwx. 1 root root     23 10月 19 00:44 work ->/var/cache/tomcat6/work


 
4、修改端口8080为80：

	需80端口未被占用，可以使用netstat -nat查看80端口是否在使用。
	a)修改vi /etc/tomcat6/server.xml文件的如下字段中的8080为80
	<Connector port="8080" protocol="HTTP/1.1"
	               connectionTimeout="20000"
	               redirectPort="8443" />
	b)由于在Centos6中，系统不允许tomcat用户使用1024以下的端口，因此还需修改vi /etc/tomcat6/tomcat6.conf
	找到CONNECTOR_PORT="8080"并注释掉，新增如下两行：
	TOMCAT_USER="root"
	CONNECTOR_PORT="80"
注：这样做的安全性有待验证
 
c）使用命令service tomcat6 restart 重启tomcat服务。以后访问页面就可以只需要输入IP或者主机名即可，而不再需要加端口号。


FTP工具
https://www.filezilla.cn/download/client

可以