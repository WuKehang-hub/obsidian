---
tags:
  - SSH
  - Linux
  - Git
date: 2026-09-03
---

SSH（Secure Shell）是一套通过网络安全地连接和操作另一台计算机的协议与工具。它最常见的用途是登录云服务器，也可以执行远程命令、传输文件。

> 最核心的理解是：**本地电脑负责发出指令，SSH 负责安全传递指令，远程电脑负责真正执行指令。**

## 一、SSH 是什么

### SSH 到底是什么

SSH 首先是一种**网络协议**，它规定了客户端和服务器如何建立加密连接、验证身份以及传输数据。日常使用的 `ssh` 命令是 SSH 客户端。

一次常见的 SSH 连接包含两端：

- **SSH 客户端**：主动发起连接的一端，通常是自己的电脑。
- **SSH 服务端**：接受连接的一端，通常是云服务器。

SSH 连接建立后，登录信息、命令和返回结果都会通过加密通道传输。服务器通常通过密码或 SSH Key 确认客户端是否有权登录。

### 登录后操作的是哪台电脑

执行下面的命令并成功登录后：

```bash
ssh user@server
```

当前终端就相当于变成了远程电脑的终端，当前终端中随后输入的 `cd`、`ls`、`python`、`sudo` 等一切命令，都在**远程服务器**上执行，使用的也是远程服务器的 CPU、内存、磁盘、操作系统和网络。

例如，登录后执行：

```bash
hostname
pwd
```

看到的是远程服务器的主机名和远程文件路径，而不是本地电脑的信息。

终端窗口仍显示在本地电脑上。本地输入会被发送到远端，远端的执行结果再传回本地显示。

### SSH 和远程桌面的区别

| 对比项     | SSH              | 远程桌面            |
| ------- | ---------------- | --------------- |
| 主要界面    | 命令行              | 完整图形桌面          |
| 传输内容    | 命令、文字及必要数据       | 桌面画面、鼠标和键盘操作    |

## 二、SSH 基本使用

### 最基本的登录命令

```bash
ssh 用户名@服务器地址
```

例如：

```bash
ssh ubuntu@203.0.113.10
ssh alice@example.com
```

第一次连接某台服务器时，SSH 可能要求确认服务器指纹。确认服务器可信后输入 `yes` 即可。

### 用户名、IP、域名和端口

以这条命令为例：

```bash
ssh -p 2222 ubuntu@example.com
```

- `ubuntu` 是远程服务器上的用户名。
- `example.com` 是域名，用来定位服务器，也可以直接换成服务器的 IP 地址。
- IP 地址是服务器在网络中的地址，例如 `203.0.113.10`。
- `2222` 是 SSH 服务监听的端口。

用户名不是本地电脑用户名，域名也不是用户名的一部分。`@` 左边是远程用户名，右边是服务器地址。

### SSH 端口和 `-p`

SSH 默认使用 TCP `22` 端口。因此，下面两条命令通常等价：

```bash
ssh ubuntu@example.com
ssh -p 22 ubuntu@example.com
```

如果服务器管理员把 SSH 服务配置在其他端口，就必须通过小写的 `-p` 指定端口：

```bash
ssh -p 2222 ubuntu@example.com
```

### 登录后退出

正常退出 SSH 会话：

```bash
exit
```

也可以按 `Ctrl+D`。退出后，终端回到本地 Shell。

### 使用配置文件简化登录

可以在本地 `~/.ssh/config` 中保存连接参数：

```sshconfig
Host myserver
    HostName example.com
    User ubuntu
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```

以后只需执行：

```bash
ssh myserver
```

该别名也能用于 `scp`、`rsync` 和 VS Code Remote SSH。

## 三、SSH 能做什么

### 执行远程命令

登录后可以交互式执行命令

### 传输文件

纯粹执行 `ssh user@server` **不会自动传输本地项目**。需要另外使用 `scp`、`rsync` 或 Git 等工具。

#### 使用 scp

把本地文件传到远端：

```bash
scp local.txt ubuntu@example.com:~/app/
```

把远端文件下载到当前本地目录：

```bash
scp ubuntu@example.com:~/app/result.txt .
```

#### 使用 rsync

同步项目目录到远端：

```bash
rsync -avz --progress ./project/ ubuntu@example.com:~/project/
```

源路径末尾的 `/` 表示同步 `project` 目录中的内容，而不是再创建一层 `project`。`rsync` 会比较两端文件，通常只传输有变化的部分，适合反复同步项目。

排除不需要上传的目录：

```bash
rsync -avz \
  --exclude '.git/' \
  --exclude '.venv/' \
  --exclude '__pycache__/' \
  ./project/ ubuntu@example.com:~/project/
```

### ssh、scp 和 rsync 的分工

| 工具      | 主要用途         | 特点           |
| ------- | ------------ | ------------ |
| `ssh`   | 登录服务器、执行远程命令 | 本身不会自动同步项目   |
| `scp`   | 复制文件或目录      | 用法简单，适合一次性传输 |
| `rsync` | 增量同步目录       | 适合反复同步       |

## 四、SSH 与 VS Code

### VS Code Remote SSH 是什么

VS Code Remote - SSH 扩展让本地 VS Code 通过 SSH 连接远程主机，并像编辑本地项目一样浏览、编辑和运行远程项目。

连接后，VS Code 分成两部分：

- **本地部分**：窗口、菜单、键盘输入和界面渲染。
- **远程部分**：项目文件、终端、运行环境和部分扩展。

VS Code 会自动在远程主机上安装配套的 VS Code Server，用来处理远程文件、终端和扩展。

### Remote SSH 到底改变了什么

普通本地 VS Code 打开的工作区位于本地文件系统，终端和程序默认也在本地运行。连接 Remote SSH 后，当前窗口的**工作区上下文切换到了远端**：

- 文件资源管理器浏览远程目录。
- 保存操作写入远程磁盘。
- 集成终端在远程主机上启动。
- 运行、调试和 Git 命令在远程主机上执行。

界面仍然由本地 VS Code 显示，它不是把云服务器的完整桌面传回来，也不会自动把整个项目下载到本地。

### 本地 VS Code 与云服务器上的 VS Code

通常不需要在没有图形桌面的云服务器上安装并打开完整 VS Code。Remote SSH 的典型结构是：

```text
本地电脑：VS Code 图形界面
       │
       │ SSH 加密连接
       ▼
云服务器：VS Code Server + 项目文件 + 编译/运行环境
```

### 代码存在哪里、在哪里运行

- Remote SSH 窗口打开 `/home/ubuntu/project`：代码保存在云服务器磁盘上，运行和调试默认使用云服务器资源。
- 普通本地窗口打开 `C:\Users\...` 或 `/home/localuser/...`：代码保存在本地，默认在本地运行。

Remote SSH 不会自动把原本的本地项目变成远程项目。需要先用 Git、`rsync`、`scp` 等方式把代码放到远端，或者在 Remote SSH 窗口中直接克隆仓库、创建和编辑文件。

本地电脑与云服务器始终是两台独立计算机，各自拥有文件系统、软件环境和算力。断开连接后，已经保存的远程代码仍留在服务器上。

## 五、本地开发与云服务器运行

完全可以只在本地编写代码，让云服务器负责运行。常用同步方式是 Git 和 `rsync`。

### Git 方案：本地 → GitHub → 云服务器

基本流程如下：

```text
本地修改并提交 → push 到 GitHub → 云服务器 pull → 云服务器运行
```

本地执行：

```bash
git add .
git commit -m "update feature"
git push origin main
```

云服务器执行：

```bash
cd ~/project
git pull --ff-only origin main
python main.py
```

这个方案有清晰的版本历史，适合正式开发和协作，但每次同步通常都要先提交并推送。

### rsync 方案：本地 → 云服务器

本地直接执行：

```bash
rsync -avz \
  --exclude '.git/' \
  --exclude '.venv/' \
  --exclude '__pycache__/' \
  ./project/ myserver:~/project/
```

这个方案不需要经过 GitHub，保存后可以立即同步，适合个人快速迭代，但 `rsync` 本身不保存版本历史。

### 一条命令同步并运行

可以把 `rsync` 与 `ssh` 用 `&&` 串联：

```bash
rsync -avz --exclude '.git/' ./project/ myserver:~/project/ \
  && ssh myserver 'cd ~/project && python main.py'
```

只有同步成功后，远程命令才会执行。经常使用时，可以把这条命令保存成 Shell 脚本。
