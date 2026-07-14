---
source: opc_test.py
tags:
  - 代码
  - Python
  - OPC
---

# opc_test

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
KEPServerEX OPC UA 性能测试工具（官方 opcua 库版）
支持：
  - 无安全策略 (None)
  - 匿名访问
  - 单节点读取测试
  - 批量读取测试
"""

import time
import statistics
from datetime import datetime
from opcua import Client

class KEPServerEXBenchmark:
    def __init__(self, server_url, node_ids, test_count=1000):
        self.server_url = server_url
        self.node_ids = node_ids
        self.test_count = test_count
        self.client = None
        self.nodes = []

    def connect(self):
        """连接 KEPServerEX"""
        try:
            print(f"连接 KEPServerEX 服务器: {self.server_url}")
            self.client = Client(self.server_url)
            
            # 匿名访问
            self.client.set_user("anonymous")
            print("✓ 认证方式: anonymous")
            
            # 连接
            self.client.connect()
            print("✓ 连接成功！")
            
            # 缓存节点
            self.nodes = []
            for node_id in self.node_ids:
                try:
                    node = self.client.get_node(node_id)
                    value = node.get_value()
                    print(f"✓ 节点 {node_id}: 可读, 当前值={value}")
                    self.nodes.append(node)
                except Exception as e:
                    print(f"✗ 节点 {node_id}: 不可读 - {e}")
            
            if not self.nodes:
                print("错误: 没有可用的节点")
                return False
            
            print(f"✓ 已缓存 {len(self.nodes)} 个节点")
            return True

        except Exception as e:
            print(f"✗ 连接失败: {e}")
            return False

    def disconnect(self):
        """断开连接"""
        if self.client:
            self.client.disconnect()
            print("✓ 已断开连接")

    def test_single_node(self, node_index=0):
        """单节点读取测试"""
        read_times = []
        errors = []

        print(f"\n开始单节点性能测试: {self.node_ids[node_index]}, 测试次数: {self.test_count}")
        start_time = time.time()

        # 预热
        for _ in range(5):
            try:
                self.nodes[node_index].get_value()
            except:
                pass

        for i in range(self.test_count):
            try:
                t1 = time.perf_counter()
                value = self.nodes[node_index].get_value()
                t2 = time.perf_counter()
                read_times.append((t2 - t1) * 1000)  # ms
            except Exception as e:
                errors.append(f"第 {i+1} 次失败: {e}")

        duration = time.time() - start_time
        self._display_results("单节点测试", read_times, errors)

    def test_batch_nodes(self):
        """批量读取节点测试"""
        read_times = []
        errors = []

        print(f"\n开始批量读取测试: {len(self.nodes)} 个节点, 测试次数: {self.test_count}")
        start_time = time.time()

        # 预热
        for _ in range(3):
            try:
                [node.get_value() for node in self.nodes]
            except:
                pass

        for i in range(self.test_count):
            try:
                t1 = time.perf_counter()
                values = [node.get_value() for node in self.nodes]
                t2 = time.perf_counter()
                read_times.append((t2 - t1) * 1000)
            except Exception as e:
                errors.append(f"第 {i+1} 次失败: {e}")

        duration = time.time() - start_time
        self._display_results("批量读取测试", read_times, errors, batch_size=len(self.nodes))

    def _display_results(self, test_name, read_times, errors, batch_size=1):
        print("\n" + "="*60)
        print(f"KEPServerEX 性能测试结果 - {test_name}")
        print("="*60)

        if not read_times:
            print("没有成功的读取操作!")
            if errors:
                print("错误信息:")
                for e in errors:
                    print(f"  - {e}")
            return

        min_time = min(read_times)
        max_time = max(read_times)
        avg_time = statistics.mean(read_times)
        median_time = statistics.median(read_times)

        print(f"测试时间: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
        print(f"总测试次数: {self.test_count}")
        print(f"成功次数: {len(read_times)}")
        print(f"成功率: {len(read_times)/self.test_count*100:.2f}%")
        print(f"总耗时: {sum(read_times)/1000:.2f} 秒")
        print(f"平均每次读取: {avg_time:.3f} ms")
        if batch_size > 1:
            print(f"单节点平均读取时间: {avg_time/batch_size:.3f} ms")
        print(f"最小值: {min_time:.3f} ms, 最大值: {max_time:.3f} ms, 中位数: {median_time:.3f} ms")
        if errors:
            print(f"错误数量: {len(errors)}, 示例: {errors[:3]}")

# =============================
# 示例运行
# =============================
if __name__ == "__main__":
    SERVER_URL = "opc.tcp://10.43.64.106:8090"
    NODE_IDS = ["ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin"]  # 可修改为多个节点
    TEST_COUNT = 10

    benchmark = KEPServerEXBenchmark(SERVER_URL, NODE_IDS, TEST_COUNT)
    if benchmark.connect():
        benchmark.test_single_node()
        if len(NODE_IDS) > 1:
            benchmark.test_batch_nodes()
        benchmark.disconnect()
```
