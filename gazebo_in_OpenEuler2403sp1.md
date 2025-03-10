# Gazebo OpenEuler测试
## 环境配置
OS:OpenEuler 24.03 sp1 x86_64 WSL

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
出现问题
```shell
 Problem 1: conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
 Problem 2: conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-libs-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
 Problem 3: package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_gimpact.so.11()(64bit), but none of the providers can be installed
  - package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_opcode.so.11()(64bit), but none of the providers can be installed
  - package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_opende_ou.so.11()(64bit), but none of the providers can be installed
  - conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-libs-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
 Problem 4: package gazebo-devel-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires gazebo-libs(x86-64) = 11.14.0-7.oe2403, but none of the providers can be installed
  - conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-libs-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
 Problem 5: package gazebo-media-11.14.0-7.oe2403.noarch from openEulerROS-humble-3rdparty requires gazebo = 11.14.0-7.oe2403, but none of the providers can be installed
  - conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
 Problem 6: package gazebo-ode-devel-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires gazebo-ode(x86-64) = 11.14.0-7.oe2403, but none of the providers can be installed
  - package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_gimpact.so.11()(64bit), but none of the providers can be installed
  - package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_opcode.so.11()(64bit), but none of the providers can be installed
  - package gazebo-ode-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty requires libgazebo_opende_ou.so.11()(64bit), but none of the providers can be installed
  - conflicting requests
  - nothing provides libtbb.so.2()(64bit) needed by gazebo-libs-11.14.0-7.oe2403.x86_64 from openEulerROS-humble-3rdparty
```
安装tbb包，检索发现系统中存在`/usr/lib64/libtbb.so`,尝试手动添加软连接，之后强制安装
```shell
ln -sf  /usr/lib64/libtbb.so /usr/lib64/libtbb.so.2
dnf download gazebo*
rpm -ivh --nodeps --force ./gazeho* 
```
## 测试过程
source gazebo环境
```shell
source /usr/share/gazebo/setup.bash
```
启动gazebo
```shell
gazebo
```
依次修复报错
```shell
gazebo: error while loading shared libraries: libccd.so.2: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libBulletSoftBody.so.2.87: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libgdal.so.33: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libOgreRTShaderSystem.so.1.9.0: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libtinyxml2.so.9: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libignition-fuel_tools4.so.4: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libtar.so.1: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libsdformat9.so.9: cannot open shared object file: No such file or directory
gazebo: error while loading shared libraries: libignition-transport8.so.8: cannot open shared object file: No such file or dire
gazebo: error while loading shared libraries: libqwt-qt5.so.6.2: cannot open shared object file: No such file or directory
```

最终卡在
```shell
gazebo: symbol lookup error: /usr/lib64/libgazebo_transport.so.11: undefined symbol: _ZTIN3tbb4taskE
$ c++filt _ZTIN3tbb4taskE
typeinfo for tbb::task
$ ldd  /usr/lib64/libgazebo_transport.so.11|grep tbb
        libtbb.so.2 => /lib64/libtbb.so.2 (0x00007f82aaf74000)
```
询问老师得知系统版本应该使用OpenEuler 24.03不带sp1