---
source: 批量关联OPCUA点位.txt
tags:
  - 物联网
  - OPCUA
  - SQL
---

# 批量关联OPCUA点位

```sql
SELECT 
name AS metadataId,
concat(name,'(',name,')') AS metadataName,
'1984204919433883648' AS collectorId,
'1984204919433883648' AS channelId,
'property' AS metadataType,
id AS pointId
FROM "opc_ua_point"
```
