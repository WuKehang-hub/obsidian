---
tags:
  - Linux
  - Ubuntu
date: 2026-08-13
---

这篇笔记记录日常使用 Linux 时的基础操作。

## 打开终端

在 Ubuntu 中按 `Ctrl+Alt+T`，可以快速打开终端。

如果想直接在某个文件夹中打开终端，可以在文件管理器中进入该文件夹，右键单击空白处，然后选择“在终端中打开”。

## 查看剩余存储空间

查看各文件系统的总容量、已用空间和剩余空间：

```bash
df -h
```

其中 `-h` 会用 GB、MB 等易读单位显示容量。通常重点查看挂载点为 `/` 的一行。

## 查看与切换文件夹

显示当前所在路径：

```bash
pwd
```

列出当前文件夹中的内容：

```bash
ls
ls -lah
```

`ls -lah` 会显示隐藏文件、详细信息和易读的文件大小。

进入指定文件夹：

```bash
cd folder_name
```

常用的路径切换方式：

```bash
cd ..       # 返回上一级文件夹
cd ~        # 进入当前用户的主目录
cd /        # 进入根目录
cd -        # 返回上一次所在的文件夹
cd "folder name"  # 进入名称中含空格的文件夹
```

路径以 `/` 开头时是绝对路径；不以 `/` 开头时，通常是相对于当前文件夹的相对路径。输入路径时可以按 `Tab` 键自动补全。

## 创建文件和文件夹

创建文件夹：

```bash
mkdir folder_name
mkdir -p parent/child
```

`-p` 会在需要时一并创建不存在的父文件夹。

创建空文件：

```bash
touch file_name.txt
```

## 复制、移动与删除

复制文件或文件夹：

```bash
cp source.txt destination.txt
cp -r source_folder destination_folder
```

移动文件，或为文件重命名：

```bash
mv old_name.txt new_name.txt
mv file.txt destination_folder/
```

删除文件或文件夹：

```bash
rm file_name.txt
rm -r folder_name
```

> `rm` 删除的内容通常不会进入回收站，使用 `rm -r` 前应仔细确认路径。不要在不理解含义时使用 `rm -rf`。

## 常用终端操作

清空当前终端画面：

```bash
clear
```

按方向键上键可以调出之前执行过的命令，按 `Ctrl+C` 可以终止当前命令，按 `Ctrl+L` 可以清屏。

>终端中的矩形光标表示当前字符位置。可以把==矩形左边缘==理解为常见竖线光标所在的位置。

## 安装 Deb 软件包

1. 从软件官网下载安装包，通常选择适合当前架构的 `.deb` 文件，例如 `amd64`。
2. 在下载目录打开终端。
3. 使用 `apt` 安装本地软件包，它会同时处理依赖：

```bash
cd ~/下载
sudo apt install ./package_name_amd64.deb
```

也可以使用 `dpkg`：

```bash
sudo dpkg -i package_name_amd64.deb
sudo apt --fix-broken install
```

## 卸载软件

保留配置文件：

```bash
sudo apt remove package_name
```

同时删除系统级配置文件：

```bash
sudo apt purge package_name
```

如果不确定软件包的准确名称，可以先搜索：

```bash
apt list --installed 2>/dev/null | grep -i keyword
```
