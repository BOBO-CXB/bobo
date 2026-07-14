# SNMP

### 进入系统视图

- system-view

### 设置snmp协议版本

- snmp-agent sys-info version v2

### 设置团体名

- snmp-agent community read public

### 设置允许访问的设备IP，发送Trap报文，使用的团体名为public

- snmp-agent target-host trap address udp-domain 192.168.0.19 udp-port 5000 params securityname public 

### 退出-保存

- quit
- save



```shell
华为交换机LLDP模块的MIB文件有两个，一个为HUAWEI-LLDP-MIB.mib，一个为LLDP-MIB.mib。
设备默认配置中，SNMP只包含有internet树下面的所有节点。LLDP-MIB中的邻居信息等节点不在internet树下，因此需要配置SNMP的mib视图，配置完成后，iso下所有的节点都可以通过SNMP被访问。
如果需要通过SNMP获取LLDP的邻居信息，需要在设备上配置以下命令：

snmp-agent mib-view included iso-view iso
snmp-agent community read `communitystring` mib-view iso-view 
snmp-agent sys-info version all
```





### LLDP oid

OID: 1.0.8802.1.1.2.1.4