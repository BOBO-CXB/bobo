# Mysql安装

### 1.解压压缩包，并选择文件夹

### 2.配置环境变量，设置path路径

### 3.新建配置文件my.ini配置文件

```javascript
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=D:\Mysql\mysql-5.7.32 
# 设置mysql数据库的数据的存放目录
datadir= D:\Mysql\mysql-5.7.32\data
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 跳过登录密码验证
skip-grant-tables
```

### 4.用管理员的身份打开cmd，进入bin目录（D:\Mysql\mysql-5.7.32\bin）运行命令

#### cd /d D:\Mysql\mysql-5.7.32\bin

### 命令：

#### mysqld  -install

- 安装mysql服务，Service successfully installed.

#### mysqld --initialize-insecure --user=mysql

- 初始化数据库文件，生成data目录文件

#### net start mysql

- 启动mysql服务

#### mysql -u root -p

- 无密码登陆mysql，成功后光标变为mysql>

#### mysql> update mysql.user set authentication_string=password(123456') where user='root' and Host = 'localhost';

- 更新密码

#### flush privileges;

- 刷新权限

#### 注释配置文件

#### 重启mysql服务 net stop mysql  net start mysql



### IDEA连接url：

##### 		jdbc:mysql://localhost:3306/db_bookstore?useSSL=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai