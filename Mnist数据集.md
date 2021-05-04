---
title: mnist数据集
date: 2020-06-02 19:00:48
categories: 
- tensorflow2
tags:
- tensorflow2
- mnist
- 数字识别
---

mnist数据集的介绍及一个简单的模型
<!-- more -->

## 简介

MNIST是一个入门级的计算机视觉数据集，它包含各种手写数字图片。在机器学习中的地位相当于Python入门的打印Hello World。官网是[THE MNIST DATABASE of handwritten digits][1]

1. 数据集包含以下4部分：训练集(60000)和测试集(10000)

 - train-images-idx3-ubyte.gz
 - train-labels-idx1-ubyte.gz
 - t10k-images-idx3-ubyte.gz
 - t10k-labels-idx1-ubyte.gz
2. mnist.npz 数据集
[https://s3.amazonaws.com/img-datasets/mnist.npz][2]
 
## 图片和标签
 mnist数据集中每张图片的大小为28 * 28像素， 可以使用28 * 28的大小的数组来表示一张图片，标签用大小为10的数组来表示，这种编码称为独热编码 One hot
 
### 独热编码
 
 - 独热编码使用N位表示N种状态，但只有其中一位有效，如数字0-9: 
``` css 
[0,0,0,0,0,0,0,0,1,0]标识8
[0,0,1,0,0,0,0,0,0,0]标识2
```
 - numpy中有一个函数，numpy.argmax()可以取得最大值的下标

``` python
import numpy as np 

arr = [0,0,0,0,0,0,0,0,1,0]
print(np.argmax(arr))
```
8
 
 #### 优点 
 
 - 能够处理非连续型数值特征
 - 在一定程度上也扩充了特征
 - 具有很强的容错性
 
## mnist数字识别
 - keras 序贯模型加载mnist数据集，并评估、保存模型

``` python
import tensorflow as tf
from tensorflow.keras.datasets import mnist

from tensorflow.keras import models
from tensorflow.keras import layers
from tensorflow.keras import optimizers 

from tensorflow.keras.utils import to_categorical

from tensorflow.keras.models import load_model 

from tensorflow.keras.callbacks import ModelCheckpoint

h5file ='model_mnist.h5'

path = 'D:/MD/mnist/dataset/mnist.npz'
(train_images,train_labels),(test_images,test_labels) = mnist.load_data(path)

#序贯模型
network = models.Sequential()
#输入层，使用relu激活函数
network.add(layers.Dense(512,activation='relu',input_shape=(28 * 28,)))
network.add(layers.Dense(512,activation='relu',input_shape=(28 * 28,)))
#输出层，
network.add(layers.Dense(10,activation='softmax'))

#编译
network.compile(optimizer=optimizers.RMSprop(lr=0.001),
                loss='categorical_crossentropy',
                metrics=['accuracy'])

#准备图像数据
train_images = train_images.reshape((60000, 28 * 28))
train_images = train_images.astype('float32') / 255

test_images = test_images.reshape((10000, 28 * 28))
test_images = test_images.astype('float32') / 255

#准备标签
train_labels = to_categorical(train_labels)
test_labels = to_categorical(test_labels)

#拟合 训练
network.fit(train_images, train_labels, epochs=10, batch_size=256)
#评估模型
test_loss, test_acc = network.evaluate(test_images, test_labels)
print('test_acc:', test_acc)

#保存模型
network.save(h5file)
``` 

  [1]: http://yann.lecun.com/exdb/mnist/
  [2]: https://s3.amazonaws.com/img-datasets/mnist.npz
