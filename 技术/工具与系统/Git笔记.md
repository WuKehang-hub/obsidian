---
tags:
  - Git
date: 2026-02-25
---

![[附件/post-git-notes-cover.png|720]]

Git 是一个**分布式版本控制系统**，用于记录文件变化、协作开发以及在需要时恢复历史版本。

> 可以把 Git 理解为代码的“存档系统”，但 Git 不只是备份工具：它还提供分支、合并、远程协作和历史追踪等能力。

## 一、在 Ubuntu 中安装 Git

更新软件包索引并安装 Git：

```bash
sudo apt update
sudo apt install git -y
```

安装完成后，检查版本：

```bash
git --version
```

只要能够输出类似 `git version 2.x.x` 的内容，就说明安装成功。

> 如果使用的是 Windows，可以从 [Git 官网](https://git-scm.com/downloads) 下载 Git for Windows；本篇主要记录 Ubuntu 下的使用方法。

## 二、首次配置

安装 Git 后，需要设置提交记录中显示的用户名和邮箱：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

查看配置是否生效：

```bash
git config --global --list
```

这里的邮箱建议与 GitHub 账号使用的邮箱保持一致。

## 三、创建或下载仓库

### 创建本地仓库

进入项目文件夹后执行：

```bash
cd 项目目录
git init
```

该命令会把当前文件夹变成 Git 仓库。

### 下载远程仓库

```bash
git clone <仓库地址>
```

例如：

```bash
git clone https://github.com/user/project.git
```

`clone` 会下载项目文件以及已有的提交记录。

## 四、日常提交：最常用的四步

### 1. 查看文件状态

```bash
git status
```

用于查看哪些文件被修改、哪些文件尚未跟踪，以及哪些修改已经进入暂存区。

### 2. 添加到暂存区

添加指定文件：

```bash
git add 文件名
```

添加当前目录中的全部修改：

```bash
git add .
```

### 3. 提交修改

```bash
git commit -m "本次修改说明"
```

例如：

```bash
git commit -m "添加机器人关节阻尼参数"
```

### 4. 推送到 GitHub

```bash
git push
```

因此，日常最常用的完整流程就是：

```bash
git status
git add .
git commit -m "修改说明"
git push
```

> `git add` 只是把修改放入暂存区，`git commit` 才会生成本地提交，`git push` 则把本地提交上传到远程仓库。

## 五、连接 GitHub 远程仓库

如果项目是使用 `git clone` 下载的，通常已经自动配置好远程仓库。

如果项目是通过 `git init` 创建的，需要手动添加 GitHub 仓库地址：

```bash
git remote add origin <仓库地址>
git branch -M main
git push -u origin main
```

首次推送使用 `-u` 建立关联，以后只需要执行 `git push`。

查看当前远程仓库地址：

```bash
git remote -v
```

修改远程仓库地址：

```bash
git remote set-url origin <新地址>
```

`origin` 只是远程仓库地址的默认别名。

## 六、拉取远程更新

在开始修改项目前，可以先获取 GitHub 上的最新内容：

```bash
git pull
```

如果多人同时修改同一个文件，拉取时可能出现冲突，需要手动选择保留的内容，然后重新提交。

> 拉取前最好先通过 `git status` 检查本地是否存在尚未提交的修改。

## 七、日常分支操作

分支适合在不影响主分支的情况下开发新功能。

```bash
git branch             # 查看分支
git switch -c dev      # 创建并切换到 dev 分支
git switch main        # 返回 main 分支
git merge dev          # 将 dev 合并到当前分支
```

旧版本 Git 也可以使用下面的命令创建并切换分支：

```bash
git checkout -b dev
```

对于个人小项目，如果没有同时开发多个功能，可以先只使用 `main` 分支。

## 八、解决 Git Clone 速度慢

浏览器能够访问 GitHub，并不代表终端会自动使用代理。假设代理软件的本地 HTTP 端口为 `7897`，可以临时设置：

```bash
export http_proxy="http://127.0.0.1:7897"
export https_proxy="http://127.0.0.1:7897"
```

关闭当前终端中的代理：

```bash
unset http_proxy
unset https_proxy
```

这些变量只对**当前终端及其子进程**生效，关闭终端后便会失效。

### 让代理设置长期生效

如果不想每次手动输入 `export` 命令，可以在 `~/.bashrc` 中定义代理开关函数。以默认端口 `7897` 为例，在 `~/.bashrc` 末尾加入：

```bash
proxy_on() {
    local proxy_port="${1:-7897}"

    export HTTP_PROXY="http://127.0.0.1:${proxy_port}"
    export HTTPS_PROXY="http://127.0.0.1:${proxy_port}"
    export ALL_PROXY="socks5://127.0.0.1:${proxy_port}"

    export http_proxy="$HTTP_PROXY"
    export https_proxy="$HTTPS_PROXY"
    export all_proxy="$ALL_PROXY"

    export NO_PROXY="localhost,127.0.0.1,192.168.0.0/16,10.0.0.0/8,172.16.0.0/12,::1"
    export no_proxy="$NO_PROXY"
}

proxy_off() {
    unset HTTP_PROXY HTTPS_PROXY ALL_PROXY NO_PROXY
    unset http_proxy https_proxy all_proxy no_proxy
}
```

其中proxy_on和proxy_off均为函数名，可以自定义。

保存文件后，重新加载 Shell 配置：

```bash
source ~/.bashrc
```

之后可以按需开启或关闭当前终端的代理：

```bash
proxy_on        # 使用默认端口 7897
proxy_on 7890   # 使用指定端口
proxy_off       # 取消当前终端的代理
```

> 这种写法是把“代理开关函数”永久保存到 `.bashrc`，并不是每次打开终端都自动启用代理。只有执行 `proxy_on` 的当前终端及其子进程会使用代理。

### 常见代理故障

| 现象 | 可能原因 |
| --- | --- |
| `Connection refused` | 代理软件未启动或端口填写错误 |
| 请求超时 | 节点不可用、分流规则错误或网络质量较差 |
| 浏览器正常但终端失败 | 浏览器使用了独立代理，而终端没有设置代理变量 |
| 局域网服务访问异常 | `NO_PROXY` 中没有包含目标地址或网段 |

## 九、常见问题排查

### 1. 工作区显示干净，但想要的文件没有提交

“工作区干净”只表示 Git 当前没有检测到需要提交的变化，不等于目录中的所有文件都已被提交。

首先检查文件是否被跟踪或忽略：

```bash
git ls-files
git status --ignored
git check-ignore -v <文件路径>
```

常见原因包括：

- 文件被 `.gitignore` 规则忽略。
- 命令是在错误的仓库目录中执行的。
- 子目录本身包含 `.git`，形成了嵌套仓库。
- 文件已经提交，因此没有新的变化。

如果子目录不应该是独立仓库，确认其中的历史确实不需要后，才可删除其 `.git`：

```bash
rm -rf src/my_pkg/.git
```

> 删除 `.git` 会永久移除该子仓库的本地版本历史。执行前务必确认路径并做好备份。

### 2. GitHub 首页没有显示 README

通常应把 `README.md` 放在仓库根目录。GitHub 也可能识别 `.github/` 或 `docs/` 中的 README，但根目录最直观且不易产生歧义。

还应检查：

- 文件是否已经提交并推送。
- 文件名是否为 `README.md`。
- 当前查看的是否为正确分支。

### 3. GitHub README 中的图片不显示

GitHub README 应使用标准 Markdown 图片语法：

```markdown
![路径规划总览](附件/Fig5_3_路径规划总览.png)
```

路径必须相对于 `README.md`，并统一使用正斜杠 `/`。

以下是 Obsidian 的 Wiki 嵌入语法，GitHub 通常无法按图片方式解析：

```markdown
![[附件/Fig5_3_路径规划总览.png]]
```

如果文件名含空格、特殊字符或大小写不一致，也可能造成图片在 GitHub 上无法显示。为仓库图片使用简短的英文文件名通常更稳妥。

## 十、日常命令速查

| 需求 | 命令 |
| --- | --- |
| 安装 Git | `sudo apt install git -y` |
| 下载仓库 | `git clone <仓库地址>` |
| 查看状态 | `git status` |
| 暂存全部修改 | `git add .` |
| 创建提交 | `git commit -m "修改说明"` |
| 拉取远程更新 | `git pull` |
| 推送本地提交 | `git push` |
| 查看提交记录 | `git log --oneline` |
| 查看当前分支 | `git branch` |
| 查看远程地址 | `git remote -v` |
