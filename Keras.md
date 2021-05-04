---
title: Keras入门
date: 2020-06-03 19:00:48
categories: 
- tensorflow2
tags:
- tensorflow2
- Python3
- Keras
---

通过Keras，学习一个简单的机器学习模型的搭建过程

<!-- more -->

## 网络架构
 - **layer** 神经网络的核心组件是层，他是一种数据处理模块
 - **损失函数** 网络如何衡量在训练数据上的性能，即网络如何朝着正确的 方向前进
 - **优化器** 基于训练数据和损失函数来更新网络的机制
 - **在训练和测试过程中需要监控的指标（metric）** 本例只关心精度，即正确分类的图像所 占的比例
 - **展示网络模型** network.summary()
``` stylus
一些常用的网络模型
layers.Dense(32, activation='sigmoid')
layers.Dense(32, activation=tf.sigmoid)
layers.Dense(32, kernel_initializer='orthogonal')
layers.Dense(32, kernel_initializer=tf.keras.initializers.glorot_normal)
layers.Dense(32, kernel_regularizer=tf.keras.regularizers.l2(0.01))
layers.Dense(32, kernel_regularizer=tf.keras.regularizers.l1(0.01))
```
## 加载数据集

通过mnist.load_data(path)加载本地当前路径path目录下的数据集文件mnist.npz

## 代码示例
``` python
import tensorflow as tf
from tensorflow.keras.datasets import mnist
from tensorflow.keras import models
from tensorflow.keras import layers,optimizers
from tensorflow.keras.utils import to_categorical
from tensorflow.keras.models import load_model
h5file ='model_mnist.h5'
path = 'D:/MD/mnist/dataset/mnist.npz'
#加载数据集
(train_images,train_labels),(test_images,test_labels) = mnist.load_data(path)
#序贯模型
network = models.Sequential()
#输入层，使用relu激活函数
network.add(layers.Dense(512,activation='relu',input_shape=(28 * 28,)))
network.add(layers.Dense(512,activation='relu',input_shape=(28 * 28,)))
#输出层，
network.add(layers.Dense(10,activation='softmax'))
rmsprop =optimizers.RMSprop(lr=0.001, rho=0.9, epsilon=1e-08, decay=0.0)
#编译
network.compile(optimizer=rmsprop,
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
network.fit(train_images, train_labels, epochs=5, batch_size=256)
#评估模型
test_loss, test_acc = network.evaluate(test_images, test_labels)
print('test_acc:', test_acc)
#保存模型
network.save(h5file)
``` 

### 数据预处理
在开始训练之前，我们将对数据进行预处理，将其变换为网络要求的形状，并缩放到所有值都在[0,1]之间，比如：之前的训练图像保存在uint8类型的数组中，其形状为（60000，28，28），取值区间为[0,255]，将其变换为一个float32的数组，其形状为（60000，28，28），取值区间为[0,1]

### 编译
- **使用自定义优化器**
``` python3
from tensorflow.keras import optimizers
rmsprop =optimizers.RMSprop(lr=0.001, rho=0.9, epsilon=1e-08, decay=0.0)
network.compile(optimizer=rmsprop,
              loss='categorical_crossentropy',
              metrics=['accuracy'])
```

### 训练(拟合)
通过调用网络中的fit方法来完成，在训练过程中显示了2个数字，一个是模型在训练数据上的损失，另一个是模型在训练数据上的精度
### 评估模型
通过调用网络中的evaluate方法来完成，在训练完成后，检验模型在测试集上的性能
### 保存模型
需要说明的一点是Keras会把模型保存成“.h5”文件，为了让你的程序可以支持这种形式的文件你需要安装一下h5py这个package
 - `pip install h5py`
``` python3
from tensorflow.keras.models import load_model 
network.save('model_mnist.h5')
```

## 使用模型预测

``` python
import cv2
import os
import numpy as np
from tensorflow.keras.models import load_model
model = load_model('model_mnist.h5')  #选取自己的.h模型名称
model.summary()
#规范化图片大小和像素值
def get_inputs(src=[]):
    pre_x = []
    for s in src:
        input = cv2.imread(s)
        input = cv2.resize(input, (28 ,28))
        input = cv2.cvtColor(input, cv2.COLOR_BGR2GRAY)
        pre_x.append(input)  # input一张图片
    pre_x = np.array(pre_x) / 255.0
    return pre_x
#要预测的图片保存在这里
predict_dir = "D:/MD/mnist/dataset/pic/"
#这个路径下有两个文件，分别是cat和dog
test = os.listdir(predict_dir)
print(test)
#新建一个列表保存预测图片的地址
images = []
#获取每张图片的地址，并保存在列表images中
for testpath in test:
    fn = os.path.join(predict_dir, testpath)
    if fn.endswith("png"):        
        images.append(fn)
#调用函数，规范化图片
pre_x = get_inputs(images)
pre_x = pre_x.reshape((4, 28 * 28))
#预测
pre_y = model.predict(pre_x[:])
for item in pre_y:
    print(item) 
    print('Network prediction:', np.argmax([item]))
```

结果如下：

``` stylus
Model: "sequential"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense (Dense)                (None, 512)               401920    
_________________________________________________________________
dense_1 (Dense)              (None, 512)               262656    
_________________________________________________________________
dense_2 (Dense)              (None, 10)                5130      
=================================================================
Total params: 669,706
Trainable params: 669,706
Non-trainable params: 0
_________________________________________________________________
['1.png', '3.png', '7.png', '8.png']
[2.3097382e-09 9.9994242e-01 5.7707814e-07 1.2596505e-08 7.0986499e-07
 2.3295237e-09 5.8020344e-08 8.8017796e-06 4.7417845e-05 2.1094815e-08]
Network prediction: 1
[4.8323233e-08 4.5726656e-06 5.1644693e-06 9.9866831e-01 6.2075834e-08
 7.2616973e-04 2.5215357e-08 1.3051778e-05 2.0130505e-04 3.8116580e-04]
Network prediction: 3
[2.22959144e-07 1.51290782e-02 6.36672154e-02 5.00839087e-04
 1.79856386e-06 5.51942833e-07 1.08568745e-08 9.20564473e-01
 1.29454565e-04 6.25403709e-06]
Network prediction: 7
[2.9161657e-07 1.0341249e-06 1.3173140e-05 8.6986530e-01 8.3616067e-07
 2.0409422e-02 2.9305498e-08 2.1452777e-06 1.0675856e-01 2.9491049e-03]
Network prediction: 3
[Finished in 3.6s]
```

## 保存最佳模型

###  **EarlyStopping** ModelCheckpoint 
 如果监控的目标指标在设定的轮数内不再改善，可以用 EarlyStopping 回调函数来中断训练，比如，这个回调函数可以在刚开始过拟合的时候就中断训练，从而避免用更少的轮次重 新训练模型。这个回调函数通常与 ModelCheckpoint 结合使用，后者可以在训练过程中持续 不断地保存模型（你也可以选择只保存目前的最佳模型，即一轮结束后具有最佳性能的模型）。 
``` python
from tensorflow.keras.callbacks import ModelCheckpoint,EarlyStopping

callbacks_list = [       
EarlyStopping(monitor='acc',patience=1,),     
ModelCheckpoint(filepath=h5file,monitor='val_loss',save_best_only=True,) 
]

#编译
network.compile(optimizer='rmsprop',
                loss='categorical_crossentropy',
                metrics=['acc'])
                
network.fit(train_images, train_labels,           
	epochs=10,           
	batch_size=32,           
	callbacks=callbacks_list,           
	validation_data=(test_images, test_labels)) 

```

### **ReduceLROnPlateau** 
 如果验证损失不再改善，你可以使用这个回调函数来降低学习率。在训练过程中如果出现 了损失平台（loss plateau），那么增大或减小学习率都是跳出局部最小值的有效策略

``` python 
from tensorflow.keras.callbacks import ReduceLROnPlateau

callbacks_list = [       
ReduceLROnPlateau(monitor='val_loss',factor=0.1,patience=10,)
]

#编译
network.compile(optimizer='rmsprop',
                loss='categorical_crossentropy',
                metrics=['acc'])
                
network.fit(train_images, train_labels,           
	epochs=10,           
	batch_size=32,           
	callbacks=callbacks_list,           
	validation_data=(test_images, test_labels)) 
```
- **自定义回调函数**

## TensorBoard可视化

TensorBoard  是tensorflow提供的一个基于浏览器的可视化工具，在cmd种使用 `tensorboard --logdir= my_log_dir`启动，在浏览器中输入[http://localhost:6006][1]即可查看训练细节信息，TensorBoard 可视化包含如下内容：

- 在训练过程中以可视化的方式监控指标  
- 将模型架构可视化  
- 将激活和梯度的直方图可视化  
- 以三维的形式研究嵌入 

``` stylus
from tensorflow.keras.callbacks import TensorBoard
callbacks_list = [TensorBoard(log_dir='my_log_dir',histogram_freq=1,embeddings_freq=1,)]

network.fit(train_images, train_labels,           
	epochs=10,           
	batch_size=32,           
	callbacks=callbacks_list,           
	validation_data=(test_images, test_labels)) 
```

### 参考

 - python深度学习.pdf


  [1]: http://localhost:6006
