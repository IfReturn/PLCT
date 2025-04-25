# OpenEuler 上ROS-Yolo的测试与简单应用
## 环境相关
### 硬件 
1. USB相机、相机云台
2. 开发板，分别为Arm下的树莓派5与Risc-V架构下的Milk-v meles
### 软件
1. OpenEuler 24.03操作系统
2. ROS humble
## 总体结构
使用相机进行目标检测，之后使用云台进行跟踪。  
相机使用USB进行连接，云台则使用GPIO输出PWM波进行控制。  
 