# 📖 前言

## 什么是 Claude Code？

Claude Code 是 Anthropic 推出的命令行 AI 编程助手，支持代码理解、多文件编辑、Git 操作、多 Agent 协作等强大功能。

## 什么是 CC Switch？

**CC Switch** 是一款基于 Tauri 2 的桌面 GUI 工具，专门用来管理 AI CLI 工具（Claude Code、Codex、Gemini CLI 等）的后端模型配置。它内置 50+ Provider 预设，**图形化一键切换模型提供商**，彻底告别手写 JSON 配置文件。

## 为什么用 DeepSeek + CC Switch？

- 🖥️ **图形化管理**：CC Switch 提供可视化界面，不需要手动编辑环境变量或 JSON
- 🔄 **一键切换**：随时在 DeepSeek / Anthropic / 其他 Provider 间自由切换
- 🚀 **DeepSeek V4 Pro**：代码能力对标顶尖闭源模型
- 💰 **成本更低**：DeepSeek API 性价比极高
- 🌐 **国内友好**：无需翻墙

---

# 🛠 准备工作

## 安装 Node.js

Claude Code 需要 Node.js 18.x 或更高版本。

1. 前往 [nodejs.org](https://nodejs.org/) 下载 `.msi` 安装包
2. 双击运行，一路 "Next" 完成安装

验证安装，打开 PowerShell：

```powershell
node -v    # 应输出 v18.x / v20.x / v22.x
npm -v     # 应输出 10.x 或更高
```

## 获取 DeepSeek API Key

1. 访问 [platform.deepseek.com](https://platform.deepseek.com)，注册/登录
2. 进入 **"API Keys"** 页面，点击 **"创建 API Key"**
3. 复制生成的 `sk-` 开头的密钥，妥善保存

![image.png](/image/47ba2637-cf71-4568-8c65-66d603016d6b.png)

> ⚠️ **重要：** API Key 只显示一次，请立即复制保存！丢失需要重新创建。

---

# 📥 安装 Claude Code

打开 PowerShell，使用 npm 全局安装：

```powershell
npm install -g @anthropic-ai/claude-code
```

> 💡 **国内用户建议使用镜像加速：**
> ```powershell
> npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com
> ```

验证安装：

```powershell
claude --version
```

![image.png](/image/daaad63f-b780-40e8-b960-d236d5fd6b47.png)

---

# 🖥️ 安装并配置 CC Switch

## 下载 CC Switch

1. 打开 CC Switch 的 GitHub Releases 页面：  
   👉 https://github.com/farion1231/cc-switch/releases

2. 找到最新版本，下载 **`CC-Switch-v3.16.x-Windows.msi`**（安装版）或 **`CC-Switch-v3.16.x-Windows-Portable.zip`**（便携版）
   ![image.png](/image/117da781-9ff9-4770-aa76-cc3ae4282e7c.png)

3. 双击 `.msi` 文件安装，建议装到非系统盘（如 `D:\`）

## 用 CC Switch 接入 DeepSeek

安装完成后打开 CC Switch，按以下步骤操作：

**第 1 步：选择 Claude Code**

在 CC Switch 主界面工具列表中，对 **"Claude Code CLI"** 添加DeepSeek供应商。
![image.png](/image/3c9d24f9-1d60-40b8-ac45-5db15994b3aa.png)
![image.png](/image/e981a6f6-9830-4dc7-be8e-d357e924b186.png)
<!-- 📸 截图：CC Switch 主界面，红框标注 Claude Code CLI -->
> **[截图 9]** CC Switch 主界面 → 选择 Claude Code CLI

**第 2 步：填写配置**

在 DeepSeek 配置表单中填入：

| 配置项 | 填写内容 |
|---|---|
| **API Key** | `sk-你的DeepSeek密钥` |
| **Opus / Sonnet 模型** | `deepseek-v4-pro` |
| **Haiku / SubAgent 模型** | `deepseek-v4-flash` |
| **启用 1M 上下文** | ✅ 开启（勾选 `[1m]` 选项） |

![image.png](/image/f9a2dbf1-0b72-4b59-b046-dab35d1e073a.png)

**第 4 步：添加并启用**

- 点击 **"添加"** 按钮保存配置
- 然后点击 **"启用"**，将 DeepSeek 设为当前活跃 Provider

![image.png](/image/81a3db22-f1cc-47b8-9f48-fd9b579fe374.png)

> ✅ 至此配置完成！CC Switch 会自动管理环境变量，无需手动编辑任何配置文件。

---

# ✅ 验证安装

配置完成后，启动 Claude Code 验证：

```powershell
claude
```

进入交互界面后，输入 `/model` 查看当前使用的模型：

![image.png](/image/a5d3d00d-76ab-48f4-9315-a3e1359d48cd.png)

---

# 📋 配置速查表

| 配置项 | 正确值 | 说明 |
|---|---|---|
| **Base URL** | `https://api.deepseek.com/anthropic` | ⚠️ 是 `/anthropic` 路径，**不是** `/v1` |
| **API Key** | `sk-xxx...` | 从 platform.deepseek.com 获取 |
| **主模型** | `deepseek-v4-pro[1m]` | 用于 Opus / Sonnet 槽位 |
| **快速模型** | `deepseek-v4-flash` | 用于 Haiku / SubAgent 槽位 |
| **1M 上下文** | 勾选 `[1m]` | CC Switch 中有独立开关 |

> ⚠️ DeepSeek 旧模型名 `deepseek-chat` / `deepseek-reasoner` 将于 **2026-07-24 废弃**，务必使用新名称。

---

# 🔥 常用命令速查

```powershell
# 启动交互模式
claude

# 直接提问（非交互）
claude -p "解释这个项目是做什么的"

# 指定工作目录
claude --dir C:\path\to\project

# 查看帮助
claude --help
```

交互模式快捷键：

| 操作 | 快捷键 / 命令 |
|---|---|
| 粘贴代码 | `Ctrl+V` |
| 退出 | `exit` 或 `Ctrl+C` |
| 清屏 | `/clear` |
| 查看历史会话 | `/resume` |
| 查看当前模型 | `/model` |
| 权限管理 | `/permissions` |

---

# 📚 参考资源

- [CC Switch GitHub Releases](https://github.com/farion1231/cc-switch/releases) — 下载最新版
- [DeepSeek 官方文档 — Claude Code 集成](https://api-docs.deepseek.com/zh-cn/quick_start/agent_integrations/claude_code)
- [DeepSeek 开发者平台](https://platform.deepseek.com) — 获取 API Key
- [Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code)

