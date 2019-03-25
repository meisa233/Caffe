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

python2.7装opencv（我在windows上用了这个命令装opencv，后续读取视频帧的时候，帧的索引出现了问题，<br />
有网友说是因为这样安装opencv是没有ffmpeg提供支持的才出现了这个问题，但是目前不知道怎么解决这个问题）<br />
相关讨论如下：<br />
https://github.com/opencv/opencv/issues/5686<br />
https://github.com/opencv/opencv/issues/9053<br />
https://github.com/Zulko/moviepy/issues/835<br />
https://github.com/opencv/opencv/pull/6899<br />
https://github.com/opencv/opencv/issues/4890<br />
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
**为了让python能够识别到我们编译好的opencv，我将OpenCV主目录/build/lib中的东西都复制到了/usr/local/lib/pyth2.7/site-packages/下，注意如果python已经安装了opencv需要使用pip uninstall opencv-python卸载掉**
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
3. 修改Makefile.config和Makefile
查看具体的文件如下：<br />
[Makefile](https://github.com/meisa233/Caffe/blob/master/Files%20about%20the%20installation%20of%20caffe/Makefile)<br />
>
[Makefile.config](https://github.com/meisa233/Caffe/blob/master/Files%20about%20the%20installation%20of%20caffe/Makefile.config)<br />
以下只列举重要的部分，因为全部的可能实在是记不清了!<br />
请自己查看文件，用diff -b -B 文件名1 文件名2做对比<br />
>
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
```
USE_CUDNN := 1（需要取消注释）
```
注意上述的这些目录的位置，不同的电脑是不一样的!!!一定要注意<br />
>
如果使用的OpenCV的版本大于等于3，则需要取消相关注释
```
OPENCV_VERSION := 3（去掉前面#）
```
>
去掉对compute_20的检查（注释掉）<br />
![comment_compute_20](https://github.com/meisa233/Caffe/raw/master/Files%20about%20the%20installation%20of%20caffe/caffe-gpu.png)<br />

**关于Makefile文件**
```
ifeq ($(LINUX), 1)
	CXX := /usr/bin/g++-5
endif
```
这个地方要改成g++-5<br />
>
修改python路径
```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib 
修改为： 
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```
请根据具体安装位置修改，上述修改并不代表所有系统的python路径都会是如此!
>
4. 安装
```
sudo make all -j8
```
-j8是8个线程的意思，也就是多线程编译<br />
5. 测试
```
sudo make runtest -j8
```
测试的过程可能会很漫长，尤其到了**Deconvolution3DLayerTest**这里<br />
请不要觉得奇怪，这是正常的!<br />

除此之外!!!Power层和Math_function也可能会出错，需要做一些修改<br />
修改来源：https://code.zackzhang.net/post/rcnn-installation-memo.html<br />
>
```
MathFunctionsTest 的问题通过更改源码可以解决，下面是要修改的地方。

对于文件 include/caffe/util/math_functions.hpp：

170 行，修改

// inline char caffe_sign(Dtype val)
inline int8_t caffe_sign(Dtype val)
删除 189-193 行的内容，即删除

#define INSTANTIATE_CAFFE_CPU_UNARY_FUNC(name) \
  template <> \
  void caffe_cpu_##name<float>(const int n, const float* x, float* y); \
  template <> \
  void caffe_cpu_##name<double>(const int n, const double* x, double* y)
218 行，修改

// DEFINE_CAFFE_CPU_UNARY_FUNC(sgnbit, y[i] = std::signbit(x[i]));
DEFINE_CAFFE_CPU_UNARY_FUNC(sgnbit, y[i] = static_cast<bool>((std::signbit)(x[i])));
对于文件 src/caffe/util/math_functions.cpp：

删除 448-450 行的内容，即删除
INSTANTIATE_CAFFE_CPU_UNARY_FUNC(sign);
INSTANTIATE_CAFFE_CPU_UNARY_FUNC(sgnbit);
INSTANTIATE_CAFFE_CPU_UNARY_FUNC(fabs);
```
>
```
对于文件 src/caffe/test/test_power_layer.cpp：

82 行，修改
// GradientChecker<Dtype> checker(1e-2, 1e-2, 1701, 0., 0.01);
GradientChecker<Dtype> checker(1e-3, 1e-2, 1701, 0., 0.01);
```
6. 编译pycaffe（如果需要）
```
sudo make pycaffe -j8
```
# C3D-v1.1的安装
1. 克隆源码（与C3D-v1.0的第一步相同）
```
git clone http://github.com/facebook/C3D.git
```
2.修改Makefile.config和Makefile
```
cp Makefile.config.example Makefile.config
```
**修改Makefie.config文件**
(1)取消OPENCV_VERSION := 3的注释
```
# Uncomment if you're using OpenCV 3
OPENCV_VERSION := 3
```
(2)确认CUDA_DIR的位置是否正确(不同系统是不一样的，请一定要到相关目录确认）
```
CUDA_DIR := /usr/lib/cuda
```
(3)注释CUDA_ARCH中arch=compute_20的命令行
(4)确认PYTHON_INCLUDE的目录是否正确
**修改Makefile文件**
在大概195行左右，将
```
	LIBRARIES += opencv_core opencv_highgui opencv_imgproc opencv_video
```
改为
```
	LIBRARIES += opencv_core opencv_highgui opencv_imgproc opencv_videoio
```
3. 安装
```
(在C3D-v1.1的根目录下）
mkdir build
cd build
cmake -DCUDNN_INCLUDE="/usr/lib/cuda/include" -DCUDNN_LIBRARY="/usr/lib/cuda/lib64/libcudnn.so" -DOpenCV_DIR="/media/user02/New Volume/OpenCV3.4.3/opencv-3.4.3/build" ..
sudo make all -j8
```
但是这样会出现一个bug，bug提示如下：<br />
```
[  FAILED  ] 2 tests, listed below:
[  FAILED  ] BatchReindexLayerTest/2.TestGradient, where TypeParam = Double
[  FAILED  ] BatchReindexLayerTest/3.TestGradient, where TypeParam = Float
```
相关讨论如下<br />
https://github.com/BVLC/caffe/issues/6164<br />
其实这个是由解决方案的，如下<br />
**修改Makefile**
```
...
# Debugging
ifeq ($(DEBUG), 1)
        COMMON_FLAGS += -DDEBUG -g -O0
        NVCCFLAGS += -G
else
        COMMON_FLAGS += -DNDEBUG -O2
endif
...
改为

...
# Debugging
ifeq ($(DEBUG), 1)
        COMMON_FLAGS += -DDEBUG -g -O0
        NVCCFLAGS += -G
else
        COMMON_FLAGS += -DNDEBUG -O2
        NVCCFLAGS += -G
endif
...
```
但是，我试过了，这样做了，重新sudo make clean在按照本文档233行至239行重新编译，还是会报错，然后我进行了一些如下的操作<br />
根据[commit](https://github.com/BVLC/caffe/commit/864520713a4c5ffae7382ced5d34e4cadc608473)来修改文件<br />
除此之外，还是要按照上面的操作加上NVCCFLAGS += -G<br />
然后在build文件夹下<br />
```
sudo make clean
```
然后删除build文件夹<br />
在C3D-v.1.1的根目录下<br />
```
sudo make all -j8
```
4. 测试
```
sudo make runtest -j8
```
5. 编译pycaffe（如果需要）
```
sudo make pycaffe -j8
```
