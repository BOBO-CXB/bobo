# Linux

### 一、系统目录结构

### 1、一切皆文件

### 2、根目录/，所有文件都挂载在该目录下

```shell
ls /
```

- /bin: 该目录主要存放经常使用的命令
- /boot: 启动Linux时的一些核心文件，不要动
- /dev: Device存放的是外部设备
- /etc: 存放系统管理所需的配置文件和子目录（重要）
- /home: 用户的主目录，每个用户都有一个自己的目录，目录名一般是以用户的账号命名的
- /lib: 存放系统最基本的动态连接共享库，类似于DLL文件（不要动）
- /media: 识别的设备，例如U盘、光驱等挂载在该目录下
- /mnt: 让用户临时挂载别的文件系统的，可以将光驱挂载在/mnt
- /opt: 给主机额外安装软件所摆放的目录，如数据库等软件（重要）
- /proc: 是一个虚拟目录，系统内存的映射
- /root: 系统管理员，超级用户的主目录
- /usr: 用户的很多应用程序和文件都放在该目录下，类似于program files目录
- /temp: 用于存放临时文件，用完即丢文件可以放在该目录下，如安装包

### 3、目录相关命令

#### 绝对路劲与相对路径

- 绝对路径：路径的全称，例如C:\Program Files\7-Zip
- 相对路径：如果当前所在路径为C:\Program Files，则上述路径的相对路径为\7-Zip

>cd 目录名     切换目录命令

- 目录名可以分为绝对路径和相对路径
- 绝对路径，以/开头
- 相对路径，对于当前目录如何查找

##### cd ..  返回上一级目录

##### cd ./  



>ls 列出目录

参数:

- -a: 查看全部文件，包括隐藏文件
- -l: 列出所有文件，包含文件的属性以及权限

>pwd 显示用户当前所在目录



>mkdir 创建一个目录

参数:

- -p: 递归创建多级目录

```shell
mkdir -p test01/test02/test03
```

>rmdir 删除目录

参数:

- -p: 递归删除多级目录

```shell
rmdir -p test01/test02/test03
```

>cp 复制文件或目录

cp 原来地方 新地方

```shell
[root@CentOS7 home]# cp Linux.txt bobo
```

>rm 移除文件或目录

参数

- -f: 忽略不存在的文件，不会出现警告，强制删除
- -r: 递归删除
- -i: 互动，删除询问是否删除

```shell
rm -rf /  #删除系统中所有文件
```



>mv 移动文件或目录、重命名

参数：

- -f: 强制移动
- -u: 只替换已经更新过的文件

### 4、文件属性

```shell
[root@CentOS7 home]# cd /
[root@CentOS7 /]# ll
total 16
lrwxrwxrwx.   1 root root    7 Dec 26 22:11 bin -> usr/bin
dr-xr-xr-x.   5 root root 4096 Dec 26 22:16 boot
drwxr-xr-x   20 root root 3220 Dec 26 22:51 dev
drwxr-xr-x.  75 root root 8192 Dec 26 22:51 etc
drwxr-xr-x.   3 root root   35 Dec 27 00:08 home
lrwxrwxrwx.   1 root root    7 Dec 26 22:11 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Dec 26 22:11 lib64 -> usr/lib64
drwxr-xr-x.   2 root root    6 Apr 11  2018 media
drwxr-xr-x.   2 root root    6 Apr 11  2018 mnt
drwxr-xr-x.   2 root root    6 Apr 11  2018 opt
dr-xr-xr-x  110 root root    0 Dec 26 22:51 proc
dr-xr-x---.   2 root root  135 Dec 26 22:47 root
drwxr-xr-x   24 root root  700 Dec 26 22:51 run
lrwxrwxrwx.   1 root root    8 Dec 26 22:11 sbin -> usr/sbin
drwxr-xr-x.   2 root root    6 Apr 11  2018 srv
dr-xr-xr-x   13 root root    0 Dec 26 23:37 sys
drwxrwxrwt.   9 root root  200 Dec 26 23:57 tmp
drwxr-xr-x.  13 root root  155 Dec 26 22:11 usr
drwxr-xr-x.  19 root root  267 Dec 26 22:18 var
[root@CentOS7 /]# 

```

####    第一列   l rwxrwxrwx

- 第一个字符代表该文件的目录、文件或链接文件等属性：

  - 【d】表示为目录
  - 【-】为文件
  - 【l】为链接文档（bin -> usr/bin）相当于快捷方式
  - 【b】为装置文件中可供存储的接口设备
  - 【c】为装置文件中串行端口设备，如键盘鼠标等

- 后9个字符三个为一组，均为【rwx】的组合

  - 【r】代表可读read
  - 【w】代表可写write
  - 【x】代表可执行execute
  - 【-】代表没有相应权限

  第一组代表属主权限（owner）：即文件所有者的权限

  第一组代表属组权限（group）：即文件所有者所在用户组的权限

  第三组代表其他用户（other）：即其他用户权限

  #### 第三列 root root

  第一个代表属主

  第二个代表属组（用户组）

>chgrp 修改文件属组

```shell
chgrp [-R] 属组 文件名  #-R 递归修改文件属组
```



>chown 更改文件属主也可以修改属组

```shell
chown [-R] 属主 文件名
chown [-R] 属主：属组 文件名
```



>chmod  更改文件属性权限（必须掌握）

```shell
chmod [-R] xyz 文件或目录  #r:4 w:2 x:1

chmod 777 文件名
```

### 5、查看文件内容

- cat 由第一行开始显示文件内容（读文章或者配置文件等）
- tac 从最后一行开始显示
- nl 显示时输出行号  （看代码时显示行号）
- more 一页一页显示文件内容（空格翻页、enter代表向下看一行、：f行号）
- less 和more类似，可以往前翻页   ？可以搜索字符，n继续搜素下一个
- head只看头几行    （-n参数控制显示几行）
- tail 只看尾部几行

### 6、账号管理

>useradd 添加用户

useradd -选项 用户名

选项：

- -m：自动创建这个用户的主目录/home/bobo
- -g：用户所属的用户组

```shell
[root@CentOS7 home]# useradd -m bobo #创建用户以及目录
[root@CentOS7 home]# ls
bobo  linux.txt
[root@CentOS7 home]# 
```

>userdel 删除用户

```shell
[root@CentOS7 home]# userdel -r bobo #删除用户以及目录
```

>usermod 修改用户

usermod -选项 用户

>切换用户

切换用户的命令为 su username

切换为root用户 sudo su

退出exit

>passwd 用户名 修改密码

```shell
[root@CentOS7 ~]# passwd bobo
Changing password for user bobo.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@CentOS7 ~]# 
```

>锁定账户

```shell
passwd -l 用户名 #锁定用户后就不能进行登录
passwd -d 用户名 #没有密码也不能登录
```

### 7、用户组管理

>groupadd 组名 创建用户组

参数：

- -g id ：可以指定id，不指定的话就自增

>groupdel 组名 删除用户组

>groupmod 修改用户组

### 8、重要文件

/etc/passwd  用户管理相关的重要文件

```shell
[root@CentOS7 ~]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
bobo:x:1000:1000::/home/bobo:/bin/bash

#用户名：口令（密码，不可见）：用户标识：注释性描述：组标识：用户主目录：登陆shell
```

/etc/group 用户组文件

### 9、磁盘管理

>df (列出文件系统整体的磁盘使用量)
>
>du(检测磁盘空间使用量)

```shell
[root@CentOS7 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 475M     0  475M   0% /dev
tmpfs                    487M     0  487M   0% /dev/shm
tmpfs                    487M  7.6M  479M   2% /run
tmpfs                    487M     0  487M   0% /sys/fs/cgroup
/dev/mapper/centos-root   62G  1.5G   61G   3% /
/dev/sda1                253M  118M  135M  47% /boot
tmpfs                     98M     0   98M   0% /run/user/0
[root@CentOS7 ~]# du
36	.


```

### 10、进程管理

>ps 查看当前系统中正在执行的各种进程信息

参数：

- -a	显示当前终端运行的所有进程信息
- -u     以用户的信息显示进程
- -x      显示后台进程的参数

常用命令：

```shell
#查询所有的进程信息
ps -aux

#| 在linux中这个叫做管道符  A|B的意思就是A的结果作为B的输入
#grep 查找文件中符合条件的字符串
#组合使用

ps -aux|grep mysql #查看mysql进程

ps -ef|grep mysql #可以查看到父进程信息

#查看父进程一般可以通过目录树结构来看
pstree -pu
#-p 显示父id -u 显示用户组

[root@CentOS7 ~]# pstree -pu
systemd(1)─┬─NetworkManager(716)─┬─{NetworkManager}(729)
           │                     └─{NetworkManager}(733)
           ├─VGAuthService(662)
           ├─agetty(670)
           ├─auditd(630)───{auditd}(631)
           ├─crond(665)
           ├─dbus-daemon(657,dbus)
           ├─firewalld(679)───{firewalld}(839)
           ├─lvmetad(511)
           ├─master(1125)─┬─pickup(1139,postfix)
           │              └─qmgr(1140,postfix)
           ├─polkitd(653,polkitd)─┬─{polkitd}(668)
           │                      ├─{polkitd}(671)
           │                      ├─{polkitd}(672)
           │                      ├─{polkitd}(673)
           │                      ├─{polkitd}(674)
           │                      └─{polkitd}(677)
           ├─rsyslogd(1013)─┬─{rsyslogd}(1030)
           │                └─{rsyslogd}(1031)
           ├─sshd(1010)───sshd(1266)───bash(1268)───pstree(1334)
           ├─systemd-journal(484)
           ├─systemd-logind(664)
           ├─systemd-udevd(512)
           ├─tuned(1012)─┬─{tuned}(1256)
           │             ├─{tuned}(1257)
           │             ├─{tuned}(1259)
           │             └─{tuned}(1261)
           └─vmtoolsd(663)─┬─{vmtoolsd}(680)
                           └─{vmtoolsd}(684)

```

>kill -9 进程id 强制结束进程

### 11、环境安装

安装软件一般有三种方式：

- rpm（安装JDK）
- 解压缩（tomcat）
- yum在线安装（docker）

#### JDK安装

- 下载JDK rpm       jdk-8u311-linux-x64.rpm

- 安装java环境

- ```shell
  #检测当前系统是否存在java环境
  java -version
  #如果有的话需要卸载
  #检测jdk版本信息
  rpm -qa|grep jdk
  #强制删除
  rpm -e --nodeps jdk_xx_xx
  
  #安装java
  rpm -ivh rpm包
  
  [root@CentOS7 bobo]# rpm -ivh jdk-8u311-linux-x64.rpm 
  ```

- 配置环境变量 /etc/profile (rpm安装不需要配置)

  ```shell
  JAVA_HOME=/usr/java/jdk1.8.0_311-amd64
  CLASSPATH=%JAVA_HOME%/lib:%JAVA_HOME%/jre/bin
  PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
  export PATH CLASSPATH JAVA_HOME
  
  ```

  

#### Tomcat安装（压缩包安装）

- 下载Tomcat压缩包  apache-tomcat-9.0.56.tar.gz 
- 解压压缩包

```shell
tar -zxvf 包名

[root@CentOS7 bobo]# tar -zxvf apache-tomcat-9.0.56.tar.gz 
[root@CentOS7 bobo]# ls
apache-tomcat-9.0.56  apache-tomcat-9.0.56.tar.gz  jdk-8u311-linux-x64.rpm
```

- 启动Tomcat测试 ./xxx.sh脚本即可执行

```shell
#执行 ./startup.sh
#停止 ./shutdown.sh

[root@CentOS7 bin]# ls
bootstrap.jar       ciphers.bat                   configtest.bat  digest.sh         setclasspath.sh  startup.sh            tool-wrapper.sh
catalina.bat        ciphers.sh                    configtest.sh   makebase.bat      shutdown.bat     tomcat-juli.jar       version.bat
catalina.sh         commons-daemon.jar            daemon.sh       makebase.sh       shutdown.sh      tomcat-native.tar.gz  version.sh
catalina-tasks.xml  commons-daemon-native.tar.gz  digest.bat      setclasspath.bat  startup.bat      tool-wrapper.bat
[root@CentOS7 bin]# 
================================================
[root@CentOS7 bin]# ./startup.sh 
Using CATALINA_BASE:   /home/bobo/apache-tomcat-9.0.56
Using CATALINA_HOME:   /home/bobo/apache-tomcat-9.0.56
Using CATALINA_TMPDIR: /home/bobo/apache-tomcat-9.0.56/temp
Using JRE_HOME:        /usr
Using CLASSPATH:       /home/bobo/apache-tomcat-9.0.56/bin/bootstrap.jar:/home/bobo/apache-tomcat-9.0.56/bin/tomcat-juli.jar
Using CATALINA_OPTS:   
Tomcat started.


```

如果8080端口开了就可以直接访问

#### 防火墙相关命令

```shell
#查看防火墙状态
systemctl status firewalld

#开启，重启，关闭
service firewalld start
service firewalld restart
service firewalld stop

#查看防火墙规则
firewall-cmd --list-all  #查看全部信息
firewall-cmd --list-ports #只看端口信息

#开启端口
firewall-cmd --zone=public --add-port=80/tcp --permanent

#--zone作用域 --add-port=80/tcp端口/通信协议 --permanent永久生效

#重启防火墙
systemctl restart firewalld.service
```



- 