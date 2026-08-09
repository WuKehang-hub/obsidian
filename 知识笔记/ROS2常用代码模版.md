---
tags: 
- ROS2
- Ubuntu
- Linux
date: 2025-03-03 01:50:55
---

# 常用复制粘贴

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

# 人脸检测
人脸检测服务实现
```python
import rclpy
from rclpy.node import Node
from chapt4_interfaces.srv import FaceDetector

import face_recognition
import cv2 
from ament_index_python.packages import get_package_share_directory
import os

from cv_bridge import CvBridge
import time

from rcl_interfaces.msg import SetParametersResult

class FaceDetectNode(Node):
    def __init__(self):
        super().__init__('face_detect_node')
        self.service_=self.create_service(FaceDetector,'face_detect',self.detect_face_callback)
        self.bridge=CvBridge()
        self.declare_parameter('number_of_times_to_usample',1)
        self.declare_parameter('model','hog')
        self.number_of_times_to_upsample=self.get_parameter('number_of_times_to_usample').value
        self.model=self.get_parameter('model').value
        self.myself_image_path=os.path.join(get_package_share_directory('demo_python_service'),'resource/myself.jpg')
        self.get_logger().info("人脸检测服务已启动")
        self.add_on_set_parameters_callback(self.parameters_callback)

    def parameters_callback(self,parameters):
        for parameter in parameters:
            self.get_logger().info(f"{parameter.name}->{parameter.value}")
            if parameter.name=='number_of_times_to_usample':
                self.number_of_times_to_upsample=parameter.value
            if parameter.value=='model':
                self.model=parameter.value
        return SetParametersResult(successful=True)
        

    def detect_face_callback(self,request,response):
        if request.image.data:
            cv_image=self.bridge.imgmsg_to_cv2(request.image)
        else:
            cv_image=cv2.imread(self.myself_image_path)
            self.get_logger().info(f"函数图像为空，使用默认图像")
        start_time=time.time()
        self.get_logger().info(f"加载完成图像，开始识别")
        face_locations=face_recognition.face_locations(cv_image,
        number_of_times_to_upsample=self.number_of_times_to_upsample,model=self.model)
        response.use_time=time.time() - start_time
        response.number=len(face_locations)
        for top,right,bottom,left in face_locations:
            response.top.append(top)
            response.right.append(right)
            response.bottom.append(bottom)
            response.left.append(left)
        return response


def main():
    rclpy.init()
    node=FaceDetectNode()
    rclpy.spin(node)
    rclpy.shutdown()
```

人脸检测客户端实现
```python
import rclpy
from rclpy.node import Node
from chapt4_interfaces.srv import FaceDetector

import cv2 
from ament_index_python.packages import get_package_share_directory
import os

from cv_bridge import CvBridge


class FaceDetectClientNode(Node):
    def __init__(self):
        super().__init__('face_detect_client_node')
        self.bridge=CvBridge()
        self.number_of_times_to_upsample=1
        self.test1_image_path=os.path.join(get_package_share_directory('demo_python_service'),'resource/test1.jpg')
        self.get_logger().info("人脸检测客户端已启动")
        self.client=self.create_client(FaceDetector,'face_detect')
        self.image=cv2.imread(self.test1_image_path)

    def send_request(self):
        while self.client.wait_for_service(timeout_sec=1.0) is False:
            self.get_logger().info('等待服务端上线')
        request=FaceDetector.Request()
        request.image=self.bridge.cv2_to_imgmsg(self.image)
        future=self.client.call_async(request)
        rclpy.spin_until_future_complete(self,future)
        response=future.result()
        self.get_logger().info(f'接收到响应，共检测到{response.number}张人脸，耗时{response.use_time}s')
        self.show_response(response)

    def show_response(self,response):
        for i in range(response.number):
            top=response.top[i]
            right=response.right[i]
            left=response.left[i]
            bottom=response.bottom[i]
            cv2.rectangle(self.image,(left,top),(right,bottom),(255,0,0),4)
        cv2.imshow('Face Detect Result',self.image)
        cv2.waitKey(0)

def main():
    rclpy.init()
    node=FaceDetectClientNode()
    node.send_request()
    rclpy.spin(node)
    rclpy.shutdown()
```

# TF变换
python发布静态tf
```python
import rclpy
from rclpy.node import Node
from tf2_ros import StaticTransformBroadcaster #静态坐标发布器
from geometry_msgs.msg import TransformStamped #消息接口
from tf_transformations import quaternion_from_euler #欧拉角转四元函数
import math

class StaticTFBroadcaster(Node):
    def __init__(self):
        super().__init__('static_tf_broadcaster')
        self.static_broadcaster_=StaticTransformBroadcaster(self)
        self.publish_static_tf()

    def publish_static_tf(self):
        #发布静态tf，从base_link到camera_link
        transform=TransformStamped()
        transform.header.frame_id='base_link'
        transform.child_frame_id='camera_link'
        transform.header.stamp=self.get_clock().now().to_msg()

        transform.transform.translation.x=0.5
        transform.transform.translation.y=0.3
        transform.transform.translation.z=0.6

        q=quaternion_from_euler(math.radians(180),0,0) #把180度角度值转成弧度值，再用欧拉角转四元函数转成一个元组
        #旋转部分进行赋值
        transform.transform.rotation.x=q[0]
        transform.transform.rotation.y=q[1]
        transform.transform.rotation.z=q[2]
        transform.transform.rotation.w=q[3]

        #发布静态坐标关系
        self.static_broadcaster_.sendTransform(transform)
        self.get_logger().info(f'发布静态TF:{transform}')

def main():
        rclpy.init()
        node=StaticTFBroadcaster()
        rclpy.spin(node)
        rclpy.shutdown()
```
python发布动态tf
```python
import rclpy
from rclpy.node import Node
from tf2_ros import TransformBroadcaster #坐标发布器
from geometry_msgs.msg import TransformStamped #消息接口
from tf_transformations import quaternion_from_euler #欧拉角转四元函数
import math

class TFBroadcaster(Node):
    def __init__(self):
        super().__init__('tf_broadcaster')
        self.broadcaster_=TransformBroadcaster(self)
        self.timer_=self.create_timer(0.01,self.publish_tf)

    def publish_tf(self):
        #发布tf，从camera_link到bottle_link
        transform=TransformStamped()
        transform.header.frame_id='camera_link'
        transform.child_frame_id='bottle_link'
        transform.header.stamp=self.get_clock().now().to_msg()

        transform.transform.translation.x=0.3
        transform.transform.translation.y=0.2
        transform.transform.translation.z=0.5

        q=quaternion_from_euler(0,0,0) #把180度角度值转成弧度值，再用欧拉角转四元函数转成一个元组
        #旋转部分进行赋值
        transform.transform.rotation.x=q[0]
        transform.transform.rotation.y=q[1]
        transform.transform.rotation.z=q[2]
        transform.transform.rotation.w=q[3]

        #发布静态坐标关系
        self.broadcaster_.sendTransform(transform)
        self.get_logger().info(f'发布TF:{transform}')

def main():
        rclpy.init()
        node=TFBroadcaster()
        rclpy.spin(node)
        rclpy.shutdown()
```
python查询tf变换
```python
import rclpy
from rclpy.node import Node
import rclpy.time
from tf2_ros import TransformListener,Buffer #坐标监听器
from tf_transformations import euler_from_quaternion #四元函数转欧拉角
import math

class TFBroadcaster(Node):
    def __init__(self):
        super().__init__('tf_broadcaster')
        self.buffer_=Buffer()
        self.listener_=TransformListener(self.buffer_,self)
        self.timer_=self.create_timer(1,self.get_transform)

    def get_transform(self):
        #实时查询坐标关系 buffer_
        try:
             result=self.buffer_.lookup_transform('base_link','bottle_link',rclpy.time.Time(seconds=0),rclpy.time.Duration(seconds=1))
             transform=result.transform
             self.get_logger().info(f'平移:{transform.translation}')
             self.get_logger().info(f'旋转:{transform.rotation}')
             rotation_euler=euler_from_quaternion([transform.rotation.x,
                                                  transform.rotation.y,
                                                  transform.rotation.z,
                                                  transform.rotation.w])
             self.get_logger().info(f'旋转RPY:{rotation_euler}')
        except Exception as e:
             self.get_logger().info(f'获取坐标变换失败，原因{str(e)}')


def main():
        rclpy.init()
        node=TFBroadcaster()
        rclpy.spin(node)
        rclpy.shutdown()
```
