---
source: opc.py
tags:
  - 代码
  - Python
  - OPC
---

# opc

```python
import pandas as pd
from opcua import Client
from opcua.ua import NodeClass
import logging
import time
import sys
from datetime import datetime

class AdvancedOPCUAClient:
    def __init__(self, endpoint_url, username=None, password=None):
        self.endpoint_url = endpoint_url
        self.client = Client(endpoint_url)
        
        # 安全配置
        if username and password:
            self.client.set_user(username)
            self.client.set_password(password)
        
        self.nodes_data = []
        self.browse_count = 0
        
    def connect(self):
        """连接到OPC UA服务器"""
        try:
            print(f"🔗 连接到: {self.endpoint_url}")
            self.client.connect()
            print("✅ 连接成功!")
            return True
        except Exception as e:
            print(f"❌ 连接失败: {e}")
            return False
    
    def disconnect(self):
        """断开连接"""
        try:
            self.client.disconnect()
            print("🔌 已断开连接")
        except:
            pass
    
    def get_node_details(self, node):
        """获取节点的详细信息"""
        try:
            node_id = node.nodeid.to_string()
            display_name = node.get_display_name().Text
            node_class = str(node.get_node_class())
            
            # 获取浏览名称
            browse_name = "N/A"
            try:
                browse_name = node.get_browse_name().Name
            except:
                pass
            
            # 初始化详细信息
            details = {
                "NodeID": node_id,
                "DisplayName": display_name,
                "BrowseName": browse_name,
                "NodeClass": node_class,
                "DataType": "N/A",
                "CurrentValue": "N/A",
                "Description": "N/A",
                "Writeable": "N/A"
            }
            
            # 变量节点的特殊处理
            if node.get_node_class() == NodeClass.Variable:
                try:
                    value = node.get_value()
                    details["CurrentValue"] = str(value)
                    
                    data_type_node = node.get_data_type()
                    data_type_name = data_type_node.get_display_name().Text
                    details["DataType"] = data_type_name
                    
                    # 检查是否可写
                    access_level = node.get_access_level()
                    details["Writeable"] = "是" if access_level & 2 else "否"
                    
                except Exception as e:
                    details["CurrentValue"] = f"读取错误: {e}"
            
            # 获取描述
            try:
                description = node.get_description()
                if description and description.Text:
                    details["Description"] = description.Text
            except:
                pass
            
            return details
            
        except Exception as e:
            print(f"⚠️ 获取节点信息失败: {e}")
            return None
    
    def find_newconnection_node(self):
        """查找BrowseName为NewConnection_1的节点"""
        print("🔍 正在查找NewConnection_1节点...")
        root = self.client.get_root_node()
        
        # 递归查找BrowseName为NewConnection_1的节点
        def find_node_recursive(node, target_browse_name, depth=0, max_depth=10):
            if depth > max_depth:
                return None
                
            try:
                # 获取当前节点的BrowseName
                browse_name = node.get_browse_name().Name
                if browse_name == target_browse_name:
                    print(f"✅ 找到目标节点: {browse_name}")
                    return node
                
                # 递归查找子节点
                children = node.get_children()
                for child in children:
                    result = find_node_recursive(child, target_browse_name, depth+1, max_depth)
                    if result:
                        return result
            except Exception as e:
                pass
                
            return None
        
        return find_node_recursive(root, "NewConnection_1")
    
    def browse_newconnection_nodes(self, newconnection_node, max_depth=5):
        """浏览NewConnection_1节点下的所有子节点"""
        print("🔄 开始扫描NewConnection_1下的节点...")
        self.nodes_data = []
        self.browse_count = 0
        
        def browse_recursive(node, current_depth=0, path=""):
            if current_depth > max_depth:
                return
                
            try:
                # 获取节点信息
                node_info = self.get_node_details(node)
                if node_info:
                    node_info["Depth"] = current_depth
                    node_info["Path"] = path
                    self.nodes_data.append(node_info)
                    self.browse_count += 1
                    
                    if self.browse_count % 50 == 0:
                        print(f"📊 已扫描 {self.browse_count} 个节点...")
                
                # 递归浏览子节点
                if current_depth < max_depth:
                    children = node.get_children()
                    for child in children:
                        try:
                            child_name = child.get_display_name().Text
                            new_path = f"{path}/{child_name}" if path else child_name
                            browse_recursive(child, current_depth + 1, new_path)
                        except Exception as e:
                            print(f"⚠️ 浏览子节点失败: {e}")
                            continue
                            
            except Exception as e:
                print(f"⚠️ 浏览节点时出错: {e}")
        
        # 开始浏览
        start_time = time.time()
        browse_recursive(newconnection_node, 0, "NewConnection_1")
        end_time = time.time()
        
        print(f"✅ 扫描完成! 共找到 {len(self.nodes_data)} 个节点, 耗时: {end_time - start_time:.2f} 秒")
        return self.nodes_data
    
    def export_to_excel(self, filename=None, max_depth=5):
        """导出NewConnection_1下的节点信息到Excel文件"""
        # 查找NewConnection_1节点
        newconnection_node = self.find_newconnection_node()
        if not newconnection_node:
            print("❌ 未找到BrowseName为NewConnection_1的节点")
            return False
            
        if filename is None:
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            filename = f"NewConnection_1_nodes_{timestamp}.xlsx"
        
        # 浏览NewConnection_1下的节点
        nodes_data = self.browse_newconnection_nodes(newconnection_node, max_depth)
        
        if not nodes_data:
            print("❌ 在NewConnection_1下没有找到任何节点")
            return False
        
        # 创建DataFrame并排序
        df = pd.DataFrame(nodes_data)
        df = df.sort_values(["Depth", "Path"])
        
        # 创建Excel文件
        try:
            with pd.ExcelWriter(filename, engine='openpyxl') as writer:
                # 主数据表
                df.to_excel(writer, sheet_name='NewConnection_1节点', index=False)
                
                # 创建按节点类型分组的表
                if 'NodeClass' in df.columns:
                    node_class_summary = df['NodeClass'].value_counts().reset_index()
                    node_class_summary.columns = ['节点类型', '数量']
                    node_class_summary.to_excel(writer, sheet_name='类型统计', index=False)
                
                # 变量节点表
                if 'NodeClass' in df.columns:
                    variable_nodes = df[df['NodeClass'].str.contains('Variable')]
                    variable_nodes.to_excel(writer, sheet_name='变量节点', index=False)
                
                # 自动调整列宽
                for sheet_name in writer.sheets:
                    worksheet = writer.sheets[sheet_name]
                    for column in worksheet.columns:
                        max_length = 0
                        column_letter = column[0].column_letter
                        for cell in column:
                            try:
                                max_length = max(max_length, len(str(cell.value)))
                            except:
                                pass
                        adjusted_width = min(max_length + 2, 50)
                        worksheet.column_dimensions[column_letter].width = adjusted_width
            
            print(f"💾 数据已导出到: {filename}")
            
            # 显示统计信息
            self.show_statistics(df)
            return True
            
        except Exception as e:
            print(f"❌ 导出失败: {e}")
            return False
    
    def show_statistics(self, df):
        """显示统计信息"""
        print("\n📈 统计信息:")
        print(f"总节点数: {len(df)}")
        
        if 'NodeClass' in df.columns:
            print("\n节点类型分布:")
            for node_class, count in df['NodeClass'].value_counts().items():
                print(f"  {node_class}: {count}")
        
        if 'DataType' in df.columns:
            variable_nodes = df[df['NodeClass'].str.contains('Variable', na=False)]
            if not variable_nodes.empty:
                print(f"\n变量节点数: {len(variable_nodes)}")
                print("数据类型分布:")
                for data_type, count in variable_nodes['DataType'].value_counts().head(10).items():
                    print(f"  {data_type}: {count}")

def main():
    # OPC UA服务器配置
    ENDPOINT_URL = "opc.tcp://192.168.203.218:4862"
    OUTPUT_FILENAME = "NewConnection_1_nodes.xlsx"
    MAX_BROWSE_DEPTH = 5  # 浏览深度
    
    # 创建客户端
    client = AdvancedOPCUAClient(endpoint_url=ENDPOINT_URL)
    
    try:
        # 连接服务器
        if not client.connect():
            sys.exit(1)
        
        # 导出数据
        success = client.export_to_excel(
            filename=OUTPUT_FILENAME,
            max_depth=MAX_BROWSE_DEPTH
        )
        
        if success:
            print("\n🎉 操作完成!")
        else:
            print("\n💥 操作失败!")
            sys.exit(1)
            
    except KeyboardInterrupt:
        print("\n⏹️ 用户中断操作")
    except Exception as e:
        print(f"\n❌ 程序执行出错: {e}")
        import traceback
        traceback.print_exc()  # 打印完整的错误堆栈
    finally:
        client.disconnect()

if __name__ == "__main__":
    main()
```
