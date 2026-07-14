---
source: from opcua import Client.py
tags:
  - 代码
  - Python
  - OPCUA
---

# from opcua import Client

```python
from opcua import Client

client = Client("opc.tcp://10.43.64.106:8090")
client.set_user("anonymous")
client.connect()
node = client.get_node("ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin")
print(node.get_value())
client.disconnect()
```
