# Kafka安装

- 解压压缩包文件

```shell
tar -zxvf kafka_2.12-2.5.0.gz
```

- 修改配置文件 

  - kafka需要安装zookeeper使用

  - 配置/config 下的“zookeeper.properties”  文件。修改dataDir和clientPort。前者是快照存放地址(自己随意配置)，后者是客户端连接zookeeper服务的端口。**默认端口2181** 最好默认不修改
  - 配置/config下的“server.properties”。修改log.dirs和zookeeper.connect。前者是日志存放文件夹，后者是zookeeper连接地址（端口和clientPort保持一致）。

- 开启kafka自带zookeeper

     前台运行：

```shell
./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```

   		后台运行：

```shell
./bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

- 开启kafka

  前台运行：

```shell
bin/kafka-server-start.sh config/server.properties
```

​		后台运行：

```shell
./bin/kafka-server-start.sh -daemon ./config/server.properties
```

- 使用jps命令查看是否正常