
#### 卸载掉原有mysql
1. 查看是否安装mysql
	
	
		 rpm -qa | grep mysql　　// 是否已经安装了mysql数据库

2. 卸载mysql


		yum remove  mysql mysql-server mysql-libs mysql-server;

    	find / -name mysql// 将找到的相关东西delete掉；

    	rpm -qa|grep mysql//(查询出来的东东yum remove掉)


#### 安装

1. 获取可以安装的版本

		yum list | grep mysql

2. 自动安装

	 	yum install -y mysql-server mysql mysql-deve


 

3. 查看刚安装好的mysql-server的版本

		 rpm -qi mysql-server
 


#### 配置

1. 启动服务:

		service mysqld start


2. 开机自启:
	
 		chkconfig mysqld on
 		chkconfig --list | grep mysql

3. 设置密码:

		mysqladmin -u root password 'root'　　// 通过该命令给root账号设置密码为 root



4. 配置防火墙

		vi /etc/sysconfig/iptables

		-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT

5. 重启防火墙

		service iptables restart

6. 配置远程访问
	
		GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
刷新授权表
	
		FLUSH PRIVILEGES;

#### 其他

1. 文件位置
	
		1./etc/my.cnf 这是mysql的主配置文件

		2./var/lib/mysql   mysql数据库的数据库文件存放位置

2. 相关命令

		service mysqld start 	启动服务
		service mysqld stop 	停止服务
		service mysqld restart 	重启服务
	

