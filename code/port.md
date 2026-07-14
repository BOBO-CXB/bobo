---
source: port.py
tags:
  - 代码
  - Python
---

# port

```python
#!/usr/bin/env python3
"""
服务器端口扫描脚本 - 扫描9000以上端口
"""

import socket
import sys
import threading
import time
from concurrent.futures import ThreadPoolExecutor, as_completed

def check_port(host, port, timeout=1):
    """检查单个端口是否开放"""
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as sock:
            sock.settimeout(timeout)
            result = sock.connect_ex((host, port))
            if result == 0:
                try:
                    service = socket.getservbyport(port)
                except:
                    service = "unknown"
                return port, True, service
            return port, False, None
    except Exception as e:
        return port, False, None

def scan_ports(host, start_port=9000, end_port=9100, max_threads=100):
    """扫描指定范围的端口"""
    print(f"开始扫描 {host}:{start_port}-{end_port} 端口范围...")
    print("-" * 50)
    
    open_ports = []
    start_time = time.time()
    
    with ThreadPoolExecutor(max_workers=max_threads) as executor:
        futures = {
            executor.submit(check_port, host, port): port 
            for port in range(start_port, end_port + 1)
        }
        
        for future in as_completed(futures):
            port, is_open, service = future.result()
            if is_open:
                open_ports.append((port, service))
                print(f"[+] 端口 {port}/tcp 开放 - 服务: {service}")
    
    end_time = time.time()
    
    print("-" * 50)
    print(f"扫描完成! 耗时: {end_time - start_time:.2f}秒")
    print(f"发现 {len(open_ports)} 个开放端口:")
    
    for port, service in sorted(open_ports):
        print(f"  {port}/tcp - {service}")
    
    return open_ports

def main():
    
    host = '10.193.98.3'
    start_port = 12000
    end_port = 21000  # 默认扫描9000-9100
    
    if len(sys.argv) >= 4:
        try:
            start_port = int(sys.argv[2])
            end_port = int(sys.argv[3])
        except ValueError:
            print("错误: 端口号必须是数字")
            sys.exit(1)
    
    if start_port < 1 or end_port > 65535:
        print("错误: 端口范围必须在1-65535之间")
        sys.exit(1)
    
    if start_port > end_port:
        start_port, end_port = end_port, start_port
    
    scan_ports(host, start_port, end_port)

if __name__ == "__main__":
    main()
```
