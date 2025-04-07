# 在milk-v meles上测试ROS与yolo算法
## 环境配置
使用已经适配过的23.09版本镜像，刷入后使用lpi4a的rootfs替换  
替换后还需要进行额外操作：
1. 更改fstab到正确的设备来正确挂载boot
2. 拷贝modules到新的rootfs
3. 拷贝博通网卡的firmware到新的rootfs
4. 在rc.local中配置pwm输出来启用风扇
5. 显卡与NPU缺少驱动
由于显卡和NPU暂时缺少驱动，只能先使用23.09版本测试yolo
## 23.09环境配置
这里参考荔枝派的文档https://wiki.sipeed.com/hardware/zh/lichee/th1520/lpi4a/8_application.html#Yolov5n
由于oerv.wiki暂时无法访问（截至2025-4-5），没拿到23.09不带ros的镜像，故不包含ros的配置
### python环境
#### 安装预编译的python wheel
由于需要同时使用ros的环境，所以不采取虚拟环境安装，
```shell
git clone -b python3.11 https://github.com/zhangwm-pt/prebuilt_whl.git
cd prebuilt_whl
pip install numpy-1.25.0-cp311-cp311-linux_riscv64.whl
pip install kiwisolver-1.4.4-cp311-cp311-linux_riscv64.whl
pip install Pillow-9.5.0-cp311-cp311-linux_riscv64.whl
pip install matplotlib-3.7.2.dev0+gb3bd929cf0.d20230630-cp311-cp311-linux_riscv64.whl
pip install pycocotools-2.0.6-cp311-cp311-linux_riscv64.whl
pip3 install loguru-0.7.0-py3-none-any.whl
pip3 install torch-2.0.0a0+gitc263bd4-cp311-cp311-linux_riscv64.whl
pip3 install MarkupSafe-2.1.3-cp311-cp311-linux_riscv64.whl
pip3 install torchvision-0.15.1a0-cp311-cp311-linux_riscv64.whl
pip3 install psutil-5.9.5-cp311-abi3-linux_riscv64.whl
pip3 install tqdm-4.65.0-py3-none-any.whl
pip3 install tabulate-0.9.0-py3-none-any.whl
```
这里由于`ultralytic`需求opencv-python版本>=3.6.0，进行了对opencv-python的交叉编译，教程可以在网上找到，也可以直接从仓库里拉下去

可以直接下载仓库中的opencv-python，安装  
2. yolo环境配置
```shell
pip install ultralytics
```

ros包配置与测试和其他架构相同，可见https://openeuler-ros-docs.readthedocs.io/en/latest/other-tutorials/yolo-ros.html

