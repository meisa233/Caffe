在实验室的新服务器装caffe-C3D-v1.1**多GPU版**，整个就是一翻车现场……
>
前提，这台电脑上啥都没装，只装了显卡驱动<br />
>
**显卡驱动版本:418.56**<br />
**系统版本: Ubuntu 18.04**<br />
**显卡型号: RTX 2080 Ti** <br />
>
整体的安装流程参考：https://blog.csdn.net/qq473179304/article/details/79444609<br />
>
**安装步骤如下：**<br />
>
>
(1)安装依赖<br />
>
```
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler  
  
sudo apt-get install --no-install-recommends libboost-all-dev  
  
sudo apt-get install libopenblas-dev liblapack-dev libatlas-base-dev  
  
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev  
  
sudo apt-get install git cmake build-essential  
```
>
**请确保会安装成功，不然会影响后续的操作**<br />
>
(2)安装显卡驱动（因为已经装好了，这个步骤略过）<br />
>
(3)安装CUDA<br />
>
禁用nouveau驱动<br />
打开以下文件<br />
```
sudo vim /etc/modprobe.d/blacklist.conf
```

>
安装步骤来自：https://blog.csdn.net/qq997843911/article/details/85039021<br />
另外一个参考：https://blog.csdn.net/qq_32408773/article/details/84112166<br />
>
为了以后不出问题，选择采用**SSH连接的方式**进行安装，请务必确保网络状况良好<br />
>
CUDA下载地址：https://developer.nvidia.com/cuda-toolkit-archive <br />
>
这里安装的版本是CUDA 10.0（据说RTX 2080 Ti只支持CUDA10.0以上，当然也有说法说CUDA 9.2也可以，但是会出问题）<br />
>
选择的是：CUDA Toolkit 10.0 (Sept 2018), Online Documentation<br />
>
我们选择*runfile(local)* 
版本<br />
>
得到的文件名是sudo sh cuda_10.0.130_410.48_linux.run<br />
>
然后进行<br />
```
sudo sh cuda_10.0.130_410.48_linux.run
```
整体安装过程如下
```
Do you accept the previously read EULA?
accept/decline/quit: accept

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 410.48?
(y)es/(n)o/(q)nit: n

Install the CUDA 10.0 Toolkit?
(y)es/(n)o/(q)nit: y

Enter Toolkit Location
 [ default is /usr/local/cuda-10.0 ]:
 
Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)nit: y

Install the CUDA 10.0 Samples?
(y)es/(n)o/(q)uit: y

```
然后会出现很多需要选择的，全部选择Y，其他的的就是直接回车键默认。<br />
但是，注意，由于显卡驱动已经安装好了，所以在询问是否需要更新（或者安装）到最新的显卡驱动的时候，就不要选择安装了。<br/>
>
接下来需要进行环境配置<br />
>
```
sudo vi ~/.bashrc
# 添加以下三条语句到结尾
export CUDA_HOME=/usr/local/cuda-10.0
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64:$LD_LIBRARY_PATH # 这个是添加到结尾
export PATH=/usr/local/cuda-10.0/bin:$PATH # 这个是添加到结尾

# 以下是别人写的参数,仅供参考
# export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}
# export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}           
# export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

source ~/.bashrc
```
>
然后安装官方推荐的第三方库
```
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
```

(4)安装cuDNN<br />
下载地址：https://developer.nvidia.com/rdp/cudnn-archive <br />
(需要登录) <br />
这里安装的版本是7.4.1<br />
>
安装过程：<br />
解压后，进入cuda/include文件夹下<br />
```
sudo cp cudnn.h /usr/local/cuda/include/ #复制头文件  
```
然后进入cudnn/lib64文件夹下<br />
输入以下命令
```
sudo cp lib* /usr/local/cuda/lib64/ #复制动态链接库  
cd /usr/local/cuda/lib64/   
sudo rm -rf libcudnn.so libcudnn.so.7  #删除原有动态文件  
sudo ln -s libcudnn.so.7.0.5 libcudnn.so.7  #生成软衔接  
sudo ln -s libcudnn.so.7 libcudnn.so  #生成软链接  
```
随后需要将路径/usr/local/cuda/lib64 添加到动态库<br />
```
sudo vim /etc/ld.so.conf.d/cuda.conf  
```
在末尾添加
```
/usr/local/cuda/lib64  
```
接下来使用
```
sudo ldconfig
```
使命令生效<br />
>
>
测试一下安装是否正确<br />
命令如下：<br />
```
nvcc -V
```
如果能够正常显示cuda的版本就没问题了<br />
(5)安装OpenCV<br />
(6)安装NCCL<br />
安装过程来源：https://www.itread01.com/content/1548039254.html
```
git clone https://github.com/NVIDIA/nccl.git
cd nccl (看具体情况，也有可能是nccl-master）
sudo make CUDA_HOME=/usr/local/cuda-10.0/ test
sudo make install
sudo ldconfig
```
除此之外，需要修改Caffe的C3D-v1.1目录下的**Makefile.config**<br />
```
# USE_NCCL := 1
变为
USE_NCCL := 1
添加以下两条语句
INCLUDE_DIRS += /nccl的目录/build/include
LIBRARY_DIRS += /nccl的目录/build/lib
```

修改Caffe的C3D-v1.1的目录下的cmake目录下的Moduels下的FindNCCL.cmake文件<br />

```
set(NCCL_INC_PATHS
    /usr/include
/usr/local/include
/nccl的目录/build/include <-----------这条是要添加的语句
    $ENV{NCCL_DIR}/include
)

set(NCCL_LIB_PATHS
    /lib
    /lib64
    /usr/lib
    /usr/lib64
    /usr/local/lib
/usr/local/lib64
/nccl的目录/build/lib <-----------这条是要添加的语句
    $ENV{NCCL_DIR}/lib

    )
```

修改Cmakelists.txt文档
```
caffe_option(USE_NCCL "Build Caffe with NCCL library support" OFF)
OFF改成ON
```
(7)编译C3D-v1.1
-----------------------------------
补充：cmake安装https://blog.csdn.net/u014279330/article/details/83146586<br />
(问题来源：https://blog.csdn.net/mhsszm/article/details/90238119）
