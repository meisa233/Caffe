标题：暹罗网络教程<br />
描述：在MNIST数据上训练和测试暹罗网络。<br />
类别：示例<br />
include_in_docs：true<br />
布局：默认<br />
优先级：100<br />

使用Caffe进行暹罗网络培训<br />
---------------------------------------
这个Example显示了如何使用权重共享（sharing）和对比度损失函数（contrastive loss function）在Caffe中使用暹罗网络学习模型。

我们假设你已经成功编译了caffe。 如果没有，请参阅安装页面。 这个例子建立在MNIST教程智商，所以请在继续之前阅读它。

*指南指定所有路径并假设所有命令都从根caffe目录执行*
----------------------------------------
切换到caffe根目录下<br />
运行get_mnist.sh文件<br />
```Linux
bash ./data/mnist/get_mnist/sh
```
由于运行create_mnist_siamese.sh出现问题，所以需要修改