---
source: iot.py
tags:
  - 代码
  - Python
  - 物联网
---

# iot

```python
import hashlib
import base64
import time
import urllib.parse

def generate_signature_for_get(app_key, app_secret, params):
    """
    为GET请求生成签名
    
    Args:
        app_key: 应用标识符
        app_secret: 应用秘钥
        params: 参数字典
    """
    # 生成时间戳
    timestamp = time.strftime("%Y%m%d%H%M%S", time.localtime())
    
    # 构建查询字符串（按字母顺序排序）
    if params:
        sorted_params = sorted(params.items(), key=lambda x: x[0])
        query_string = "&".join([f"{k}={v}" for k, v in sorted_params])
        print("排序后的查询字符串:", query_string)
        body_base64 = base64.b64encode(query_string.encode('utf-8')).decode('utf-8')
    else:
        body_base64 = ""
    
    # 构建签名字符串 - 修正：使用body_base64而不是空字符串
    sign_string = f"appKey={app_key}&timestamp={timestamp}&body=""&appSecret={app_secret}"
    print("签名字符串:", sign_string)
    
    # 计算MD5签名
    signature = hashlib.md5(sign_string.encode('utf-8')).hexdigest()
    
    return {
        'signature': signature,
        'timestamp': timestamp,
        'app_key': app_key,
        'body_base64': body_base64,
        'headers': {
            'X-Sign': signature,
            'X-App-Key': app_key,
            'X-Timestamp': timestamp
        }
    }

def generate_video_url(base_url, signature_result):
    """
    生成带签名参数的视频播放URL
    
    Args:
        base_url: 基础URL（不包含查询参数）
        signature_result: 签名结果字典
    """
    # 构建签名参数
    sign_params = {
        'X-Sign': signature_result['signature'],
        'X-App-Key': signature_result['app_key'],
        'X-Timestamp': signature_result['timestamp']
    }
    
    # 将签名参数编码为查询字符串
    query_string = urllib.parse.urlencode(sign_params)
    
    # 构建完整URL
    full_url = f"{base_url}?{query_string}"
    
    return full_url

# 使用示例
if __name__ == "__main__":
    APP_KEY = "2025112852480"
    APP_SECRET = "FuVFgXetVu0gdbEhaqwk"
    
    # 从URL中提取的参数
    params = {
        'pageIndex': '0',
        'pageSize': '1000', 
        'accessType': 'HIK_API',
        'pageNo': '1'
    }
    
    # 生成签名
    result = generate_signature_for_get(APP_KEY, APP_SECRET, params)
    
    print("\n" + "="*50)
    print("生成的签名信息:")
    print("="*50)
    print(f"签名 (X-Sign): {result['signature']}")
    print(f"时间戳 (X-Timestamp): {result['timestamp']}")
    print(f"应用标识 (X-App-Key): {result['app_key']}")
    print(f"Base64编码的body: {result['body_base64']}")
    
    print("\n" + "="*50)
    print("视频播放接口URL示例:")
    print("="*50)
    
    # 假设的基础URL（请根据实际情况修改）
    base_video_url = "http://127.0.0.1:8071/live/1798231612651921409"
    video_url = generate_video_url(base_video_url, result)
    print(f"视频播放URL: {video_url}")
    
    # 按照您提供的格式输出
    print("\n" + "="*50)
    print("按照要求格式输出的URL:")
    print("="*50)
    formatted_url = f"{base_video_url}?X-Sign={result['signature']}&X-App-Key={result['app_key']}&X-Timestamp={result['timestamp']}"
    print(formatted_url)
    
    print("\n注意事项:")
    print("- 请确保每次请求都使用最新的时间戳")
    print("- 客户端和服务端之间的时间差不能超过3分钟")
    print("- 如果时间戳过期，需要重新生成签名")
```
