# 这是一个关于C3D的安装说明(v1.0和v1.1)--整理版
# 准备工作：OpenCV的安装
这里以**3.4.3**版本为例<br />
除此之外，系统为Ubuntu 18.04<br />
(租的服务器就是这个版本，我也没办法了）<br />
>
安装方法来自：https://blog.csdn.net/qq473179304/article/details/79444609<br />
>
官网地址：http://opencv.org/releases.html(需要到官网下载opencv的源码包)
>
首先安装依赖<br />
```
sudo apt-get install libgtk2.0-dev pkg-config
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libavresample-dev
```
>
```
(切换到opencv根目录下）
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..  
（PS：如果cmake命令不存在，使用sudo apt-get install cmake安装cmake）
sudo make -j8
```
没有任何问题之后进行安装
```
sudo make install
```
查看OpenCV的命令如下
```
pkg-config --modversion opencv
```
(注意查看的一般是电脑默认的OpenCV版本，如果你并没有安装在默认目录下，这个命令是无效的）

python2.7装opencv（我在windows上用了这个命令装opencv，后续读取视频帧的时候，帧的索引出现了问题，有网友说是因为这样安装opencv是没有ffmpeg提供支持的<br />
因而出现了这个问题，但是目前不知道怎么解决这个问题）<br />
(相关讨论如下：<br />
https://github.com/opencv/opencv/issues/5686<br />
https://github.com/opencv/opencv/issues/9053<br />
https://github.com/Zulko/moviepy/issues/835<br />
https://github.com/opencv/opencv/pull/6899<br />
https://github.com/opencv/opencv/issues/4890<br />
）
```
sudo pip install opencv-python
```
**在执行命令之前请务必注意:**<br />
```
 FFMPEG:Yes
 avcodec:Yes
 avformat:Yes
 avutil:Yes
 swscale:Yes
```
这五项必须是**Yes**<br />
>
出现问题可以尝试安装FFMPEG(不推荐，以往安装过程中按照如上过程是没有问题的）
```
sudo add-apt-repository ppa:djcj/hybrid
sudo apt-get update
sudo apt-get install ffmpeg
```
>
除此之外，当一个版本实在装不上，也请不要气馁，可以选择其他版本试试看。<br />
>
在以往的尝试中，我无法安装OpenCV 3.4.0版本，但是我成功安装了OpenCV 3.4.3版本。<br />
>
以下列举一些可能会出现的问题<br />
1. 报错：error -- unsupported GNU version!
具体如下
```
In file included from /opt/cuda/include/host_config.h:50:0,
                 from /opt/cuda/include/cuda_runtime.h:78,
                 from <command-line>:0:
/opt/cuda/include/crt/host_config.h:121:2: error: #error -- unsupported GNU version! gcc versions later than 6 are not supported!
 #error -- unsupported GNU version! gcc versions later than 6 are not supported!
  ^~~~~
```
解决方法：在编译时命令加上-DCMAKE_C_COMPILER=gcc-6 -DCMAKE_CXX_COMPILER=g++-6 ..
即：
```
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_C_COMPILER=gcc-6 -DCMAKE_CXX_COMPILER=g++-6 WITH_FFMPEG=YES ..
```
# C3D-v1.0的安装
1. 克隆源码
```
git clone http://github.com/facebook/C3D.git
```
2. 安装g++-5<br />
（我已经尝试了很多g++的版本，只有5可以）
```
sudo apt-get install g++-5
```
3. 修改Makefile.config
首先得到Makefile.config文件<br />
切换到C3D-v1.0目录下
```
cp Makefile.config.example Makefile.config
```
在Makefile.config中有几个需要主要的地方<br />
```
PYTHON_INCLUDE := /usr/include/python2.7 \
		/usr/local/lib/python2.7/dist-packages/numpy/core/include
```
```
PYTHON_LIB := /usr/lib
```
```
CUDA_DIR := /usr/lib/cuda
```
注意上述的这些目录的位置，不同的电脑是不一样的!!!一定要注意<br />
>
关于Makefile文件
```
ifeq ($(LINUX), 1)
	CXX := /usr/bin/g++-5
endif
```
这个地方要改成g++-5<br />
