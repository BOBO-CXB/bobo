---
source: import asyncio.py
tags:
  - 代码
  - Python
---

# import asyncio

```python
#!/usr/bin/env python3
"""
OPC UA KEPServerEX 性能测试工具
针对KEPServerEX V6优化
"""

import asyncio
import time
import statistics
import sys
from datetime import datetime
from asyncua import Client

class KEPServerEXBenchmark:
    def __init__(self, server_url, node_ids, test_count=1000):
        """
        初始化KEPServerEX性能测试器
        
        Args:
            server_url: OPC UA服务器地址
            node_ids: 节点ID列表
            test_count: 测试次数
        """
        self.server_url = server_url
        self.node_ids = node_ids
        self.test_count = test_count
        self.client = None
        self.nodes = []
        
    async def connect_kepserverex(self):
        """连接KEPServerEX服务器 - 专门针对KEPServerEX优化"""
        print(f"连接KEPServerEX服务器: {self.server_url}")
        print("配置: 无安全策略 + 匿名认证")
        
        try:
            # 创建客户端
            self.client = Client(self.server_url)
            
            # KEPServerEX通常配置为无安全策略
         
            # 正确设置匿名访问
            try:
                self.client.set_user('anonymous')
                print("✓ 认证方式: anonymous")
            except Exception as e:
                print(f"⚠ 设置匿名认证失败: {e}")

            self.client.timeout = 30

            # 连接
            try:
                await self.client.connect()
                print("✓ 连接成功")
            except Exception as e:
                print(f"✗ 连接失败: {e}")
            
            # 显示服务器信息
            try:
                server_node = self.client.nodes.server
                server_name = await server_node.read_attribute("ServerName")
                server_version = await server_node.read_attribute("BuildInfo")
                print(f"服务器: {server_name.Value.Value}")
                print(f"版本: {server_version.Value.ProductName} {server_version.Value.ProductVersion}")
            except:
                pass
            
            # 缓存节点
            print(f"验证节点...")
            self.nodes = []
            for node_id in self.node_ids:
                try:
                    node = self.client.get_node(node_id)
                    # 快速测试节点是否可读
                    value = await node.read_value()
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
    
    async def disconnect(self):
        """断开连接"""
        if self.client:
            try:
                await self.client.disconnect()
                print("✓ 已断开连接")
            except:
                pass
    
    async def test_single_read(self, node_index=0):
        """测试单节点读取"""
        try:
            start_time = time.perf_counter()
            value = await self.nodes[node_index].read_value()
            end_time = time.perf_counter()
            read_time = (end_time - start_time) * 1000  # 转换为毫秒
            return read_time, value
        except Exception as e:
            return None, str(e)
    
    async def test_batch_read(self):
        """测试批量读取"""
        try:
            start_time = time.perf_counter()
            values = await self.client.read_values(self.nodes)
            end_time = time.perf_counter()
            read_time = (end_time - start_time) * 1000  # 转换为毫秒
            return read_time, values
        except Exception as e:
            return None, str(e)
    
    async def run_single_node_test(self):
        """运行单节点性能测试"""
        if not self.nodes:
            print("错误: 没有可用的节点")
            return
        
        print(f"\n开始单节点性能测试")
        print(f"节点: {self.node_ids[0]}")
        print(f"测试次数: {self.test_count}")
        print("-" * 50)
        
        read_times = []
        successful_reads = 0
        errors = []
        
        # 预热
        print("预热阶段...")
        for i in range(5):
            read_time, value = await self.test_single_read()
            if read_time is not None:
                print(f"预热 {i+1}: {read_time:.2f} ms")
            await asyncio.sleep(0.1)
        
        print("\n开始正式测试...")
        start_test_time = time.time()
        
        for i in range(self.test_count):
            read_time, value = await self.test_single_read()
            
            if read_time is not None:
                read_times.append(read_time)
                successful_reads += 1
                
                # 显示进度
                if (i + 1) % 100 == 0:
                    print(f"已测试 {i + 1}/{self.test_count} 次")
            else:
                errors.append(f"第 {i + 1} 次读取失败: {value}")
            
            # 小延迟避免对服务器造成过大压力
            await asyncio.sleep(0.001)
        
        total_duration = time.time() - start_test_time
        self._display_results("单节点测试", read_times, successful_reads, total_duration, errors)
    
    async def run_batch_test(self, batch_size=10):
        """运行批量读取性能测试"""
        if not self.nodes:
            print("错误: 没有可用的节点")
            return
        
        actual_batch_size = min(batch_size, len(self.nodes))
        print(f"\n开始批量读取性能测试")
        print(f"批处理大小: {actual_batch_size} 个节点")
        print(f"测试次数: {self.test_count}")
        print("-" * 50)
        
        read_times = []
        successful_reads = 0
        errors = []
        
        # 预热
        print("预热阶段...")
        for i in range(3):
            read_time, values = await self.test_batch_read()
            if read_time is not None:
                print(f"预热 {i+1}: {read_time:.2f} ms, 读取了 {len(values)} 个值")
            await asyncio.sleep(0.1)
        
        print("\n开始正式测试...")
        start_test_time = time.time()
        
        for i in range(self.test_count):
            read_time, values = await self.test_batch_read()
            
            if read_time is not None:
                read_times.append(read_time)
                successful_reads += 1
                
                # 显示进度
                if (i + 1) % 50 == 0:
                    print(f"已测试 {i + 1}/{self.test_count} 批次")
            else:
                errors.append(f"第 {i + 1} 次批量读取失败")
            
            # 小延迟
            await asyncio.sleep(0.001)
        
        total_duration = time.time() - start_test_time
        self._display_results("批量读取测试", read_times, successful_reads, total_duration, errors, actual_batch_size)
    
    def _display_results(self, test_name, read_times, successful_reads, total_duration, errors, batch_size=1):
        """显示测试结果"""
        print("\n" + "=" * 60)
        print(f"KEPServerEX 性能测试结果 - {test_name}")
        print("=" * 60)
        
        if not read_times:
            print("没有成功的读取操作!")
            if errors:
                print(f"\n错误信息 ({len(errors)} 个):")
                for error in errors[:5]:
                    print(f"  - {error}")
            return
        
        # 基本统计
        min_time = min(read_times)
        max_time = max(read_times)
        avg_time = statistics.mean(read_times)
        median_time = statistics.median(read_times)
        
        # 计算百分位数
        try:
            p95 = statistics.quantiles(read_times, n=20)[18]  # 95th percentile
            p99 = statistics.quantiles(read_times, n=100)[98]  # 99th percentile
        except:
            p95 = avg_time
            p99 = avg_time
        
        # 计算性能指标
        total_points = successful_reads * batch_size
        read_rate = total_points / total_duration if total_duration > 0 else 0
        avg_batch_time = avg_time / batch_size if batch_size > 0 else 0
        
        print(f"测试时间: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
        print(f"服务器: {self.server_url}")
        print(f"总测试次数: {self.test_count}")
        print(f"成功次数: {successful_reads}")
        print(f"成功率: {(successful_reads/self.test_count*100):.2f}%")
        print(f"总读取点数: {total_points}")
        print(f"总耗时: {total_duration:.2f} 秒")
        print(f"平均读取速率: {read_rate:.2f} 点/秒")
        
        if batch_size > 1:
            print(f"单点平均读取时间: {avg_batch_time:.3f} ms")
        
        print("\n--- 响应时间统计 (毫秒) ---")
        print(f"最小值: {min_time:.3f} ms")
        print(f"最大值: {max_time:.3f} ms")
        print(f"平均值: {avg_time:.3f} ms")
        print(f"中位数: {median_time:.3f} ms")
        print(f"95百分位: {p95:.3f} ms")
        print(f"99百分位: {p99:.3f} ms")
        
        # 显示时间分布
        print("\n--- 响应时间分布 ---")
        bins = [0, 10, 20, 50, 100, 200, 500, 1000, 2000, float('inf')]
        labels = ["<10ms", "10-20ms", "20-50ms", "50-100ms", "100-200ms", 
                 "200-500ms", "500-1000ms", "1-2s", ">2s"]
        
        for i in range(len(bins)-1):
            count = len([t for t in read_times if bins[i] <= t < bins[i+1]])
            if count > 0:
                percentage = (count / len(read_times)) * 100
                print(f"{labels[i]:<10} {count:4d} 次 ({percentage:6.2f}%)")
        
        # 显示错误信息
        if errors:
            print(f"\n--- 错误信息 ({len(errors)} 个错误) ---")
            for error in errors[:3]:
                print(f"  - {error}")
            if len(errors) > 3:
                print(f"  ... 还有 {len(errors) - 3} 个错误")
        
        # 保存结果
        self._save_results(test_name, read_times, successful_reads, total_duration, errors, batch_size)
    
    def _save_results(self, test_name, read_times, successful_reads, total_duration, errors, batch_size):
        """保存测试结果到文件"""
        filename = f"kepserverex_test_{datetime.now().strftime('%Y%m%d_%H%M%S')}.txt"
        
        try:
            with open(filename, 'w', encoding='utf-8') as f:
                f.write("=" * 60 + "\n")
                f.write(f"KEPServerEX 性能测试报告 - {test_name}\n")
                f.write("=" * 60 + "\n")
                f.write(f"测试时间: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}\n")
                f.write(f"服务器: {self.server_url}\n")
                f.write(f"测试节点数量: {len(self.node_ids)}\n")
                f.write(f"批处理大小: {batch_size}\n")
                f.write(f"总测试次数: {self.test_count}\n")
                f.write(f"成功次数: {successful_reads}\n")
                f.write(f"成功率: {(successful_reads/self.test_count*100):.2f}%\n")
                f.write(f"总读取点数: {successful_reads * batch_size}\n")
                f.write(f"总耗时: {total_duration:.2f} 秒\n")
                
                if read_times:
                    avg_time = statistics.mean(read_times)
                    read_rate = (successful_reads * batch_size) / total_duration if total_duration > 0 else 0
                    f.write(f"平均读取速率: {read_rate:.2f} 点/秒\n")
                    f.write(f"平均响应时间: {avg_time:.3f} ms\n")
                
                if errors:
                    f.write(f"\n错误数量: {len(errors)}\n")
                    for error in errors[:10]:
                        f.write(f"  - {error}\n")
                
                f.write("\n详细响应时间数据 (ms):\n")
                for i, t in enumerate(read_times):
                    f.write(f"{i+1:4d}: {t:.3f}\n")
            
            print(f"\n测试结果已保存到: {filename}")
        except Exception as e:
            print(f"保存结果失败: {e}")

async def interactive_test():
    """交互式测试"""
    print("=" * 60)
    print("KEPServerEX OPC UA 性能测试工具")
    print("=" * 60)
    
    # 默认配置
    SERVER_URL = "opc.tcp://10.43.64.106:8090"
    DEFAULT_NODE = "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin"
    
    print(f"\n默认配置:")
    print(f"  服务器地址: {SERVER_URL}")
    print(f"  测试节点: {DEFAULT_NODE}")
    
    use_default = input("\n使用默认配置? (y/n): ").strip().lower()
    
    if use_default != 'y':
        SERVER_URL = input("请输入OPC UA服务器地址: ").strip() or SERVER_URL
        node_input = input("请输入节点ID(多个节点用逗号分隔): ").strip()
        if node_input:
            node_ids = [n.strip() for n in node_input.split(',')]
        else:
            node_ids = [DEFAULT_NODE]
    else:
        node_ids = [DEFAULT_NODE]
    
    # 测试类型
    print("\n选择测试类型:")
    print("  1. 单节点性能测试")
    print("  2. 批量读取性能测试")
    print("  3. 两种都测试")
    
    test_type = input("\n请选择 (1-3): ").strip()
    
    # 测试次数
    while True:
        try:
            test_count = input("\n请输入测试次数 (默认1000): ").strip()
            test_count = int(test_count) if test_count else 1000
            if test_count > 0:
                break
            else:
                print("测试次数必须大于0")
        except ValueError:
            print("请输入有效的数字")
    
    # 创建测试器
    benchmark = KEPServerEXBenchmark(SERVER_URL, node_ids, test_count)
    
    try:
        # 连接服务器
        print(f"\n正在连接到服务器: {SERVER_URL}")
        connected = await benchmark.connect_kepserverex()
        
        if not connected:
            print("连接失败，测试中止")
            return
        
        # 根据选择运行测试
        if test_type in ['1', '3']:
            # 单节点测试
            print(f"\n{'='*60}")
            print("开始单节点性能测试")
            print(f"节点: {node_ids[0]}")
            print(f"测试次数: {test_count}")
            print('='*60)
            
            confirm = input("\n确认开始单节点测试? (y/n): ").strip().lower()
            if confirm == 'y':
                await benchmark.run_single_node_test()
            else:
                print("单节点测试取消")
        
        if test_type in ['2', '3']:
            # 批量测试
            print(f"\n{'='*60}")
            print("开始批量读取性能测试")
            print(f"节点数量: {len(node_ids)}")
            print(f"测试次数: {test_count}")
            print('='*60)
            
            batch_size = len(node_ids)
            if batch_size == 1:
                # 如果只有一个节点，询问批量大小
                while True:
                    try:
                        batch_input = input("请输入批量读取大小 (默认10): ").strip()
                        batch_size = int(batch_input) if batch_input else 10
                        if batch_size > 0:
                            break
                        else:
                            print("批量大小必须大于0")
                    except ValueError:
                        print("请输入有效的数字")
            
            confirm = input(f"\n确认开始批量测试 (批量大小={batch_size})? (y/n): ").strip().lower()
            if confirm == 'y':
                await benchmark.run_batch_test(batch_size=batch_size)
            else:
                print("批量测试取消")
        
    except KeyboardInterrupt:
        print("\n\n测试被用户中断")
    except Exception as e:
        print(f"\n发生错误: {e}")
        import traceback
        traceback.print_exc()
    finally:
        # 断开连接
        await benchmark.disconnect()
        
    input("\n按回车键退出...")

async def quick_test():
    """快速连接测试"""
    SERVER_URL = "opc.tcp://10.43.64.106:8090"
    NODE_ID = "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin"
    
    print("KEPServerEX快速连接测试")
    print("=" * 50)
    print(f"服务器: {SERVER_URL}")
    print(f"节点: {NODE_ID}")
    print("=" * 50)
    
    benchmark = KEPServerEXBenchmark(SERVER_URL, [NODE_ID], 10)
    
    try:
        print("\n连接服务器...")
        connected = await benchmark.connect_kepserverex()
        
        if not connected:
            print("连接失败")
            return
        
        print("\n测试10次读取...")
        await benchmark.run_single_node_test()
        
    except Exception as e:
        print(f"测试失败: {e}")
    finally:
        await benchmark.disconnect()

async def production_benchmark():
    """生产环境性能基准测试"""
    SERVER_URL = "opc.tcp://10.43.64.106:8090"
    
    # 生产环境常用的测试节点
    NODE_IDS = [
        "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin",
        "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin",  # 重复同一个节点，模拟多个点位
        "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin",
        "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin",
        "ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin",
    ]
    
    print("KEPServerEX生产环境性能基准测试")
    print("=" * 60)
    print(f"服务器: {SERVER_URL}")
    print(f"节点数量: {len(NODE_IDS)} (模拟实际场景)")
    print("=" * 60)
    
    # 创建测试器
    benchmark = KEPServerEXBenchmark(SERVER_URL, NODE_IDS, 500)
    
    try:
        # 连接
        print("\n连接服务器...")
        connected = await benchmark.connect_kepserverex()
        
        if not connected:
            print("连接失败")
            return
        
        # 1. 单节点基准测试
        print("\n" + "="*60)
        print("阶段1: 单节点基准测试 (200次)")
        print("="*60)
        
        benchmark.test_count = 200
        await benchmark.run_single_node_test()
        
        # 2. 批量读取测试
        print("\n" + "="*60)
        print("阶段2: 批量读取测试 (200次)")
        print("="*60)
        
        benchmark.test_count = 200
        await benchmark.run_batch_test(batch_size=len(NODE_IDS))
        
        # 3. 长时间稳定性测试
        print("\n" + "="*60)
        print("阶段3: 稳定性测试 (100次，间隔0.5秒)")
        print("="*60)
        
        print("模拟实际采集场景，每0.5秒读取一次...")
        
        read_times = []
        for i in range(100):
            read_time, value = await benchmark.test_single_read()
            if read_time is not None:
                read_times.append(read_time)
                if (i + 1) % 10 == 0:
                    print(f"  已采集 {i + 1}/100 次，当前: {read_time:.2f} ms")
            else:
                print(f"  第 {i + 1} 次采集失败")
            
            await asyncio.sleep(0.5)  # 模拟实际采集间隔
        
        if read_times:
            avg = statistics.mean(read_times)
            max_t = max(read_times)
            print(f"\n稳定性测试结果:")
            print(f"  平均响应时间: {avg:.2f} ms")
            print(f"  最大响应时间: {max_t:.2f} ms")
            print(f"  数据丢失率: {(100 - len(read_times))/100*100:.1f}%")
        
    except Exception as e:
        print(f"基准测试失败: {e}")
    finally:
        await benchmark.disconnect()

def show_help():
    """显示帮助信息"""
    print("=" * 60)
    print("KEPServerEX OPC UA 性能测试工具 - 使用说明")
    print("=" * 60)
    print("\n使用方法:")
    print("  python kepserverex_benchmark.py          # 交互式测试")
    print("  python kepserverex_benchmark.py --quick  # 快速连接测试")
    print("  python kepserverex_benchmark.py --prod   # 生产环境基准测试")
    print("  python kepserverex_benchmark.py --help   # 显示帮助")
    print("\n默认配置:")
    print("  服务器: opc.tcp://10.43.64.106:8090")
    print("  节点: ns=2;s=JYJ.JYJ1-6.HIDPressureDropStopLimitMin")
    print("  安全策略: None (无安全策略)")
    print("  认证方式: anonymous (匿名)")
    print("\n说明:")
    print("  本工具专门针对KEPServerEX V6优化")
    print("  支持单节点测试、批量读取测试和稳定性测试")

if __name__ == "__main__":
    if len(sys.argv) == 1:
        # 交互模式
        asyncio.run(interactive_test())
    elif len(sys.argv) == 2:
        arg = sys.argv[1]
        if arg == "--quick" or arg == "-q":
            asyncio.run(quick_test())
        elif arg == "--prod" or arg == "-p":
            asyncio.run(production_benchmark())
        elif arg == "--help" or arg == "-h":
            show_help()
        else:
            print(f"未知参数: {arg}")
            print("使用 --help 查看帮助")
    else:
        print("参数过多")
        print("使用 --help 查看帮助")
```
