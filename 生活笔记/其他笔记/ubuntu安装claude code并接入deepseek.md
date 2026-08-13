---
tags:
  - claude
date: 2026-05-24 17:33:55
---

![[img/post-claude-code-cover.png|720]]

## 第一步：准备环境（安装 Node.js）

Claude Code 依赖 Node.js 环境，要求版本 **v18+**。推荐使用 `nvm`（Node Version Manager）来管理 Node.js 版本，避免全局权限冲突。

**1. 下载并安装 nvm：**

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

**2. 使环境变量生效：**

```bash
source ~/.bashrc
```

> 如果你使用的是 zsh，请改为 `source ~/.zshrc`。

**3. 安装并切换到 Node.js 20.20.1 版本：**

```bash
nvm install 20.20.1
nvm use 20.20.1
```

**4. 验证安装是否成功（应正常返回版本号）：**

```bash
node -v
npm -v
```

---

## 第二步：安装 Claude Code

环境准备就绪后，通过 npm 全局安装 Claude Code 终端工具。

**1. 执行全局安装命令：**

```bash
npm install -g @anthropic-ai/claude-code
```

**2. 验证安装（确保输出正常的版本号）：**

```bash
claude --version
```

---

## 第三步：安装与配置 CC-Switch

无需手动修改繁琐的系统环境变量，通过 CC-Switch 客户端可以实现图形化一键配置与切换。

**1. 安装 CC-Switch：**

去github上直接搜索CC-Switch，搜出来第一个就是，下载它的安装包。

在终端中进入你下载好的安装包目录，执行以下命令，deb包名改成你真实的包名：

```bash
sudo dpkg -i CC-Switch-v3.15.0-Linux-x86_64.deb
```

> 如果提示缺少依赖，可以运行 `sudo apt --fix-broken install` 修复。

**2. 打开 CC-Switch：**

安装完成后，在 Ubuntu 应用程序菜单中找到并打开 **CC-Switch**。

**3. 添加 API 配置：**

在软件界面中，**直接点击"加号（＋）"按钮**来添加 API 配置，填入以下核心信息：

| 配置项   | 建议值                                    |
| -------- | ----------------------------------------- |
| Base URL | `https://api.deepseek.com/anthropic`      |
| API Key  | 你的 DeepSeek API Key（必填，请替换为真实值，没有则需要在deepseek官网注册一个） |
| Model    | `deepseek-v4-pro[1m]`                     |

保存后即可一键开启代理转发。

---

## 第四步：启动与日常使用

配置完成后，建议在具体的代码项目目录中启动 Claude Code，以便 AI 能够完美理解你的代码上下文。

**1. 进入你的代码项目文件夹：**

```bash
cd /path/to/your/project
```

**2. 启动 Claude Code：**

```bash
claude
```

**3. 开始使用：**

现在你可以在 `>` 提示符后直接使用自然语言向 DeepSeek 提问，或下达代码编写、重构、调试等指令。

**常用操作速查：**

| 操作         | 命令/方式            |
| ------------ | -------------------- |
| 退出会话     | `/exit`              |
| 查看帮助     | `/help`              |
| 清除对话历史  | `/clear`             |
| 查看当前状态  | `/status`            |

---

## 常见问题

**Q: 提示 `claude: command not found`？**

A: 检查 npm 全局安装路径是否在 `PATH` 中：

```bash
npm list -g --depth=0
echo $PATH
```

如果路径缺失，将以下内容添加到 `~/.bashrc`：

```bash
export PATH="$HOME/.npm-global/bin:$PATH"
```

**Q: CC-Switch 代理转发不生效？**

A: 检查 CC-Switch 是否正在运行（系统托盘应有图标），确认 Base URL 和 API Key 填写无误，并确保网络能正常访问 DeepSeek API。
