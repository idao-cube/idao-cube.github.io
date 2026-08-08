JWT 解析工具可以将任意 JSON Web Token 解码为可读内容：

- **自动解析**：输入 JWT 后自动解码 Header、Payload 和 Signature 三个部分
- **标准字段识别**：自动识别 `alg`、`typ`、`sub`、`iss`、`iat`、`exp` 等标准 JWT claims，显示人类可读名称
- **时间戳转换**：`iat`、`exp`、`nbf` 等时间戳字段自动转为可读日期时间
- **实时解析**：输入即解析，无需点击按钮

适用于调试 JWT 认证、查看 Token 内容、分析 API 返回的访问令牌等场景。