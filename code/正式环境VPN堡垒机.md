---
source: 正式环境VPN堡垒机.txt
tags:
  - 运维
  - VPN
  - 堡垒机
  - 火车北站
  - 账号密码
---

# 正式环境 VPN 堡垒机

> [!warning] 敏感信息
> 含 VPN、堡垒机、服务器、数据库、各平台密钥。

## 部署命令
```bash
scp event-center-server.jar  root@10.23.193.248:/home/app/bin
scp -r event-app/  root@10.23.193.248:/home/app/uni
# 服务器密码：LJcloud@1370
scp -r dist/ root@10.23.193.248:/home/app/
mv dist/ web
cp config/IPConfig.js web/
# 可把要传输的文件放到 10.23.193.85 服务器上，再通过 scp 传输
scp -r web/ root@10.23.193.248:/home/app/
scp -r event-app/  root@10.23.193.248:/home/app/uni
```

## 1. 连接 VPN
- 工具：EasyConnect
- 地址：https://222.180.168.210:8443

> [!info] 账号 / 密码
> `bzzgj-user03` / `Cqbz-admin@20261030-user03`
> `bzzgj-user04` / `Cqbz-admin@20261030-user04`
> `bzzgj-user05` / `Cqbz-admin@20261030-user05`
> `bzzgj-user06` / `Cqbz-admin@20261030-user06`

## 2. 登陆堡垒机
- VPN 连接后访问堡垒机
- 地址：https://10.216.1.254:64443
- 备用：`hcbzdsjznpt-user03` / `Cqbz-03zw@2024`

> [!info] 政务网正式环境备用 VPN
> 用户名：`cqbz-dsj-user01`
> 密码：`asd31020`
> 堡垒机：`cqbz-dsj-user01` / `OTEyNTRhNT001`

## 3. 服务器列表

### 火车北站可视化（GWH）
| 用途 | IP | 账号 | 密码 |
|---|---|---|---|
| 事窗管理系统 | 10.23.193.84 | root | LJcloud@1370 |
| 大屏可视化 | 10.23.193.85 | root | LJcloud@1370 |
| 预留 | 10.23.193.86 | root | LJcloud@1370 |

### 事件中心
| 用途 | IP | 账号 | 密码 |
|---|---|---|---|
| 前端服务 001 | 10.23.193.247 | root | LJcloud@1370 |
| 服务端-主 | 10.23.193.248 | root | LJcloud@1370 |
| 服务端-从 | 10.23.193.249 | root | LJcloud@1370 |

- `23.93.201.11:49001`
- app：`23.93.201.11:49002`、`23.93.201.11:80`

### 数据库
- **业务系统数据库**：`10.23.193.93`，账号 `cqbz_rw` / `sjztrds@123`
- **事件中心数据库**：`10.23.193.54:3306`，账号 `root` / `lingyi@2022`
- **RDS（`10.23.193.83:3306`）新增 3 库**：
  - `sjzx` / `sjzx@2024`（库 `sjzx`，表 `cas_user_center`，字段 `phone`、`password`）
  - `mnyl` / `mnyl@2024`
  - `lkx` / `lkx@2024`

### 公用 Windows 服务器（测试页面/数据库）
- IP：`10.23.193.1`，登录选 RDP
- 管理员：`Administrator` / `LJcloud@1370zt`
- 用户1：`Haojing01` / `Haojing1@cq`
- 用户2：`Haojing02` / `Haojing2@cq`

### 管理工具与地址
- 大屏管理工具：http://10.23.193.85:8080/#/
- 事窗：http://10.23.193.84:9086/#/login ｜ http://23.93.201.11:8086/#/ （`admin` / `cqbzxm@123`）
- 地图：http://10.23.193.77/station-n-map/#/VisualGisView
- 地图配置：`mapUrl: 'http://23.93.201.11:8088/station-n-map/#/VisualGisView'`
- WebSocket：`ws://23.93.201.11:7081/api/websocket/100`
- 事件外链：http://23.93.201.11/event_outer/#/case/caseList

### 凭证与密钥
- **App Key**：`cqbzzz-pbbH0JmF5cpHQ98aeUV3iaq`
- **App Secret**：`c3O9GNyd8gi6xXHgMMECE6sVezAlqX75m0TErt2y`
- **渝快政管理员**：`13648382871` / `zgj@123456`
- **渝快政应用密钥**
    - 域名：`zd-openplatform.bigdatacq.com`
    - App Key：`cqbzdsjjpt-ljxq-2ap7ka1dy2aeUj`
    - App Secret：`j8OG3R7VmltTX18uZ0nSkwwSJwk718H9H2sBA35F`
- **统一用户中心密钥**
    - App ID：`G2LiBZYhIVTyvFdI4uvVwi3auhuAWrXB`
    - App Secret：`3ZF2CUi5B3ZvAllrd4zF9870GY46ztKo`
- **OSS**
    - Endpoint：http://oss-cn-chongqing-ljyth-d01-a.ops.ljcloud.com/
    - AccessKey ID：`LRvCh1Gw0Fnv57MG`
    - AccessKey Secret：`ijzgQJLznVc8BHyBBGYsXDGjgQNC24`

### 截图资源 URL
- http://192.168.0.240:9000/caster/upload/20230518/2e7ad29e4d4697422346c9eb6a4ec047.png
- http://10.23.193.85:9000/caster/upload/20230510/2c9a1558577a09ae25375af993cfba86.png
- http://10.23.193.85:8080/oss/caster/upload/20230510/2c9a1558577a09ae25375af993cfba86.png
- http://23.93.201.11:8088/oss/caster/upload/20230510/2c9a1558577a09ae25375af993cfba86.png
- http://222.180.171.219:8088/oss/caster/upload/20230614/48f6b05482e177ad90564935befb18b1.png
- http://222.180.171.219:8088/oss/caster/upload/20230923/326731991b2d86373f659f504b236b4e.png
- http://222.180.171.219:8088/oss/caster/upload/20230923/f4e2f23a1fd2246bda7ec3919df5c0cc.png
- http://192.168.0.240:9000/caster/upload/20230923/033ead6c2e8b1f402edb9bf7d90a7f92.png

### 其他命令与代码
```bash
# 安装本地 jar 到 maven
mvn install:install-file -Dfile=./zwdd-sdk-java-1.2.0.jar -DgroupId=com.zwdd -DartifactId=zwdd-sdk-java -Dversion=1.2.0 -Dpackaging=jar
```

```javascript
if(url.indexOf("http") < 0 && url.indexOf("https") < 0){
  item.fileUrlList.push(IPConfig.ossUrl + url)
}
```
