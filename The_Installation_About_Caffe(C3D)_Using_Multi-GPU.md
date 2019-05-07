在实验室的新服务器装caffe-C3D-v1.1多GPU版，整个就是一翻车现场……
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
(2)安装CUDA<br />
>
安装步骤来自：https://blog.csdn.net/w55100/article/details/88929029<br />
>
为了以后不出问题，选择采用SSH连接的方式进行安装，请务必确保网络状况良好<br />

