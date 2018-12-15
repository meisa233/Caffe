于是这个C3D的坑是不得不迈出去了<br />
参考：https://blog.csdn.net/kmyfoer/article/details/80060696
## 安装过程
### !请先看error2的解决方案（需要装ffmpeg并重装opencv3.4.0)
### 1.克隆源码
```
git clone http://github.com/facebook/C3D.git
```
### 2.修改配置文件
#### (1) 创建Makefile.config文件
```
cp Makefile.config.example Makefile.config
```
#### (2) 如果使用的opencv的版本大于3，需要在Makefile.config中修改相应设置
```
OPENCV_VERSION := 3（去掉前面#）
```
#### (3) 去掉对compute_20的检查
![compute_20](https://github.com/meisa233/Caffe/blob/master/Files%20about%20the%20installation%20of%20caffe/caffe-gpu.png)<br />
**将红框圈出来的地方注释掉**
#### (4）修改Makefile中的python路径
```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib 
修改为： 
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```
### 2.编译和安装
#### (1) 编译
```
make all -j8
```
#### (2) 测试
```
sudo make runtest -j8
```
#### (3) 编译pycaffe
```
sudo make pycaffe -j8
```
##### 可能会出现问题
```
python/caffe/_caffe.cpp:10:31: fatal error: numpy/arrayobject.h: 没有那个文件或目录
```
解决方法：由于不知道是什么东西造成的
所以重装了一下python-numpy
```
sudo apt-get remove python-numpy
sudo apt-get install python-numpy
```
### error1:undefined reference to `cv::VideoCapture::VideoCapture()'
解决方案：修改Makefile文件
将<br />
```
LIBRARIES += opencv_core opencv_highgui opencv_imgproc opencv_video
```
修改为
```
LIBRARIES += opencv_core opencv_highgui opencv_imgproc opencv_videoio
```
### error2:在测试VideoDataLayerTest/0.TestReadAvi出现错误
```
Unable to stop the stream: Inappropriate ioctl for device
```
查了资料之后，心态崩了，要重新安装Opencv(这里是3.4.0)<br />
解决方法如下：<br />
https://github.com/facebook/C3D/issues/231 <br />
卸载opencv的方法如下（来自https://www.cnblogs.com/txg198955/p/5990295.html）<br />
```
$ sudo make uninstall
$ cd ..
$ sudo rm -r build
$ sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv*

```
重新安装有ffmpeg的opencv<br />
方法:<br />
先安装ffmpeg(方法来自https://blog.csdn.net/lwgkzl/article/details/77836207 ）<br />
```
sudo add-apt-repository ppa:djcj/hybrid
sudo apt-get update
sudo apt-get install ffmpeg
```
然后安装支持ffmpeg的opencv<br />
### 20181215更新<br />
今天重新配置环境，因为想要直接装支持ffmpeg的opencv,于是打算按照上面三行命令来<br />
然而**不行啊**<br />
一开始
```
FFMPEG:No
 avcodec:No
 avformat:No
 avutil:No
 swscale:No
```
后来看了一些网页，安装了依赖<br />
```
sudo apt-get install libgtk2.0-dev pkg-config
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
```
但是**FFMPEG仍然是No**
于是我把**FFMPEG给卸了**
然后就。。 就成功了 ！！！
<br />
注意，如果失败了的话要
```
sudo make clean
```
重新来<br />
---------------------------
### 新问题：
```
In file included from /opt/cuda/include/host_config.h:50:0,
                 from /opt/cuda/include/cuda_runtime.h:78,
                 from <command-line>:0:
/opt/cuda/include/crt/host_config.h:121:2: error: #error -- unsupported GNU version! gcc versions later than 6 are not supported!
 #error -- unsupported GNU version! gcc versions later than 6 are not supported!
  ^~~~~
 ```
 解决方法，编译时的命令改成：
 ```
 cmake -DCMAKE_C_COMPILER=gcc-6 -DCMAKE_CXX_COMPILER=g++-6 ..
 ```
即<br />
```
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_C_COMPILER=gcc-6 -DCMAKE_CXX_COMPILER=g++-6 WITH_FFMPEG=YES ..

```
在opencv下新建一个文件夹build<br />
```
cd build  
  
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local WITH_FFMPEG=YES..  
#如果cmake命令不存在，用sudo apt-get install cmake来安装cmake  
make -j8  #编译  
```
编译完成后，安装
```
sudo make install #安装  
```
安装完成后通过查看opencv版本验证是否安装成功：
```
pkg-config --modversion opencv 
```
