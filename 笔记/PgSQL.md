# PgSQL

### 命令行连接数据库：

- ./psql -U postgres -h 127.0.0.1 -p 5432

### 导出数据库：

```shell
pg_dump -U postgres -d hliot -p 5432 > /opt/hliot.sql
```

### 导出数据表：

```shel
pg_dump -U postgres -d hliot -t dev_device_instance -f dev_device_instance .sql
```

### 常用指令：

- \l  查看系统中现存的数据库 
- \c  切换库，如template1=# \c sales 从template1转到sales库
- \d  查看表和sequence
- \d  table_name，查看表结构，如：\d public.t_ip或\d t_ip，虽然`\d`看不到其他schema的表，但依然可以描述表`\d wechat.stat_basic_hour`
- \dt 只查看表
- \di 查看索引
- \du 查看有哪些用户
- \dn 查看schema
- \dp 显示表的权限分配情况
- \q 退出客户端程序psql
- \i 执行命令 \i hliot.sql
- SET SEARCH_PATH TO public,wechat;   设置搜索路径(不区分大小写，下同) 设置后`\d`能列出指定的schema的表和sequence
- SHOW SEARCH_PATH;    查看搜索模式
- SELECT USER;    查看当前是什么用户登录的
- postgres=# \conninfo    查看连接信息(什么用户连的)
  You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".
- postgres=#` 的提示符`postgres`表示当前数据库是`postgres`

### 查询语句:

```mysql
DELETE FROM dev_device_instance WHERE state = 'notActive';


SELECT pro."name",COUNT(*) FROM dev_device_instance dev LEFT JOIN dev_product pro ON dev.product_id = pro.id   GROUP BY product_id,pro."name";

UPDATE public.s_user SET username = 'operator' WHERE id = '5a105e8b9d40e1329780d62ea2265d8a';
```

