---
tags:
  - ROS2
  - Ubuntu
  - Linux
date: 2025-03-03
---

无需多言
```bash
colcon build
source install/setup.bash
```

这个也无需多言
```python
def main():
        rclpy.init()
        node=StaticTFBroadcaster()
        rclpy.spin(node)
        rclpy.shutdown()
```

这个更加无需多言
```python
import rclpy
from rclpy.node import Node
```

创建功能包
```bash
ros2 pkg create 包名 --build-type ament_python --dependencies 依赖名 --license Apache-2.0 
```

日常打印
```python
self.get_logger().info()
```

launch
```python
import launch
import launch_ros

def generate_launch_description():
    action_node_turtlesim_node=launch_ros.actions.Node(
        package='turtlesim',
        executable='turtlesim_node',
        output='screen'
    )

    action_node_face_detect=launch_ros.actions.Node(
        package='demo_python_service',
        executable='face_detect_node',
        output='screen'
    )


    return launch.LaunchDescription([
        action_node_turtlesim_node,
        action_node_face_detect
    ])
```

