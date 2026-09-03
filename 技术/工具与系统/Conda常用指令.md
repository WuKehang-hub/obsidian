---
tags:
  - Conda
  - Python
  - 环境管理
date: 2026-08-20
---

# Conda 是什么

Conda 主要用于管理 **Python 环境**和**软件包**。

不同项目可能需要不同版本的 Python、PyTorch 或 NumPy。Conda 可以为每个项目创建独立环境，避免版本互相冲突。

例如：

- 项目 A 使用 Python 3.10 和 PyTorch 2.0；
- 项目 B 使用 Python 3.12 和新版 PyTorch；
- 两套环境彼此隔离，不会互相覆盖。

> 一个 Conda 环境可以理解成一个独立的 Python 工具箱。安装包之前，应先确认自己进入了正确的环境。

# Anaconda 和 Miniconda

Anaconda 和 Miniconda 都可以用来安装 Conda，主要区别是默认附带的软件包数量不同。

## Anaconda

Anaconda 是“全家桶”方案，安装后已经包含：

- Python 解释器；
- Conda 环境和包管理工具；
- 一个 `base` 环境；
- NumPy、Pandas、Matplotlib 等大量数据科学软件包。

优点是安装后很多工具可以直接使用，缺点是体积大，而且其中很多软件包可能永远用不到。

## Miniconda

Miniconda 是精简方案，默认只包含 Python、Conda 和少量必要依赖。

使用者可以在创建环境后，再根据项目需求安装 PyTorch、NumPy 等软件包。这种方式体积更小，环境也更干净。

| 对比项 | Anaconda | Miniconda |
| --- | --- | --- |
| 默认软件包 | 预装大量数据科学包 | 只安装必要组件 |
| 占用空间 | 较大 | 较小 |
| 安装方式 | 安装后很多工具可直接使用 | 需要什么就安装什么 |
| 适合人群 | 希望快速获得完整数据科学环境的用户 | 希望按项目管理干净环境的用户 |

> 一般情况下更推荐 Miniconda：它更轻量，也能避免预装大量用不到的软件包。Anaconda 和 Miniconda 使用的 Conda 指令是相同的。

# 查看 Conda 和环境

```bash
# 查看 Conda 版本
conda --version

# 查看 Conda 的基本信息和当前环境
conda info

# 查看所有 Conda 环境
conda env list
```

`conda env list` 的结果中，带 `*` 的环境是当前正在使用的环境。

例如：

```text
base                  *  /home/user/miniconda3
robot                     /home/user/miniconda3/envs/robot
```

这里表示当前处于 `base` 环境。

# 创建环境

最常用的创建方式是同时指定环境名和 Python 版本：

```bash
conda create -n robot python=3.10
```

其中：

- `create` 表示创建环境；
- `-n` 是 `--name` 的缩写；
- `robot` 是环境名，可以自行修改；
- `python=3.10` 表示安装 Python 3.10。

创建环境时也可以同时安装常用包：

```bash
conda create -n robot python=3.10 numpy pandas
```

添加 `-y` 后，Conda 会自动确认安装：

```bash
conda create -n robot python=3.10 -y
```

# 激活和退出环境

激活环境：

```bash
conda activate robot
```

激活成功后，终端开头通常会显示环境名：

```text
(robot) user@computer:~$
```

退出当前环境：

```bash
conda deactivate
```

检查当前使用的 Python：

```bash
which python
python --version
```

Windows 使用：

```powershell
where python
python --version
```

> 如果包安装成功后仍然无法导入，首先检查运行代码的 Python 和安装包时使用的 Python 是否属于同一个环境。

# 删除和复制环境

删除环境前，先退出该环境：

```bash
conda deactivate
conda env remove -n robot
```

也可以使用：

```bash
conda remove -n robot --all
```

复制一个已有环境：

```bash
conda create -n robot_backup --clone robot
```

克隆环境适合在升级重要依赖前进行备份。

# 安装和查看软件包

在当前环境中安装包：

```bash
conda install numpy
```

安装指定版本：

```bash
conda install numpy=1.26
```

同时安装多个包：

```bash
conda install numpy pandas matplotlib
```

从指定软件源安装：

```bash
conda install -c conda-forge opencv
```

其中，`-c` 是 `--channel` 的缩写，`conda-forge` 是常用的社区软件源。

查看当前环境中已经安装的包：

```bash
conda list
```

查询某个包是否已经安装：

```bash
conda list numpy
```

搜索 Conda 中是否存在某个包：

```bash
conda search numpy
```

# 更新和卸载软件包

更新指定软件包：

```bash
conda update numpy
```

卸载指定软件包：

```bash
conda remove numpy
```

更新 Conda 本身：

```bash
conda update conda
```

更新当前环境的所有包：

```bash
conda update --all
```

> `conda update --all` 可能同时改变大量依赖版本。重要项目应先导出环境配置或克隆环境，再进行整体更新。

# Conda 和 pip 如何配合

pip 是 Python 常用的**软件包管理工具**，主要用来从 PyPI（Python Package Index）下载、安装、更新和卸载 Python 软件包。

例如：

```bash
python -m pip install requests
python -m pip uninstall requests
python -m pip list
```

其中，`install` 用于安装包，`uninstall` 用于卸载包，`list` 用于查看已安装的 Python 包。

pip 主要管理 Python 软件包，而 Conda 除了管理 Python 包，还能创建独立环境，并管理部分 CUDA、C/C++ 库等非 Python 依赖。

| 对比项            | pip               | Conda                 |
| -------------- | ----------------- | --------------------- |
| 主要用途           | 安装 Python 软件包     | 管理环境和软件包              |
| 常用软件源          | PyPI              | Conda 的 channels      |
| 是否管理 Python 版本 | 不负责创建独立 Python 环境 | 可以为每个环境安装不同 Python 版本 |
| 非 Python 依赖    | 处理能力较有限           | 可以管理部分系统级依赖           |

Conda 环境中也可以使用 pip。pip 会把包安装到当前激活环境的 Python 中，所以安装前必须先确认已经激活正确的 Conda 环境。

推荐写法：

```bash
conda activate robot
python -m pip install package_name
```

推荐使用 `python -m pip`，而不是直接使用 `pip`，因为这样可以更明确地调用当前 Python 对应的 pip，降低包装错环境的概率。

推荐安装顺序：

1. 先激活正确的 Conda 环境；
2. 优先使用 Conda 安装软件包；
3. Conda 中找不到时，再使用 pip；
4. 使用 pip 后，尽量不要再让 Conda 大规模更新所有依赖。

检查 Python 和 pip 是否属于同一个环境：

```bash
which python
python -m pip --version
```

Windows 使用：

```powershell
where python
python -m pip --version
```

# 以 editable 模式安装本地项目

开发 Python 项目时，可以使用 `-e` 选项进行可编辑安装：

```bash
conda activate robot
cd /path/to/project
python -m pip install -e .
```

其中：

- `-e` 是 `--editable` 的缩写；
- `.` 表示安装当前目录中的项目；
- 安装后可以像普通 Python 包一样导入该项目；
- 环境会直接引用本地源码，修改代码后通常无须重新安装。

例如，将 `rsl_rl` 安装到当前 Conda 环境：

```bash
conda activate robot
cd /path/to/rsl_rl
python -m pip install -e .
```

> 必须在运行项目所使用的同一个 Conda 环境中执行安装，否则运行时可能出现 `ModuleNotFoundError`。

editable 安装依赖原项目目录，因此安装后不要随意删除或移动源码目录。需要取消安装时，使用项目的包名卸载：

```bash
python -m pip uninstall package_name
```

# 导出和恢复环境

将当前环境导出为 YAML 文件：

```bash
conda env export > environment.yml
```

如果只想记录自己主动安装的主要依赖，使文件更简洁，可以使用：

```bash
conda env export --from-history > environment.yml
```

根据 `environment.yml` 创建环境：

```bash
conda env create -f environment.yml
```

使用配置文件更新已有环境：

```bash
conda env update -n robot -f environment.yml --prune
```

`--prune` 表示删除环境中存在、但配置文件中没有声明的包，使环境与配置文件保持一致。

两种导出方式的区别：

| 指令 | 特点 | 适用场景 |
| --- | --- | --- |
| `conda env export` | 记录完整依赖和具体版本 | 在相近系统上精确复现 |
| `conda env export --from-history` | 只记录主动安装的主要包 | 分享项目、跨电脑创建环境 |

# 常见问题


## 依赖冲突

出现依赖冲突时，优先尝试：

1. 不要同时指定太多软件包的精确版本；
2. 减少不同软件源的混用；
3. 先安装 PyTorch 等核心框架，再安装其他包；
4. 冲突严重时创建一个干净的新环境，不要反复修补旧环境。

# 常用工作流程

创建项目环境：

```bash
conda create -n robot python=3.10 -y
conda activate robot
conda install numpy pandas matplotlib
python -m pip install other_package
```

保存项目环境：

```bash
conda env export --from-history > environment.yml
```

在另一台电脑恢复环境：

```bash
conda env create -f environment.yml
conda activate robot
```

删除不再使用的环境：

```bash
conda deactivate
conda env list
conda env remove -n robot
```

# 常用指令速查

| 目的 | 指令 |
| --- | --- |
| 查看 Conda 版本 | `conda --version` |
| 查看所有环境 | `conda env list` |
| 创建环境 | `conda create -n robot python=3.10` |
| 激活环境 | `conda activate robot` |
| 退出环境 | `conda deactivate` |
| 删除环境 | `conda env remove -n robot` |
| 克隆环境 | `conda create -n newenv --clone oldenv` |
| 查看已安装的包 | `conda list` |
| 搜索包 | `conda search package_name` |
| 安装包 | `conda install package_name` |
| 安装指定版本 | `conda install package_name=版本号` |
| 从 conda-forge 安装 | `conda install -c conda-forge package_name` |
| 更新包 | `conda update package_name` |
| 卸载包 | `conda remove package_name` |
| 使用 pip 安装 | `python -m pip install package_name` |
| 导出主要依赖 | `conda env export --from-history > environment.yml` |
| 根据 YAML 创建环境 | `conda env create -f environment.yml` |
