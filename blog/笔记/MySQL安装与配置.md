二、卸载掉原有mysql
查看是否安装mysql
	
	 rpm -qa | grep mysql　　// 这个命令就会查看该操作系统上是否已经安装了mysql数据库

卸载mysql

	rpm -e mysql　　// 普通删除模式
	 rpm -e --nodeps mysql　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除


安装

我是通过yum的方式来进行mysql的数据库安装，首先我们可以输入 yum list | grep mysql 命令来查看yum上提供的mysql数据库可下载的版本：

	[root@xiaoluo ~]# yum list | grep mysql

然后我们可以通过输入 yum install -y mysql-server mysql mysql-devel 命令将mysql mysql-server mysql-devel都安装好(注意:安装mysql时我们并不是安装了mysql客户端就相当于安装好了mysql数据库了，我们还需要安装mysql-server服务端才行)

 

	[root@xiaoluo ~]# yum install -y mysql-server mysql mysql-deve
 

此时我们可以通过如下命令，查看刚安装好的mysql-server的版本

 

	[root@xiaoluo ~]# rpm -qi mysql-server
 


四、mysql数据库的初始化及相关配置

启动服务:

	service mysqld start

 



 开机自启:
	
 	chkconfig mysqld on
 	chkconfig --list | grep mysql

设置密码:


	 mysqladmin -u root password 'root'　　// 通过该命令给root账号设置密码为 root




五、mysql数据库的主要配置文件

1./etc/my.cnf 这是mysql的主配置文件

2./var/lib/mysql   mysql数据库的数据库文件存放位置


 
配置防火墙

	vi /etc/sysconfig/iptables

	-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT

保存和关闭文件，重启防火墙

	service iptables restart


创建用户：（以SQLyou工具登录）

 	create user yhw identified by "yhw";

 yhw表示你要建立的用户名，后面的yhw表示密码

"mei5201314"