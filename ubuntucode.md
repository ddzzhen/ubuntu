### 1.一般操作命令
#_卸载命令_
```sh
apt-get remove *packagename* --purge 
apt-get autoremove --purge
apt-get clean
```
>该命令可以卸载程序(包括配置文件)、卸载依赖、删除/var/cache/apt/archives下所有安装包。

#升级软件命令
```sh
apt-get update && apt-get upgrade
```
#apt常用命令
```sh
tar -xvf       #解压tar.xz文件
tar -xzf       #解压tar.gz文件
sudo passwd    #更改root密码
/etc/ssh/sshd_config  port=22		    #修改ssh端口
apt-cache show *packagename* 			#获取包的相关信息，如说明、大小、版本等
apt-cache depends *packagename* 		#了解使用依赖
apt-cache rdepends *packagename*		#查看该包被哪些包依赖
apt-get install packagename 			#安装包
apt-get install package=version 		#指定安装版本
apt-get install packagename --reinstall 			#重新安装包
apt-get remove packagename --purge 				#卸载程序，包括删除配置文件等
apt-get update 							#更新/etc/apt/sources.list里的源
apt-get upgrade -u 					#升级程序(不包括依赖关系改变的) -u完整显示列表
apt-get dist-upgrade 				#升级程序(依赖关系改变并重新组织依赖，慎用)
apt-get clean #删除安装包(节约硬盘空间,下次安装需要重新下载包，软件包位置：/var/cache/apt/archives/)
apt-get autoclean 		#删除已卸载的安装包
apt-get autoremove 		#卸载依赖的程序
apt-get 安装位置
	  *下载的软件存放位置 /var/cache/apt/archives
	  *安装后软件默认位置 /usr/share
	  *可执行文件位置 /usr/bin
	  *lib文件位置 /usr/lib
```
#Linux 常用目录
/boot 引导程序，内核等存放的目录
/sbin 超级用户可以使用的命令的目录
/bin 普通用户使用的命令
/lib 共享库目录
/dev 设备目录
/root 用户root的home目录
/etc 全局配置文件目录
/usr 用户安装目录
/usr/include C程序语言编译使用的头文件
/proc 系统内部一些信息
/var 经常变化目录 经常放日志文件，缓存文件
/tmp 临时目录 系统断电 或许目录被会清空
/lost+found 当系统崩溃的时候，在系统修复过程中需要恢复的文件，可能就会在这里被找到了，这个目录一般为空
man hier 查看目录帮助
man *appname* 打开说明文档,例如：man apt-get查看apt-get详细说明，比help要详细

## 2.防火墙配置
#### （1）ufw简单设置
sudo apt-get install ufw		#安装
sudo ufw status					#查看状态
sudo ufw default deny		#默认拒绝策略，即拒绝外部对本机的访问，本机对外部访问正常
sudo ufw enable				#启用防火墙
sudu ufw disable				#关闭防火墙
sudo ufw allow 22			#启用22端口，包括tcp和udp
sudo ufw delete allow 22				#删除已经添加的22端口规则
sudo ufw logging on|off	#转换日志状态
sudo ufw allow smtp			#允许smtp

sudo vi /etc/default/ufw				#防火墙配置文件
**修改DEFAULT_FORWARD_POLICY="ACCEPT"即可实现转发，从而让ocserv正常使用。**
#### （2）iptables复杂设置
＃删除原来 iptables 里面已经有的规则
iptables -F
iptables -X

＃抛弃所有不符合三种链规则的数据包
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

＃设置：本地进程 lo 的 INPUT 和 OUTPUT 链接 ； eth0的 INPUT链
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i eth0 -m state --state NEW,INVALID -j LOG
iptables -A OUTPUT -o lo -j ACCEPT

＃对其他主要允许的端口的 OUTPUT设置：
＃ DNS
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 53 -j ACCEPT
iptables -A OUTPUT -o eth1 -p UDP --sport 1024:65535 --dport 53 -j ACCEPT

＃HTTP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 80 -j ACCEPT

HTTPS
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 443 -j ACCEPT

Email 接受 和发送
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 110 -j ACCEPT
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 25 -j ACCEPT

＃ FTP 数据和控制
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 20 -j ACCEPT
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 21 -j ACCEPT

＃DHCP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 68 -j ACCEPT
iptables -A OUTPUT -o eth1 -p UDP --sport 1024:65535 --dport 68 -j ACCEPT

＃POP3S Email安全接收
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 995 -j ACCEPT

＃时间同步服务器 NTP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 123 -j ACCEPT

＃拒绝 eth0 其他剩下的
iptables -A OUTPUT -o eth0 --match state --state NEW,INVALID -j LOG

有关于iptables存储和调用的命令：
代码:
iptables-save > /etc/iptables.up.rule ＃ 存储在想用的地方
iptables-restore < /etc/iptables.up.rules ＃调用代码

因为iptables 在每次机器重新启动以后，需要再次输入或者调用，为了方便操作，使用
代码:
sudo gedit /etc/network/interfaces

在代码:
auto ath0
iface ath0 inet dhcp
后面加上代码:
pre-up iptables-restore < /etc/iptables.up.rules	 #启动自动调用已存储的iptables

代码:
post-down iptables-save > /etc/iptables.up.rule		 #关机时，把当前iptables 储存  

##  3.vpn账号和密码

username:
```
		January
		February
		March
		April
		May
		June
		July
		August
		September
		October
		November
		December
```
password:knowwhat.cf
