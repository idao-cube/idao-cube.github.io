## 它能做什么？

这个功能可以把 **Claude Desktop 变成你自己的第三方 API 桌面客户端**。配置完成后，模型调用走你填的第三方 API，不再消耗 Claude 官方订阅额度（但会消耗第三方 API 的额度或余额）。

适合想用 **Cowork、Projects、Artifacts** 等 Claude Desktop 专属能力，同时又希望接入自己 API 服务的用户。

> ⚠️ 注意：第三方模式不是"完整网页版 Claude"的替代品。官方普通 Chat 标签在此模式下不可用，主要使用的是 **Cowork / Code / Projects / Artifacts** 这些能力。

---

## 重要前提（必读）

配置前必须确认以下几点：

- **使用最新版 Claude Desktop**：低版本可能没有开发者模式或第三方推理配置入口。
- **先不要登录 Claude 官方账号**：建议保持未登录状态。如果已经登录，先退出再配置。
- **使用支持 Anthropic-compatible 的 API**：单纯 OpenAI-compatible 的接口不一定能用。
- **Base URL 必须是 HTTPS**：对应服务需要支持 Anthropic Messages API（能处理 `/v1/messages` 请求）。
- **注意隐私风险**：你的提示词、文件内容、项目上下文会经过第三方 API 服务。不要把敏感资料交给不可信的中转站。

> 本教程以 Windows 为例，截图也是 Windows 环境下的界面。

---

## 步骤 1：启用开发者模式

打开 Claude Desktop，**先不要登录官方账号**。

在顶部菜单栏选择 **Help（帮助） → Troubleshooting（疑难解答）**。

在弹出的子菜单里点击 **Enable Developer Mode（启用开发者模式）**。

> 如果当前界面不好直接点菜单，可以按键盘 `Tab` 切到左上角菜单区域，再按回车打开菜单。

启用成功后，顶部菜单栏会多出一个 **Developer（开发者）** 菜单。

---

## 步骤 2：进入第三方 API 配置页面

1. 点击新出现的 **Developer** 菜单。
2. 选择 **Configure Third-Party Inference…（配置第三方推理…）**。

---

## 步骤 3：填写 Base URL 和 API Key

打开配置窗口后，按下面方式逐项设置：

| 配置项 | 操作 |
|---|---|
| **Use this configuration** | 打开开关（必须开启） |
| **Gateway** | 选择 `Anthropic-compatible` |
| **Gateway base URL** | 粘贴你的第三方 API Base URL（`https://` 开头） |
| **Gateway API key** | 粘贴你的 API Key（中转站后台复制的那串密钥） |
| **Gateway auth scheme** | 保持默认即可 |
| **Gateway extra headers** | 一般留空，除非服务商明确要求额外请求头 |

设置完后，点击右下角 **Apply locally（本地应用）**。

---

## 步骤 4：验证是否成功

1. 配置完成后，Claude Desktop 可能会提示重启。如果没有提示，也建议**手动完全退出**后重新打开。
2. 重新打开后，进入 **Cowork、Code 或 Projects** 相关页面。
3. 输入一个简单问题测试。

如果模型能正常响应，或者界面中显示的是你第三方 API 提供的模型，就说明**配置成功**。

### 成功后的效果

- 模型调用走你的第三方 API，不消耗 Claude 官方订阅额度
- 可以使用 Cowork / Projects / Artifacts 等第三方模式支持的功能
- 响应速度取决于你的第三方 API 服务质量和网络延迟

---

## 常见问题

### Q1：找不到 Developer 菜单？

按顺序检查：

1. 确认已点击 **Help → Troubleshooting → Enable Developer Mode**
2. 完全退出 Claude Desktop 后重新打开
3. 确保 Claude Desktop 已更新到最新版本

### Q2：配置后还是进了普通 Claude 登录页？

可能是配置没有生效，尝试：

1. 确认 **Use this configuration** 开关已打开
2. 确认已填写 Gateway、Base URL 和 API Key
3. 点击 **Apply locally** 后完全退出并重新打开
4. 如仍不生效，在 **Help → Troubleshooting** 里查看配置报告或错误提示

### Q3：报错或无法连接？

重点检查这几项：

- API Key 是否复制完整（前后不要多空格）
- Base URL 是否以 `https://` 开头
- 你的第三方 API 是否真的支持 **Anthropic-compatible**，而非只支持 OpenAI-compatible
- 服务商是否要求额外请求头——如有要求，需填入 **Gateway extra headers**
- 第三方 API 余额、额度或模型权限是否正常

### Q4：填了 OpenAI 格式接口为什么不能用？

Claude Desktop 需要的是 **Anthropic-compatible** 接口。如果服务只提供 OpenAI-compatible 接口，需要先通过支持协议转换的网关，转成 Anthropic Messages API 后再接入。

### Q5：想切换回官方 Claude？

直接关闭 **Use this configuration** 开关，然后完全退出并重新打开 Claude Desktop。如果出现官方登录入口，选择官方账号登录即可。

### Q6：免费用户能用 Cowork 吗？

第三方模式本身不依赖 Claude Pro 额度——模型调用走你自己的第三方 API。但具体哪些功能可用，会受 Claude Desktop 当前版本和官方策略影响。

---

## 小贴士

- 配置是本地保存的，**只影响当前这台 Windows 电脑**上的 Claude Desktop。
- **不影响网页版 Claude**，也不会改变你的 Claude 官方账号设置。
- **第三方 API 质量很关键**：建议优先选择稳定、透明、可信的服务商。
- 官方客户端和相关功能可能随时更新，如果界面变化，以最新版本里的菜单和提示为准。
- 配置完成后，直接去试试 **Cowork 或 Projects**，桌面版体验确实很顺手。

---

## 总结

这个功能本质上就是 Claude Desktop 开放了一个 **"自带 API"** 的入口。对于想用 Claude 桌面端工具链、但不想（或不需要）订阅 Claude Pro 的用户来说，是一个很实用的选择。

关键就三步：开开发者模式 → 填 Base URL 和 Key → Apply locally，然后就可以用了。

> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)