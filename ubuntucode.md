## 1.一般操作命令
**#卸载命令**
```sh
apt-get remove *packagename* --purge 
apt-get autoremove --purge
apt-get clean
```
>该命令可以卸载程序(包括配置文件)、卸载依赖、删除/var/cache/apt/archives下所有安装包。

**#升级软件命令**
```sh
apt-get update && apt-get upgrade
```
**#apt常用命令**
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
**#Linux 常用目录**
>/boot #引导程序，内核等存放的目录
/sbin #超级用户可以使用的命令的目录
/bin #普通用户使用的命令
/lib #共享库目录
/dev #设备目录
/root #用户root的home目录
/etc #全局配置文件目录
/usr #用户安装目录
/usr/include #C程序语言编译使用的头文件
/proc #系统内部一些信息
/var #经常变化目录 经常放日志文件，缓存文件
/tmp #临时目录 系统断电 或许目录被会清空
/lost+found #当系统崩溃的时候，在系统修复过程中需要恢复的文件，可能就会在这里被找到了，这个目录一般为空
```sh
man hier 查看目录帮助
man *appname* 打开说明文档,例如：man apt-get查看apt-get详细说明，比help要详细
```
**#程序挂起**
```sh
ctrl-z	    #挂起程序
jobs	    #查看挂起的程序
bg %1	    #将挂起的1号程序，在后台继续执行
fg %1	    #前台处理1号程序
kill	    #管理后台的任务
command &   #后台运行command,但仍会输出在页面
command >out.file 2>&1 &        #后台运行,且将标准输出和错误输出放在out.file中
nohup command &		            #后台继续运行，退出账号也继续运行
其中:
        * 匹配文件名中任何字符串，包括null
        ？ 匹配文件名任何单个字符
        [...] 匹配[]中包含的任何字符
        [!...] 匹配[]中非！之后的字符
```
**#session命令**
#新建session:
```sh
    screen
    command
    ctrl-a && ctrl-d    #回到原来的session,下次登录再切回去
    screen -r
```
#新建多个session管理
```sh
screen -S name          #创建名为name的session
screen -r name          #切至指定 session
screen -r pid_number    #切至指定 session_pid
screen -ls              #列出所有session
ctrl-c && ctrl-d        #停掉某个session
```
#tmux管理
```sh
tmux                                    #新建session
command                                 #执行任意command
tmux detach  or ctrl-b && ctrl-d        #切回session
tmux attach                             #再次登录时返回运行服务的session
tmux new -s name                        #新建指定名字的session
tmux attach -t name                     #切换到
tmux list-sessions                      #列出所有
tmux detach                             #退出当前session，返回到前一个
tmux kill-session -t name               #杀死指定session
```
**#rc.local脚本**
#Ubuntu14
```sh
vim /opt/cmd/auto.sh        #创建脚本文件
```
>#!/bin/bash
/opt/redis/redis-server		    #示例为启动redis-server
```sh
chmod +x /opt/cmd/auto.sh   #设置权限
```
```sh
vim /etc/init.d/rc.local    #编辑rc.local文件
```
>在do_start()函数第一行增加自启动目录，格式为：路径 &> /dev/null &
**示例:**
do_start(){
    /opt/cmd/auto.sh &> /dev/null &
    if [ -x /etc/rc.local ]  then
    ......
        }
        
#Ubuntu16
需要新建rc.local
```sh
vi /etc/rc.local
chown root:root /etc/rc.local				
chmod 755 /etc/rc.local
systemctl enable rc-local.service
```
rc.local样板:
```sh
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

exit 0
```
**#keygen登录**
root文件夹下:
```sh
ssh-keygen
cd .ssh
vim authorized_keys
```
将本地的pub文件密钥粘贴进去,保存
```sh
vim /etc/ssh/sshd_config
```
>#RSAAuthentication yes
#PubkeyAuthentication yes
#AuthorizedKeysFile     .ssh/authorized_keys
**备注:去掉上面三行的注释**
```sh
service ssh restart         #可以用ssh-key登录
```
**#远程文件传输**
相关链接:http://www.cnblogs.com/voidy/p/4215891.html
本地向其它服务器传输文件夹:
```sh
scp -r  -P XXXX /本地文件夹/  username@ip:/远程文件夹/
scp -r  -P 27875 /home/ root@1.2.3.4:/home/sample/          #使用root用户和27875端口向ip服务器传输文件夹,如果没有[-P 27875]则默认用端口22传输,输入后会提示输入密码
```
**#更换系统内核**
查询当前系统的内核:
```sh
dpkg -l|grep linux-image
```
卸载不需要的内核:
```sh
apt-get remove linux-image-3.16.0-92-generic
```
下载安装脚本:(脚本里的内核号根据需要修改)
```sh
wget http://box.maxfox.me/auto-switch-kernel.sh
```
备注:能够使用speedspeeder的32位内核为:linux-image-extra-3.13.0-29-generic
```sh
chmod +x auto-switch-kernel.sh && ./auto-switch-kernel.sh
update-grub         #更新内核
reboot              #重启
```
**#锐速一键安装**
Ubuntu-3.13.0-29版本的锐速一键安装:
```sh
wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder-v.sh && bash serverspeeder-v.sh Ubuntu 14.04 3.13.0-29-generic x32 3.10.66.29 serverspeeder_3033
```
常用指令:
```sh
/serverspeeder/bin/serverSpeeder.sh status
/serverspeeder/bin/serverSpeeder.sh stop
/serverspeeder/bin/serverSpeeder.sh restart
```
备注:该版本的配置不影响ocserv的udp速度.
**#shadowsocks一键安装**
```sh
apt-get install python-pip
pip install git+https://github.com/shadowsocks/shadowsocks.git@master
sudo ssserver -p 443 -k dudazhi -m aes-256-cfb --user nobody -d start      #临时开启
```
自动配置:
```sh
vim /etc/shadowsocks.json
```
>{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}

```sh
ssserver -c /etc/shadowsocks.json                   #测试是否正确
ssserver -c /etc/shadowsocks.json -d start          #后台启动
ssserver -c /etc/shadowsocks.json -d stop           #后台关闭
```
开机自动启动配置:

```sh

ssserver -c /etc/shadowsocks.json -d start      #后台启动命令加入自动脚本
/opt/cmd/auto.sh                                #自动脚本,详见[rc.local脚本]
```
## 2.防火墙配置
**#ufw简单设置**
```sh
sudo apt-get install ufw	#安装
sudo ufw status				#查看状态
sudo ufw default deny		#默认拒绝策略，即拒绝外部对本机的访问，本机对外部访问正常
sudo ufw enable				#启用防火墙
sudu ufw disable			#关闭防火墙
sudo ufw allow 22			#启用22端口，包括tcp和udp
sudo ufw delete allow 22	#删除已经添加的22端口规则
sudo ufw logging on|off	    #转换日志状态
sudo ufw allow smtp			#允许smtp
sudo vi /etc/default/ufw	#防火墙配置文件
>>>>修改DEFAULT_FORWARD_POLICY="ACCEPT"即可实现转发，从而让ocserv正常使用
```
**#iptables复杂设置**
```sh
＃删除原来 iptables 里面已经有的规则
    iptables -F
    iptables -X
＃抛弃所有不符合三种链规则的数据包
    iptables -P INPUT DROP
    iptables -P OUTPUT DROP
    iptables -P FORWARD DROP
＃设置：本地进程 lo 的 INPUT 和 OUTPUT 链接；eth0的INPUT链
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
    ＃HTTPS
        iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 443 -j ACCEPT
    ＃Email接受和发送
        iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 110 -j ACCEPT
        iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 25 -j ACCEPT
    ＃FTP数据和控制
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
    ＃有关于iptables存储和调用的命令
        iptables-save > /etc/iptables.up.rule           ＃存储在想用的地方
        iptables-restore < /etc/iptables.up.rules       ＃调用代码
    ＃因为iptables 在每次机器重新启动以后，需要再次输入或者调用，为了方便操作，使用
        sudo gedit /etc/network/interfaces
    ＃在代码:
        auto ath0
        iface ath0 inet dhcp
    ＃后面加上代码
        pre-up iptables-restore < /etc/iptables.up.rules	 #启动自动调用已存储的iptables
        post-down iptables-save > /etc/iptables.up.rule		 #关机时，把当前iptables 储存
 ```       
## 3.glances安装
安装地址：
>https://github.com/nicolargo/glances
https://pypi.python.org/pypi/Glances

**#安装流程**
```sh
apt-get install gcc python-dev python-pip bottle
pip install psutil
pip install --upgrade glances
```
**#使用方法**
```sh
For the standalone mode, just run:
    $ glances
For the Web server mode, run:
    $ glances -w
and enter the URL [http://<ip>:61208] in your favorite web browser.

For the client/server mode, run:
    $ glances -s            #on the server side.
    $ glances -c <ip>       #on the client one.
You can also detect and display all Glances servers available on your network or defined in the configuration file:
    $ glances --browser
For help:
    $ glances -h
```
## 4.python升级
官网下载地址：[http://www.python.org/getit/] #稳定版2.7.13
```sh
tar xvf Python-x.x.x.tar.xz
cd Python-x.x.x
./configure
      make && make install
```
#默认安装目录：/usr/local/lib/python2.7，查看版本号：
```sh
    /usr/local/bin/python -V
```
#更改默认版本号：
```sh
    sudo rm /usr/bin/python
    sudo ln -s /usr/local/bin/python2.7 /usr/bin/python
```
## 5.runlevel
#查看当前level
```sh
    /bin/runlevel
```
#设置默认值
```sh
vim /etc/init/rc-sysinit.conf
DEFAULT_RUNLEVEL		修改为3
```
#level解释
>rc0.d  rc1.d  rc2.d  rc3.d  rc4.d  rc5.d  rc6.d
0 - 停机 
1 - 单用户模式 
2 - 多用户，但是没有NFS 
3 - 完全多用户模式 
4 - 自定义模式 
5 - 桌面模式
6 - 重新启动
注:请不要将level设置为0/6,否则将出现异常;经过测试,对于Ubuntu系统而言,2/3/4等价.


