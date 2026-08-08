## 模型与定位

Amazon Nova 是 AWS 原生模型家族，通过 Bedrock 与 SageMaker AI 提供。第一代包含 Micro / Lite / Pro / Premier 文本模型及 Canvas（图像）、Reel（视频）生成模型；2026 年推出第二代：Nova 2 Lite（1M 上下文多模态）、Nova 2 Pro（Preview）、Nova 2 Sonic（语音到语音）。Nova Forge 允许用户基于 Nova 2 Lite 构建自定义前沿模型。与 AWS 生态（IAM、VPC、CloudWatch、SageMaker）深度集成。

## 参数速览

| 项目 | 说明 |
| --- | --- |
| 输入模态 | 文本、图像、视频、音频（分模型） |
| 输出能力 | 文本、图像（Canvas）、视频（Reel）、语音（Sonic） |
| 推理模式 | 标准解码（二代起含推理模型档） |
| 典型模型名 | `amazon.nova-pro-v1:0`、`amazon.nova-2-lite`、`amazon.nova-canvas-v1:0` |
| 上下文窗口 | Nova 2 Lite 最高 1M；Pro 300K |

## 常用请求参数

| 参数 | 说明 |
| --- | --- |
| `modelId` | 模型 ID，如 `amazon.nova-pro-v1:0` |
| `maxTokens` | 最大输出长度 |
| `inferenceConfig` | temperature/top_p 等采样配置 |
| 推理提示 | Converse API 支持原生推理参数 |

## 调用与兼容性

使用 `bedrock-runtime` 的 InvokeModel/Converse API，或 `bedrock-mantle`（Anthropic 兼容端点）；Python boto3 示例：

```python
import boto3

client = boto3.client("bedrock-runtime", region_name="us-east-1")
resp = client.converse(
    modelId="amazon.nova-pro-v1:0",
    messages=[{"role": "user", "content": [{"text": "你好"}]}],
)
print(resp["output"]["message"]["content"][0]["text"])
```

## 版本与更新注意

Nova Pro 于 2024-12-05 发布（300K 上下文）；Nova 2 系列 2026 年逐步上线。定价按 token 计费：Micro $0.035/$0.14、Lite $0.06/$0.24、Pro $0.80/$3.20、Premier $2.50/$12.50（每百万 token 输入/输出），Nova 2 Lite $0.30/$2.50（Flex 档半价）。

## 选型建议

已在 AWS 上运行的业务优先考虑，免跨云传输成本；需要图像/视频生成选 Canvas/Reel；轻量高频任务用 Micro/Lite，旗舰任务用 Pro/Premier。