#Tomcat的安装与配置

Tomcat 8 安装


官网：http://tomcat.apache.org/


压缩包解压

	tar -zxvf apache-tomcat-8.0.32.tar.gz

移到解压出来文件夹到 /usr 下

	mv apache-tomcat-8.0.32/ /usr/program/

为了方便，修改解压目录的名字

	mv /usr/program/apache-tomcat-8.0.32/ /usr/program/tomcat8/

设置 Iptables 规则（这一步是必须设置的）

	一种方式：先关闭 iptables，防止出现拦截问题而测试不了：service iptables stop
	一种方式：在 iptables 中添加允许规则（Tomcat 默认端口是 8080）：

添加规则

	sudo iptables -I INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
保存规则

	sudo /etc/rc.d/init.d/iptables save
重启 iptables：sudo service iptables restart
测试安装好后的 Tomcat：
启动 Tomcat：sh /usr/program/tomcat8/bin/startup.sh ; tail -200f /usr/program/tomcat8/logs/catalina.out
访问：http://服务器 IP 地址:8080/
停止 Tomcat：sh /usr/program/tomcat8/bin/shutdown.sh
Tomcat 8 配置
设置 Tomcat 环境变量：

sudo vim /etc/profile
最文件最尾巴添加下面内容：

CATALINA_HOME=/usr/program/tomcat8
export CATALINA_HOME

# 下面参数根据自己机子内存进行配置
# CATALINA_OPTS 只配置给 Tomcat 的 VM
# JAVA_OPTS 配置给所有调用 Java 的程序的 VM
# 有 CATALINA_OPTS 的时候 Tomcat 优先读这个
# 其中：-server 表示启用 jdk 的 server 版，一定要作为第一个参数，在多个 CPU 时性能佳;  
CATALINA_OPTS="-server -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m"
export CATALINA_OPTS

# 设置生成的 PID 位置
CATALINA_PID=$CATALINA_HOME/catalina.pid
export CATALINA_PID
刷新变量配置：source /etc/profile
如果使用 shutdown.sh 无法停止 tomcat，可以修改其配置：vim /usr/program/tomcat8/bin/shutdown.sh
把最尾巴这一行：exec "$PRGDIR"/"$EXECUTABLE" stop "$@"
改为：exec "$PRGDIR"/"$EXECUTABLE" stop 10 -force
Tomcat 8 优化
官网标准答案：https://tomcat.apache.org/tomcat-8.0-doc/config/http.html
官网帮助文档下载（该资料在：/tomcat-8.0-doc/config/http.html）：wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.0.32/bin/apache-tomcat-8.0.32-fulldocs.tar.gz
编辑配置文件：vim /usr/program/tomcat8/conf/server.xml
修改链接参数：
默认值：
  <Connector 
      port="8080" 
      protocol="HTTP/1.1" 
      connectionTimeout="20000" 
      redirectPort="8443" 
  />
修改为：
  <Connector 
     port="8080" 
     protocol="org.apache.coyote.http11.Http11Nio2Protocol" 
     connectionTimeout="20000" 
     redirectPort="8443" 
     enableLookups="false" 
     acceptCount="100" 
     maxThreads="200" 
     minSpareThreads="10" 
     compression="on" 
     compressionMinSize="2048" 
     compressableMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript" 
     URIEncoding="utf-8"
  />
重点参数解释：
protocol，Tomcat 8 设置 nio2 更好：org.apache.coyote.http11.Http11Nio2Protocol
protocol，Tomcat 6 设置 nio 更好：org.apache.coyote.http11.Http11NioProtocol
enableLookups，禁用DNS查询
acceptCount，指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理，默认设置 100
maxThreads，最大并发数，默认设置 200
minSpareThreads，Tomcat初始化时创建的线程数，默认设置 10

FTP工具
https://www.filezilla.cn/download/client

可以