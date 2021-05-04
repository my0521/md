---
title: Keras详解 
date: 2020-06-02 19:00:48
categories: 
- tensorflow2
tags:
- tensorflow2
- Python3
- Keras
---
记录Keras中的所有模块及其功能说明
<!-- more -->
## 官方中文文档
[http://keras-cn.readthedocs.io/en/latest/][1]

## Keras常用模块

 -  **Input 、Model、Sequential**这三个模块是以前老的接口，新的版本已经将它们融合到后面的模块当中 
 -  **__builtins__ __doc__  __file__  __loader__ __name__ __path__ __spec__  _sys**  以'__'开头的模块是一些内嵌的模块 

 -  **activations** 主要负责为神经层附加激活函数，如linear、sigmoid、hard_sigmoid、tanh、softplus、relu、softmax、 softplus以及LeakyReLU等比较新的激活函数，详细说明：[http://keras.io/activations/][2]
 -  **applications** 是应用,这里面提供了已经训练好的keras模型，像图像识别的VGG等 
 -  **callbacks**是一个回调的抽象函数，在高级应用里面可以用来展示训练过程中网络内部的状态 
 -  **constraints**是一个约束项，主要是能够对神经网络进行约束，来防止神经网络的过拟合 
 -  **datasets** 里面包含了很多神经网络常用的数据集 
 -  **estimator**
 -  **experimental**
 -  **initializers**主要负责对模型参数（权重）进行初始化，初始化方法包括：uniform、lecun_uniform、normal、orthogonal、zero、glorot_normal、he_normal等
 详细说明：[http://keras.io/initializations/][3]
 -  **layers** 里面包含了keras已经实现的一些网络层，像全连接层Dense,卷积神经网络中的Conv 
 -  **losses**是目标函数，也就损失函数，代价函数等，包括像均方差误差，交叉熵等等，用来衡量神经网络训练过程中的训练的好坏，能够看在迭代的过程中神经网络的一个训练情况 
 -  **metrics**是评估函数，可以用来评估神经网络的性能，里面包括像准确度，召回率等
 -  **mixed_precision**
 -  **models**是模型库,Keras有两种类型的模型，序贯模型（Sequential）和函数式模型（Model），函数式模型应用更为广泛，序贯模型是函数式模型的一种特殊情况。序贯模型：使用序贯模型可以像搭积木一样一层一层地网上叠加神经网络 
 -  **preprocessing** 数据预处理模块
 -  **regularizers** 是正则化方法，是用来防止神经网络在训练过程中出现过拟合 
 -  **utils**工具模块，本模块提供了一系列有用工具，用于提供像数据转换，数据规范化等功能 
 -  **wrappers**包装器(层封装器)，能够将普通层进行包装，比如将普通数据封装成时序数据
 -  **optimizers** 是优化器，神经网络编译时必备的参数之一，可以用来在神经网络训练过程当中来更新权值的一个方法 
 
 


  [1]: http://keras-cn.readthedocs.io/en/latest/
  [2]: http://keras.io/activations/
  [3]: http://keras.io/initializations/
