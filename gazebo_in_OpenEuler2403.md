# Gazebo OpenEuler测试
## 环境配置
OS:OpenEuler 24.03 x86_64 WSL   
硬件平台：
CPU: AMD Ryzen9 7940HX    
GPU0: NVIDIA RTX 4060 Laptop(8G VRAM)    
GPU1: AMD Radeon 610M   
内存容量:64G
### 安装gazebo
```shell
cat<<EOF > /etc/yum.repos.d/gazebo.repo
[openEulerROS-humble-3rdparty]
name=openEulerROS-humble
baseurl=https://eulermaker.compass-ci.openeuler.openatom.cn/api/ems1/repositories/ROS-SIG-Multi-Version_ros-humble_openEuler-24.03-LTS-ROS-3rdparty/openEuler%3A24.03-LTS/x86_64 
enabled=1
gpgcheck=0
EOF
dnf install gazebo*
```
## 测试gazebo
启动时报错
```shell
gazebo: error while loading shared libraries: libhdf5.so.310: cannot open shared object file: No such file or directory
```
`ldd`查看发现确实没有对应的.so
```shell
ifreturn@DESKTOP-UN7SN9H ~> ldd /usr/bin/gazebo|grep hdf
        libhdf5.so.310 => not found
        libhdf5_hl.so.200 => /usr/lib64/libhdf5_hl.so.200 (0x00007f88ade45000)
        libhdf5.so.200 => /usr/lib64/libhdf5.so.200 (0x00007f88ada4f000)
```
locate发现`/usr/lib64/mpich/lib/libhdf5.so.310`    
（以下方法仅作测试使用，不建议学习）   
在/etc/ld.so.conf.d中添加文件`custom.conf`并刷新ld缓存
```shell
echo "/usr/lib64/mpich/lib" > /etc/ld.so.conf.d/custom.conf
ldconfig
```
再次启动gazebo

正常启动，观测到严重的性能问题，控制台输出error
```shell
MESA-LOADER: failed to open zink: /usr/lib64/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib64/dri, suffix _dri)
failed to load driver: zink
MESA-LOADER: failed to open zink: /usr/lib64/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib64/dri, suffix _dri)
failed to load driver: zink
MESA-LOADER: failed to open zink: /usr/lib64/dri/zink_dri.so: cannot open shared object file: No such file or directory (search paths /usr/lib64/dri, suffix _dri)
failed to load driver: zink
AL lib: (WW) GetSymbol: Failed to load jack_error_callback: /usr/lib64/libjack.so.0: undefined symbol: jack_error_callback
AL lib: (WW) jack_msg_handler: Cannot connect to server socket err = No such file or directory
AL lib: (WW) jack_msg_handler: Cannot connect to server request channel
AL lib: (WW) jack_msg_handler: jack server is not running or cannot be started
AL lib: (WW) jack_msg_handler: JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
AL lib: (WW) jack_msg_handler: JackShmReadWritePtr::~JackShmReadWritePtr - Init not done for -1, skipping unlock
AL lib: (WW) ALCjackBackendFactory_init: jack_client_open() failed, 0x11
AL lib: (WW) alc_initconfig: Failed to initialize backend "jack"
```
安装mesa相关包
```shell
dnf install mesa*
```
仍无改善   
在源中搜索`zink_dri.so`
```shell
dnf provides */zink_dri.so                                                                                                                 
Error: No matches found. If searching for a file, try specifying the full path or using a wildcard prefix ("*/") at the beginning.
```
无结果