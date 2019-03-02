因为我真的很想尝试一下自己定义一个新层，于是自己尝试了一下
---
### 太麻烦了!!!
----
>
>
来自官方的文档：https://github.com/BVLC/caffe/wiki/Development#developing-new-layers<br />
(为了防止翻车，自己最好备份一下caffe哦）<br />
## 1.首先修改caffe.proto
>
### (1)增加id
找到**message LayerParameter**，为自己的层添加一个id，记得是不能够跟已经有的重复的。
并且在<br />
```
// LayerParameter next available layer-specific ID: 148 (last added: euclidean_param)
```
这里写着可用的id和上一个添加的参数名称，可以按照这个来添加并且更新这条注释<br />
### (2)增加参数列表
```
message euclideanParameter {

  optional bool stable_prod_grad = 3 [default = true];
}
```
这是我添加的，但是这个参数没啥用，因为我压根用不到。
## 2.增加your_layer.hpp和your_layer.cpp
### (1)增加your_layer.hpp
注意hpp文件在include文件夹下<br />
我的hpp文件<br />
```C++
#ifndef CAFFE_euclidean_LAYER_HPP_
#define CAFFE_euclidean_LAYER_HPP_

#include <vector>

#include "caffe/blob.hpp"
#include "caffe/layer.hpp"
#include "caffe/proto/caffe.pb.h"

namespace caffe {

/**
 * @brief Compute elementwise operations, such as product and sum,
 *        along multiple input Blobs.
 *
 * TODO(dox): thorough documentation for Forward, Backward, and proto params.
 */
template <typename Dtype>
class euclideanLayer : public Layer<Dtype> {
 public:
  explicit euclideanLayer(const LayerParameter& param)
      : Layer<Dtype>(param) {}
	  
	  
  virtual void LayerSetUp(const vector<Blob<Dtype>*>& bottom,
      const vector<Blob<Dtype>*>& top);
  virtual void Reshape(const vector<Blob<Dtype>*>& bottom,
      const vector<Blob<Dtype>*>& top);
  virtual inline const char* type() const { return "euclidean"; }


 protected:
  virtual void Forward_cpu(const vector<Blob<Dtype>*>& bottom,
    const vector<Blob<Dtype>*>& top);
    
  virtual void Backward_cpu(const vector<Blob<Dtype>*>& top,
      const vector<bool>& propagate_down, const vector<Blob<Dtype>*>& bottom) {
    NOT_IMPLEMENTED;
  }

  bool stable_prod_grad_;
  Blob<Dtype> nums_buffer_;
  Blob<Dtype> nums_buffer_2;
  Blob<Dtype> nums_buffer_3;
};

}  // namespace caffe

#endif  // CAFFE_euclidean_LAYER_HPP_

```
这是我模仿其他文件写的一个计算欧氏距离的头文件。<br />
具体解释<br />
a.关于继承（https://blog.csdn.net/sinat_24143931/article/details/78684022）<br />
```
1. 直接继承于Layer 
2. 继承于DataLayer 
3. 继承于NeuronLayer 
4. 继承于LossLayer 
5. 或者如果是直接想改进某个层的代码，可以直接继承该类，并做相对应的修改即可。
```
### (2)修改your_layer.cpp
```
#include <cfloat>
#include <vector>

#include "caffe/layers/euclidean_layer.hpp"
#include "caffe/util/math_functions.hpp"

namespace caffe {

template <typename Dtype>
void euclideanLayer<Dtype>::LayerSetUp(
  const vector<Blob<Dtype>*>& bottom, const vector<Blob<Dtype>*>& top) {
}

template <typename Dtype>
void euclideanLayer<Dtype>::Reshape(
  const vector<Blob<Dtype>*>& bottom, const vector<Blob<Dtype>*>& top) {
	  
}


template <typename Dtype>
void euclideanLayer<Dtype>::Forward_cpu(const vector<Blob<Dtype>*>& bottom,
    const vector<Blob<Dtype>*>& top) {
  int count = bottom[0]->count();
  int count0 = 1;
  caffe_sub(
      count,
      bottom[0]->cpu_data(),
      bottom[1]->cpu_data(),
      nums_buffer_.mutable_cpu_data());
  Dtype dot = caffe_cpu_dot(count, nums_buffer_.cpu_data(), nums_buffer_.cpu_data());
  Dtype* dot_t = &dot;
  Dtype* top_data = top[0]->mutable_cpu_data();
  caffe_sqrt(count0, dot_t, top_data);
}

//#ifdef CPU_ONLY
//STUB_GPU(euclideanLayer);
//#endif

INSTANTIATE_CLASS(euclideanLayer);
REGISTER_LAYER_CLASS(euclidean);

}  // namespace caffe
```
