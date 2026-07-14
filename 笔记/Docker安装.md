# Docker安装

## 一、在线安装

#### 安装Docker

参考文档：[Install Docker Engine on CentOS | Docker Documentation](https://docs.docker.com/engine/install/centos/)

```shell
#删除旧版本docker
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
                  
#安装yum-utils并且配置仓库地址
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

#安装最新版docker
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#启动docker
sudo systemctl start docker
```

##### 常用指令

```shell
#列出所有的Docker容器
docker ps -a

#启动服务
docker compose up

#重启服务
docker restart fac905ed6e52

#停止和删除容器、网络、卷、镜像
docker compose down

#删除镜像
docker image rm 4c6b3cc10e6b  3e12e2ceb68f  ec0817395263  47c5b6ca1535
```



## 二、离线安装

### 1、下载离线安装包（根据系统架构选择x86或者aarch64）

- https://download.docker.com/linux/static/stable/x86_64/

### 2、解压安装包

- ```shell
  tar -zxvf docker-18.06.3-ce.tgz
  ```

### 3、复制文件

- 将docker中的全部文件，复制到/usr/bin

- ```shell
  cp ./docker/* /usr/bin
  ```

### 4、创建docker.service文件

- ```shell
  cd /etc/systemd/system/
  touch docker.service
  
  vim docker.service
  ```

- ~~文件内容(将其中的ip地址，改成私有仓库服务器地址，其它参数不用改。--insecure-registry=192.168.205.230)~~

- ```shell
  [Unit]
  Description=Docker Application Container Engine
  Documentation=https://docs.docker.com
  After=network-online.target firewalld.service
  Wants=network-online.target
   
  [Service]
  Type=notify
  # the default is not to use systemd for cgroups because the delegate issues still
  # exists and systemd currently does not support the cgroup feature set required
  # for containers run by docker
  ExecStart=/usr/bin/dockerd --selinux-enabled=false
  ExecReload=/bin/kill -s HUP $MAINPID
  # Having non-zero Limit*s causes performance problems due to accounting overhead
  # in the kernel. We recommend using cgroups to do container-local accounting.
  LimitNOFILE=infinity
  LimitNPROC=infinity
  LimitCORE=infinity
  # Uncomment TasksMax if your systemd version supports it.
  # Only systemd 226 and above support this version.
  #TasksMax=infinity
  TimeoutStartSec=0
  # set delegate yes so that systemd does not reset the cgroups of docker containers
  Delegate=yes
  # kill only the docker process, not all processes in the cgroup
  KillMode=process
  # restart the docker process if it exits prematurely
  Restart=on-failure
  StartLimitBurst=3
  StartLimitInterval=60s
   
  [Install]
  WantedBy=multi-user.target
  ```

### 5、赋权

- ```shell
  chmod a+x docker.service
  ```

### 6、加载服务

- ```shell
  systemctl daemon-reload
  ```

### 7、启动docker

- ```shell
  systemctl start docker
  ```

### 8、生成jar包镜像

- 编写Dockerfile文件

- ```shell
  FROM openjdk:8-jre
  ADD /radar-1.0.0-SNAPSHOT.jar /radar.jar
  COPY /application.yml /application.yml
  ENTRYPOINT ["java", "-jar","/radar.jar"]
  ```

- ```
  docker build -t wlwgxpt-radar:v1.0 -f  Dockerfile .
  ```

### 9、查看镜像

```she
docker images
```

### 10、给镜像添加标签

```shell
 docker tag b305c608f83a 10.193.201.8:20080/wlwgxpt/wlwgxpt-radar
```

### 11、登录私有仓库

```shell
docker login http://10.193.201.8:20080/wlwgxpt
```

### 12、推送镜像至私有仓库

```shell
docker push 10.193.201.8:20080/wlwgxpt/wlwgxpt-radar
```

### 13、导入导出镜像（save、load）

```shell
#导出
docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3
docker save 0c14a0e20aa3 > openjdk.tar  

#导入
docker load -i openjdk.tar 
```

### 14、运行镜像

```shell
docker run -d --name iot-front -p 1999:1999 -p 18000:80 iot-front:v1.0
```

### 15、进入镜像容器命令行

```shell
docker exec -it [container-name] bash
```

### 16、获取 Docker容器 或者 Docker镜像的元数据

```shell
docker inspect a7e3493d6a85
```

### 17、查看容器日志

```shell
docker logs a7e3493d6a85

#最近100条日志
docker logs -f --tail=100 容器ID
```

### 附录

[Docker 常用指令](https://haicoder.net/docker/docker-top.html)





ln -s /data/dockerData/docker /var/lib/docker