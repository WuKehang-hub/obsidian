---
tags:
  - ROS2
  - Ubuntu
  - Linux
date: 2025-03-12
---

![[附件/post-ros2-notes-cover.png|720]]

这篇笔记记录一些在学习和使用 ROS 2 时遇到的基础细节。

## 终端与工作空间

### `source` 与 `colcon build`

同一个终端中，ROS 2 的系统环境通常只需要加载一次：

```bash
source /opt/ros/humble/setup.bash
```

构建工作空间：

```bash
cd ~/your_ws
colcon build
```

第一次构建工作空间，或者新增了功能包、接口和可执行文件后，需要重新加载工作空间环境：

```bash
source install/setup.bash
```

新开终端后，需要重新执行相应的 `source` 命令。也可以把命令写入 `~/.bashrc`，让终端启动时自动加载。

### 终端快捷操作

- 按方向键 `↑` 可以调出上一条命令，适合快速修改并重新执行。
- `Ctrl + C` 用于终止当前命令，不是复制。
- Linux 终端中的复制和粘贴通常是 `Ctrl + Shift + C` 与 `Ctrl + Shift + V`。
- 输入命令或文件名的一部分后按 `Tab`，可以自动补全。

## 编辑代码时的快捷键

以下是 VS Code 中常用的编辑操作：

- `Ctrl + /`：注释或取消注释当前行。
- `Alt + ↑` / `Alt + ↓`：向上或向下移动当前行。
- 光标停在一行中时，直接按 `Ctrl + C` 再按 `Ctrl + V`，可以复制整行。
- `Ctrl + F`：在当前文件中搜索。
- `Ctrl + Shift + F`：在整个工程中搜索。

> 将 `cat`、`xacro` 等命令与文件路径拼接时，中间必须保留空格。例如：`xacro robot.urdf.xacro`。

## ROS 2 常用英文术语

| 英文术语 | 中文含义 |
| --- | --- |
| package | 功能包 |
| executable | 可执行文件 |
| parameter | 参数 |
| argument | 命令行参数 |
| topic | 话题 |
| service | 服务 |
| sensor | 传感器 |
| actuator | 执行器或执行机构 |
| publisher | 发布者 |
| subscriber | 订阅者 |

名称中包含 `default` 的变量通常表示“默认值”。调用者没有传入其他值时，程序会采用该默认值；显式传值后，则使用传入的值。

## `parameters` 与 `arguments` 的区别

- **parameters**：ROS 2 节点参数，由节点的参数系统管理，可以在启动文件、YAML 文件或命令行中设置。
- **arguments**：传递给可执行程序的普通命令行参数，由程序自行解析。

Launch 文件中的简单示例：

```python
from launch_ros.actions import Node

robot_node = Node(
    package="demo_package",
    executable="demo_node",
    parameters=[{"use_sim_time": True}],
    arguments=["--verbose"],
)
```

这里的 `use_sim_time` 是 ROS 2 参数，`--verbose` 是传给程序的命令行参数。

## Xacro、URDF、SDF 与插件

URDF、SDF 和 Xacro 文件本质上都使用 XML 风格的语法，但用途不同：

- **URDF**：描述机器人的连杆、关节、惯性和外观等信息。
- **SDF**：描述 Gazebo 世界、模型、传感器和插件等内容，表达能力比 URDF 更丰富。
- **Xacro**：一种 XML 宏语言，通过变量、宏和条件语句生成最终的 URDF/XML 内容。

Xacro 文件本身不会被划分为“执行器文件”或“插件文件”。ROS 2 和 Gazebo 关注的是 Xacro 展开后生成的 URDF/SDF 标签。

判断插件和硬件接口时主要看以下标签：

- Gazebo 插件通常写在 `<gazebo>` 中，并通过 `<plugin>` 声明动态库。
- `ros2_control` 的硬件接口写在 `<ros2_control>` 中。
- Gazebo 主要处理 `<gazebo>` 中的插件配置。
- `ros2_control` 根据 `<ros2_control>` 中的硬件与关节接口配置工作。

示例：

```xml
<gazebo>
  <plugin filename="libgazebo_ros2_control.so"
          name="gazebo_ros2_control"/>
</gazebo>
```

> 在正文中直接书写 `<gazebo>`、`<plugin>` 等 XML 标签时，应使用反引号包裹，否则 Obsidian 可能把它们当作 HTML 标签，导致后续 Markdown 渲染异常。

## XML 标签未闭合报错

如果 Xacro 或 URDF 报错并提示 `Check that your XML is well-formed`，通常表示 XML 格式不正确。常见原因包括：

- 开始标签没有对应的结束标签；
- 标签嵌套顺序错误；
- 属性引号缺失；
- 单标签结尾误写为 `>`，正确形式应为 `/>`。

例如：

```xml
<!-- 错误：标签没有闭合 -->
<mesh filename="robot.stl">

<!-- 正确：自闭合标签 -->
<mesh filename="robot.stl"/>
```

可以使用下面的命令检查 Xacro 是否能够正常展开：

```bash
xacro robot.urdf.xacro > /tmp/robot.urdf
check_urdf /tmp/robot.urdf
```

## 解决 `rclpy` 导入警告

### 现象

终端中可以正常运行 ROS 2 节点，但 VS Code 对 `import rclpy` 显示黄色波浪线。

### 原因

VS Code 当前选择的 Python 解释器或 Pylance 搜索路径中没有 ROS 2 的 Python 包路径。

### 解决方法

方法一：从已经加载 ROS 2 环境的终端启动 VS Code。

```bash
source /opt/ros/humble/setup.bash
code .
```

方法二：确认 VS Code 选择的 Python 解释器与 ROS 2 使用的 Python 版本一致。

方法三：在 `.vscode/settings.json` 中添加额外搜索路径。以下路径仅适用于 Ubuntu 22.04 与 ROS 2 Humble 的常见安装环境，其他版本需要相应修改：

```json
{
  "python.analysis.extraPaths": [
    "/opt/ros/humble/lib/python3.10/site-packages"
  ]
}
```

## Topic 与 Service 的区别

| 对比项 | Topic | Service |
| --- | --- | --- |
| 通信形式 | 发布/订阅 | 请求/响应 |
| 数据方向 | 通常为单向数据流 | 一次请求对应一次响应 |
| 耦合程度 | 发布者不要求订阅者同时在线 | 客户端需要等待服务端响应 |
| 适用场景 | 雷达、图像、状态等连续数据 | 开关、计算、状态查询等离散操作 |

> Service 不是 Topic 的上位替代。两者解决的是不同类型的通信问题。

## YAML 参数文件与 SRV 接口文件

- **YAML（`.yaml`）**：保存具体配置数据，例如颜色阈值、PID 参数和控制器配置。
- **SRV（`.srv`）**：定义服务请求与响应的数据结构，不负责保存运行时的具体数值。

简单的 `.srv` 示例：

```text
int64 a
int64 b
---
int64 sum
```

`---` 上方是请求字段，下方是响应字段。

## `setup.py` 中的 `data_files` 与 `entry_points`

### `data_files`

`data_files` 负责把 Launch、Config、URDF 等非 Python 资源安装到工作空间的 `install/share/` 目录。如果资源没有被安装，节点运行时可能找不到相应文件。

### `entry_points`

`entry_points` 用于把 Python 函数注册为 ROS 2 可执行命令：

```python
entry_points={
    "console_scripts": [
        "demo_node = demo_package.demo_node:main",
    ],
}
```

格式为：

```text
命令名 = 包名.模块名:入口函数
```

## 编写 Service 服务端的易错点

创建服务的常见写法：

```python
self.create_service(ServiceType, "service_name", self.callback)
```

回调函数需要返回响应对象：

```python
def callback(self, request, response):
    response.sum = request.a + request.b
    return response
```

如果忘记 `return response`，服务端无法正常返回有效响应，客户端可能持续等待、超时或产生运行时错误。

## RViz 坐标轴颜色

RViz 中默认使用以下颜色表示坐标轴：

- 红色：X 轴；
- 绿色：Y 轴；
- 蓝色：Z 轴。

## ROS 2 与 Gazebo 桥接器（ros_gz_bridge）语法

在 Launch 文件中配置 `parameter_bridge` 时，字符串格式非常严谨，核心公式为：

`话题名称@ROS数据类型<方向号>Gazebo数据类型`

**1. 符号含义：**

- `@`：分隔符，分隔话题名称和数据类型。
- `]`：单向通信，数据从 **ROS 2 流向 Gazebo**（例如：下发控制指令、推力）。
- `[`：单向通信，数据从 **Gazebo 流向 ROS 2**（例如：获取传感器数据、Odometry 里程计位置）。
- `@` 或 `==` (有些版本支持双向)：双向通信。

**2. 核心代码结构：**

```python
from launch_ros.actions import Node

# 1. 定义桥接规则列表
bridge_params = [
    '/topic_name@ros_type]gz_type', # ROS -> Gazebo
]

# 2. 创建 Node 节点
bridge = Node(
    package='ros_gz_bridge',
    executable='parameter_bridge',
    arguments=bridge_params,
    output='screen'
)
```
