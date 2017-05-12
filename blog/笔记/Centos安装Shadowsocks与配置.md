### 安装及配置Shadowsocks

### 安装Shadowsocks

使用root用户登录，进入到Linux控制台，首先对可用软件源进行升级

	yum update

这里我们使用Python版的Shadowsocks，方便日后的管理。先安装Python环境以及pip

	yum install python-setuptools && easy_install pip

成功后，安装Shadowsocks

	pip install shadowsocks

至此，Shadowsocks安装完毕。

### 配置Shadowsocks

在当前root用户的个人文件夹下新建一个Shadowsocks的配置文件

	vi /root/shadowsocks.json

这里以创建一个账号为例

	{
	    "server":"你的IP地址",
	    "port_password":{
	            "8381":"自定义一个该端口的密码",
	            "8382":"自定义一个该端口的密码"
	            },
	    "timeout":300,
	    "method":"rc4-md5",
	    "fast_open":false,
	    "workers":1
	}
	参数名	说明
	server	服务器IP
	port_password	要建立的端口号和密码（可以定义多个端口和密码供多设备使用）
	timeout	超时时间（秒）
	method	加密方法（推荐使用rc4-md5）
	fast_open	如果VPS的Linux内核在3.7以上，可以设置为true开启，以降低延迟
	workers	works数量，默认为1
编辑好后保存并退出。
 
自启动

	echo "ssserver -c /root/shadowsocks.json -d start" >> /etc/rc.d/rc.local