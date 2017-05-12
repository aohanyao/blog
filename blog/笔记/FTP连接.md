CentOS6.7 vsftpd配置

 
查看是否已经安装

	rpm -qa | grep vsftpd
 
删除： 
	rpm -e xxx可以删除已安装的vsftpd

安装：

	yum -y install vsftpd -y
 
启动：

	service vsftpd start
 
开机自启动：

	chkconfig vsftpd on

开关： 
开启、重启、关闭

	service vsftpd start
	service vsftpd restart
	service vsftpd stop
 
配置

	vi /etc/vsftpd/vsftpd.conf

	anonymous_enable=NO
	ascii_upload_enable=YES
	ascii_download_enable=YES
	ftpd_banner=Welcome to LINTUT ftp service.
	use_localtime=YES
 
配置防火墙

	vi /etc/sysconfig/iptables
 	INPUT -p tcp -m state --state NEW -m tcp --dport 21 -j ACCEPT


	-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT

保存和关闭文件，重启防火墙

	service iptables restart


自己使用的话不用创建用户了，可以直接使用root用户进行登录即可