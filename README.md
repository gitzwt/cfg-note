# Linux及常用工具配置 #

- - -
**身为码农，表示十分痛恨服务器上的各种乱七八糟配置，平时很少用到Linux命令，对Linux一直保持在`学了就忘，忘了再学`的死循环中，故做此笔记，可能以后翻看的机会也不多，毕竟总有用到的时候**

**PS:本文仅针对CentOS，使用其他发行版Linux请绕行，有补充的可以fork我**

**另外本文不包含安装部分，不会安装的请自行查阅**
- - -

### 一、准备 ###

操作系统
* [CentOS](https://www.centos.org/)
（本人使用的是7，推荐安装`Minimal`版，不使用系统自带工具，全部自己安装）

虚拟机软件（仅针对在Windows/Mac操作系统下学习CentOS，否则略过此项）
* [VirtualBox](https://www.virtualbox.org/)
（推荐使用开源软件，并且本文仅针对此虚拟机）

- - -

### 二、系统篇 ###

<table>
	<tr>
		<th>说明</th>
		<th>命令</th>
	</tr>
	<tr>
		<td>查看系统内核</td>
		<td>uname -r</td>
	</tr>
	<tr>
		<td>查看内核全部信息</td>
		<td>uname -a</td>
	</tr>
	<tr>
		<td>开启防火墙（仅针对CentOS 7）</td>
		<td>systemctl start firewalld.service</td>
	</tr>
	<tr>
		<td>关闭防火墙（仅针对CentOS 7，用虚拟机练习推荐关闭）</td>
		<td>systemctl stop firewalld.service</td>
	</tr>
</table>

- - -

### 三、网络篇 ###

* 查看IP（`Minimal`版没有`ifconfig`命令）
```shell
ip addr
```
* 修改配置文件（文件名不一定叫这个）
```shell
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```
* 将ONBOOT改为yes，意思是在系统启动时是否激活网卡
* 将NM_CONTROLLED改为yes，如果没有添加这一行，意思是实时生效，无需重启网卡
* 重启网络服务
```shell
service network restart
```

- - -

### 四、通讯篇 ###

**默认情况下宿主机是不能访问virtualbox内部的，所以要做如下操作**

* 查看宿主机网络连接，安装virtualbox时会默认创建一个名为VirtualBox Host-Only Network的网络连接

![VirtualBox Host-Only Network](resource/4.1.1.png)
* 查看ip段，通常是192.168.56.\*，不必修改，记住即可

![查看IP](resource/4.1.2.png)
* 修改虚拟机网络设置，添加网卡2，连接方式选择`仅主机(Host-Only)适配器`，保存

![网卡2](resource/4.2.1.png)
* 在虚拟机内使用`ip addr`重新查看，记住新网卡的ip段，必须和VirtualBox Host-Only Network的IP段一致，如果一致，在宿主机访问虚拟机，查看是否能ping通
* 检查vsftpd软件是否安装，默认没有安装，无法远程连接该系统
```shell
rpm -qa|grep vsftpd
```
* 安装vsftpd
```shell
yum install vsftpd -y
```
* 修改/etc/vsftpd/下的ftpusers和user_list文件，删除拒绝远程登录的账号
* 启动vsftpd服务
```shell
service vsftpd start
```

- - -

### 五、基本工具 ###

* 安装vim（文本编辑器，`Minimal`版默认只安装了vi，没有vim）
```shell
yum -y install vim
```
* 安装gcc（C语言源码编译）
```shell
yum -y install gcc-c++
```
* 安装zlib（解压缩工具）
```shell
yum -y install zlib
```
* 安装wget（下载工具）
```shell
yum -y install wget
```
* 安装pcre（正则表达式）
```shell
yum -y install pcre
```
* 安装openssl（用于https）
```shell
yum -y install openssl
```
* 安装make（安装工具）
```shell
yum -y install make
```

- - -

### 六、环境变量 ###

**linux的环境变量分多个，级别不同**

系统级环境变量
1. /etc/profile
2. /etc/environment

用户级环境变量
1. ~/.profile
2. ~/.bashrc

修改后立即生效
```shell
source 环境变量
```

- - -

### 七、软件篇 ###

**推荐用`wget [url]`命令下载，也可用ftp上传，无需安装的推荐放到`/usr/lib/`路径下**

#### [Jdk](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html) ####
**无需安装，直接解压缩后配置环境变量既可用**

修改环境变量，在末尾添加以下几行（配置完毕后不要忘记使用`source`令环境变量生效）
```shell
export JAVA_HOME=/usr/lib/jvm/jdk7 (jdk解压路径)
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
```

- - -

#### [Scala](http://www.scala-lang.org/download/) ####
**无需安装，直接解压缩后配置环境变量既可用，但需要先安装Jdk**

修改环境变量，在末尾添加以下几行（配置完毕后不要忘记使用`source`令环境变量生效）
```shell
export SCALA_HOME=/usr/lib/scala/scala-2.11 (scala类库解压路径)
export PATH=$PATH:${SCALA_HOME}/bin
```

- - -

#### [Tomcat](http://tomcat.apache.org/) ####
**无需安装，直接解压缩后配置环境变量既可用，但需要先安装Jdk**

以Tomcat8.0.35为例修改环境变量，在末尾添加一行（配置完毕后不要忘记使用`source`令环境变量生效）
```shell
export TOMCAT_HOME=/usr/local/tomcat-8.0.35 (Tomcat解压路径)
```
配置虚拟内存，在`#!/bin/sh`下面添加
```shell
JAVA_OPTS='-Xms256m (初始化堆内存)
-Xmx512m (最大堆内存)
-XX:PermSize=256m (初始化栈内存)
-XX:MaxPermSize=512m (最大栈内存)'
```

- - -

#### [Nginx](http://nginx.org/en/download.html) ####
##### 安装 #####
**`Minimal`版没有依赖项源码，需要先下载[pcre](http://www.pcre.org/)/[openssl](https://www.openssl.org/)/[zlib](http://www.zlib.net/)的源码再安装（不是安装后的，install文件夹里都有），安装包推荐放到`/usr/src/`路径下**

解压安装包后，执行`configure`文件，如果不能执行，先用`chmod`赋权，并追加参数
```shell
./configure \
--prefix=/usr/local/nginx-1.11.0 (安装路径) \
--with-http_ssl_module (支持https) \
--with-http_stub_status_module (支持状态监控) \
--with-pcre=/usr/src/pcre (pcre源码路径)
--with-openssl=/usr/src/openssl (openssl源码路径)
--with-zlib=/usr/src/zlib (zlib源码路径)
```
成功后依次执行
```shell
make
make install
```
启动nginx服务器
```shell
/usr/local/nginx-1.11.0/sbin/nginx
```
停止nginx服务器
```shell
/usr/local/nginx-1.11.0/sbin/nginx -s stop
```
重新加载配置
```shell
/usr/local/nginx-1.11.0/sbin/nginx -s reload
```

##### 基础配置 #####
主配置文件：`conf/nginx.conf`
```shell
worker_processes  1; #nginx进程数，建议设置为CPU总核心数

events {
    worker_connections  1024; #单个进程最大连接数，nginx最大连接数=进程数*单进程最大连接数
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65; #超时时间，单位为秒

    server { #代理服务器数量，可以配置多个
        listen       80; #监听端口
        server_name  localhost; #服务器域名

        charset utf-8; #字符集

        location / {
            proxy_pass   http://proxy.com; #反向代理名称，用于匹配集群
            proxy_redirect  default;
        }

        error_page   500 502 503 504  /50x.html; #错误码对应转向
        location = /50x.html {
            root   html;
        }
    }

    upstream proxy.com { #这里匹配反向代理名称
        server 192.168.56.1:9000 weight=1; #真实项目地址以及权重
        server 192.168.56.101:9000 weight=1; #权重数字越大被分配到的几率就越高
    }
}
```

- - -

#### [MySQL](http://dev.mysql.com/downloads/) ####
**从个人角度来说，本人不推荐使用MySQL数据库，可以的话尽量使用MariaDB，个中缘由自行Google，如果一定要使用MySQL，请看如下配置**

##### 安装 #####
在CentOS 7中，系统默认安装了MariaDB，需要先进行卸载，首先使用下面命令查看已安装的MariaDB相关软件
```shell
rpm -qa|grep mariadb
```
使用以下命令卸载
```shell
rpm -e --nodeps mariadb-libs-5.5.41-2.el7_0.x86_64
```
`Minimal`版本也没有libaio，需要安装
```shell
yum -y install libaio
```
以及net-tools
```shell
yum -y install net-tools
```
Linux下的MySQL分为源码安装和rpm安装，因为源码安装需要具备所有依赖项的源码，所以强烈不推荐使用源码安装，在官网下载rpm整合包就好，这里以`mysql-5.7.12`为例，下载后解压，不需要全部安装，依次安装如下安装包即可，顺序不可颠倒
```shell
rpm -ivh mysql-community-common-5.7.12-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.12-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.12-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.12-1.el7.x86_64.rpm
```
安装完毕，先不要启动MySQL

##### 配置 #####
修改配置文件
```shell
vim /etc/my.cnf
```
在[mysqld]下面添加一行
```shell
skip-grant-tables
```
保存后启动MySQL
```shell
service mysqld start
```
此时可用空密码直接进入MySQL
```shell
mysql -uroot -p
```
切换到mysql库并修改密码，MySQL5.7版本的密码字段是authentication_string，低版本是password
```mysql
use mysql
update user set authentication_string=password('123456') where user='root';
```
退出后停止数据库，将`/etc/my.cnf`里的修改删除后重新启动数据库，配置完毕

- - -

#### [MariaDB](https://mariadb.org/download/) ####

##### 安装 #####
MariaDB是CentOS推荐的数据库，安装只需要一行命令即可
```shell
yum -y install mariadb mariadb-server
```
设置为开机自启动
```shell
systemctl enable mariadb
```

##### 配置 #####
安装完成后先启动MariaDB
```shell
service mariadb start
```
运行配置向导
```shell
mysql_secure_installation
```
* 第一个提示让输入当前密码，直接回车
* 第二个提示是否设置密码，直接回车
* 输入密码，回车
* 确认密码，回车
* 是否删除匿名用户，直接回车
* 是否禁止远程登录，视实际情况而定
* 是否删除test数据库，直接回车
* 是否重新加载权限，回车，配置完毕

- - -

#### [Redis](http://redis.io/download) ####

解压后先进入redis目录，以3.2.0为例
```shell
cd redis-2.8.17
```
然后make
```shell
make
```
进入src目录
```shell
cd src
```
运行redis-server启动redis服务
```shell
./redis-server
```
但是这样启动后不会返回命令行，所以在命令后加&，启动redis后返回命令行
```shell
./redis-server &
```

**未完待续**