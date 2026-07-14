---
source: qq_bot.txt
tags:
  - 开发
  - 机器人
  - QQ
  - 凭证
---

# QQ 机器人

> [!warning] 敏感信息
> 含机器人、邮箱、各平台 API 密钥。

## QQ 机器人凭证
- **AppID（机器人ID）**：`102835909`
- **AppSecret（机器人密钥）**：`VkzFVm3LexHbwHdzMj7WvLlCd5X0TxRw`

## 运行命令
```bash
pkill -9 -f openclaw
nohup openclaw gateway > /opt/openclaw-gateway.log 2>&1 &
```

## 邮箱
- **QQ 邮箱**：`wxnyanbaxbpabcdd`
- **企业邮箱**：`mvBgfGJGFP2JkMZa`

## NVIDIA API
- **API Key**：`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`

模型配置：
```json
{
  "nvidia": {
    "baseUrl": "https://integrate.api.nvidia.com/v1",
    "apiKey": "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",
    "api": "openai-completions",
    "models": [
      {
        "id": "z-ai/glm4.7",
        "name": "z-ai",
        "reasoning": false,
        "input": ["text"],
        "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
        "contextWindow": 128000,
        "maxTokens": 8192
      },
      {
        "id": "minimaxai/minimax-m2.1",
        "name": "minimaxai",
        "reasoning": false,
        "input": ["text"],
        "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
        "contextWindow": 128000,
        "maxTokens": 8192
      }
    ]
  }
}
```

## 微信公众号
- **AppID**：`wx212de244dfbea604`
- **AppSecret**：`ae4f0db3a02b18522f6d5c0c828113c7`

## 数据库与其他密钥
- **PostgreSQL**：`666a545c5ee46ff10621efc2831a8ae9`
- **KEY**：`99cf19c29ed4de07af4842719d79fd65400f9d420f60b72498072204615fde10`
- **API KEY (JWT)**：`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwOWFmMTdlNS05ZmY2LTQ0MDYtYTNjOC03OTFiNjc2Y2IwNzgiLCJpc3MiOiJuOG4iLCJhdWQiOiJwdWJsaWMtYXBpIiwianRpIjoiMjRmMjIxZGItNjMxNS00NzkxLThmMTYtNjc0MjZmYjNkMjMyIiwiaWF0IjoxNzc1NjEwNDE3fQ.4VyiFWtUIQ3Zy4W3kITrEWpHyNSbmuOYVrEYi0rlMb0`
