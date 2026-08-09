---
tags: 
- ROS2
- Ubuntu
- Linux
date: 2025-03-12 01:50:55
---
# ROS2小细节
这篇博客记载了一些小坷自己摸索的ros2的小细节，很基础但对初学者可能有用，毕竟教程一般不教这个。

## 终端细节
同一个终端内，source一次就够了，后续只需要colcon build。当然，新建终端之后要重新source

终端里点击“上”就可以得到上一次输入的指令，在第一次指令输入错误需要再输一次又不想一个字一个字敲时很方便

## ROS2代码细节
Ctrl+/快速注释

Alt+上下键可以控制单行代码上下移动

**cat和xacro作为命令，后面拼接路径时记得加空格**

光标放在某一行，无需选中任何，快速Ctrl c+Ctrl v可以实现一整行的复制粘贴

名字里带有default的变量，表示默认的意思。一般来说，如果不指定，那么就按default来执行。如果指定了，那么按照制定了的来执行

ros2里常用单词含义
| 英文单词 (English Term) | 中文含义 (Chinese Meaning) |
| :--- | :--- |
| package | 功能包 |
| executable | 可执行文件 |
| parameters/arguments | 参数 |
| sensor | 传感器 |
| actuator | 执行机构 |

## 区分传**parameters**和**arguments**的不同

parameters主要是给节点传参数，而parameters则相当于在命令行后面加内容

有的xacro文件是执行器，有的xacro文件是插件，ros2是怎么判断的？他怎么知道哪个是插件？:XACRO 是一种宏语言，用于简化 URDF 的编写。它通过宏替换生成最终的 URDF 文件，而 ROS 2 和 Gazebo 仅解析最终的 URDF/SDF 文件中的标签。XACRO 文件本身没有“插件”或“执行器”的标记，插件和执行器的定义是通过 URDF/SDF 中的特定标签完成的。关键规则:
* 标签层级：插件必须定义在特定标签内（如 <gazebo> 或 <ros2_control>）。
* 插件标识：通过 <plugin> 标签显式声明，且必须包含插件的类型或动态库路径（如 filename="libgazebo_ros2_control.so"）。
* 上下文语义：Gazebo 或 ros2_control 会根据标签的上下文判断是否需要加载插件。例如：Gazebo 会忽略 <ros2_control> 标签，只处理 <gazebo> 内的插件。
ros2_control 会忽略 <gazebo> 标签，只处理 <ros2_control> 内的硬件接口。

## 其他
所谓的urdf,sdf,xacro这些都只是文件的后缀名，用来描述该文件的类型，但其实他们本质都是xml格式的文件。

我对ros2control的通俗的理解：集成了一些控制器,感觉可以类比机器学习里的anaconda。

rviz里，红色是x轴，绿色是y轴，蓝色是z轴

xacro或urdf出现报错：Check that :Your XML is well-formed，说明XML格式错误，如果实在找不出错误，那么常见原因：标签未闭合。将单行标签末尾从>改成/>

## 解决 `rclpy` 导入报错与环境配置
* **现象**：终端运行正常，但在 VS Code 中 `import rclpy` 报黄色波浪线警告。
* **原因**：VS Code 的 Python 插件默认只扫描标准库路径，不知道 ROS 2 的库在哪里。
* **解决**：
    1.  从已 source 过 ROS 环境的终端启动 VS Code (`code .`)。
    2.  或在 `.vscode/settings.json` 中添加 `python.analysis.extraPaths`，指向 `/opt/ros/humble/lib/python3.10/site-packages`。

## 话题 (Topic) 与服务 (Service) 的核心区别
* **通信模型**：
    * **Topic**：单向广播（流式数据）。发布者只管发，不关心有没有人收。适用于雷达、图像等连续数据。
    * **Service**：双向请求（一问一答）。客户端请求，服务端必须反馈。适用于开关、状态查询等瞬时操作。
* **误区**：服务**不是**话题的上位替代，两者适用场景完全不同。

## 参数文件 (.yaml) 与服务定义 (.srv) 的区别
* **YAML (.yaml)**：用于**存储具体数据**（配置存档）。例如机器人的颜色阈值、PID 参数。
* **SRV (.srv)**：用于**定义接口结构**（通信合同）。定义请求包含什么字段（如 `int64 a`），响应包含什么字段（如 `int64 sum`），**不存储数值**。

## `setup.py` 中 `data_files` 与 `entry_points` 的作用
* **data_files**：**搬运工**。负责将非代码资源（Launch 文件、图片、Config）从源码目录复制到安装目录 (`install/share/`)。如果不写，程序运行时会找不到文件。
* **entry_points**：**入口生成器**。负责将 Python 函数注册为终端可执行命令。
    * 格式：`'命令名 = 包名.模块名:main函数'`。

## 编写 Service 服务端 (`create_service`) 的易错点
* **API**：`self.create_service(类型, '名称', 回调函数)`。
* **致命细节**：回调函数 (`def callback(request, response):`) 执行完毕后，**必须 `return response`**。
* **后果**：如果忘记 return，客户端会因为收不到响应而陷入死锁（一直等待）。

# Linux小细节
这个不单独开一篇了，就放这里了

## Linux终端
Linux终端中的矩形光标很烦人，非常不直观。我的技巧：矩形的左端的边就是Windows里的光标的位置，这样就很好判断了

注意终端里的复制粘贴是ctrl+**shift**+c/v，单独按ctrl+c是取消当前操作

## Linux如何安装软件：
1. 到该软件的官网，找到Linux版本的下载包，选择64位Deb格式，点击下载
2. 到“下载”文件夹中，点击右键，点击“在终端中打开”；或者直接打开终端，cd到下载。然后sudo dpkg -i +包名。例：sudo dpkg -i wps-office_11.1.0.10161_amd64.deb

## Linux如何卸载软件
打开终端，然后sudo apt-get remove --purge +软件名称，如果不确定软件名称是什么，比如到底是wps还是wps2019，可以输入大概然后tab键补全。  

