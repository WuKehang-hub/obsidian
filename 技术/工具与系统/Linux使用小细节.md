---
tags:
  - Linux
  - Ubuntu
date: 2026-08-13
---
这篇笔记记录日常使用 Linux 时的基础操作。

## Linux 终端光标

终端中的矩形光标表示当前字符位置。可以把矩形左边缘理解为常见竖线光标所在的位置。

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

