
# 模型API文档

本文档详细说明了如何通过API与模型进行交互，提交问题并获取生成的回答。

## API概述

- **请求方法**：`POST`
- **API端点 URL**：[http://101.42.10.67/v1](http://101.42.10.67/v1)
- **认证方式**：需要API密钥进行身份验证。

## 请求说明

### 请求头

- **Content-Type**: `application/json`  
  说明：请求体的数据格式是JSON。

### 请求体

请求体应为JSON格式，包含以下字段：

- **`model`**（字符串，必填）：模型的名称（例如：`glm-4-9b-chat`）。
- **`messages`**（列表，必填）：包含用户的输入消息，包含角色和内容。
  - `role`（字符串，必填）：消息角色，值为 `user`。
  - `content`（字符串，必填）：用户提问的内容。
- **`temperature`**（浮动，选填，默认值：0.0）：生成回答时的随机性。`0.0`表示生成较为保守的回答，`1.0`表示回答的随机性较高。
- **`seed`**（整数，选填，默认值：42）：用于生成一致性答案的随机种子。

### 示例请求体

```json
{
    "model": "glm-4-9b-chat",  # 请替换为实际模型名称
    "messages": [
        {
            "role": "user",
            "content": "什么是机器学习？"
        }
    ],
    "temperature": 0.0,
    "seed": 42
}
```

### 请求示例代码（Python）

以下是一个示例代码，展示了如何使用Python与API进行交互：

```python
import requests

def generate(question: str) -> str:
    """Generate response from question.
    Returns:
        str: response
    """
    # 构建消息体
    message = [{"role": "user", "content": question}]
    
    # 定义API请求的URL和头部
    url = "http://101.42.10.67/v1"  # API端点 URL
    headers = {
        "Content-Type": "application/json",  # 发送JSON数据
    }
    
    # 构建请求数据
    data = {
        "model": "MODEL_NAME",  # 替换为模型名称，例如 "glm-4-9b-chat"
        "messages": message,
        "temperature": 0.0,  # 控制输出的随机性
        "seed": 42  # 随机种子
    }
    
    # 发送POST请求
    response = requests.post(url, json=data, headers=headers, auth=("Bearer", "API_KEY"))  # 需要替换为实际的API密钥
    
    # 处理响应并返回结果
    if response.status_code == 200:
        # 如果请求成功，提取生成的回答
        return response.json()["choices"][0]["message"]["content"]
    else:
        # 如果请求失败，返回错误信息
        return f"Error: {response.status_code}, {response.text}"
```

### 说明：
- **`API_KEY`**：替换为您提供的实际API密钥。
- **`MODEL_NAME`**：替换为您要使用的模型名称（例如 `"glm-4-9b-chat"`）。
- **`question`**：输入用户的提问。

## 返回结果

API响应将以JSON格式返回，包含生成的回答。响应内容示例如下：

```json
{
    "choices": [
        {
            "message": "机器学习是人工智能的一个分支，旨在通过数据训练模型，使其能够在没有明确编程的情况下做出预测或决策。"
        }
    ]
}
```

## 错误处理

如果请求失败，API将返回错误信息。例如：

- **401 Unauthorized**：API密钥无效或缺失。
- **400 Bad Request**：请求格式不正确，检查参数是否缺失或格式错误。
- **500 Internal Server Error**：服务器内部错误，稍后重试。

## 获取API密钥

请在[平台](http://101.42.10.67)注册并获取您的API密钥。

## 常见问题

### 如何调节回答的随机性？

可以通过调整`temperature`参数来调节生成回答的随机性。值越高，生成的回答越具多样性；值越低，回答会更加保守和确定。

### 如何保证API请求的安全性？

请确保您的API密钥不会泄露，避免将密钥嵌入到公开的代码或文档中。使用环境变量或加密工具来存储和管理密钥。
