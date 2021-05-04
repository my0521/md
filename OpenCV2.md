---
title: OpenCV2 
date: 2020-05-06 18:49:48
categories: 
- 图像处理
tags:
- OpenCV2 
---
OpenCV2 读取显示图像，生成图像、以及图像修复

<!-- more -->
## 安装

``` pip
pip3 install opencv-python
```

## 使用

 - 导入  `import cv2`

### 读取并显示图像

``` python
import cv2 
path = 'D:/MD/mnist/dataset/cv/4.jpg'
img = cv2.imread(path)
cv2.namedWindow("Image")  
cv2.imshow("Image", img) 
cv2.waitKey (0) 
cv2.destroyAllWindows()#释放窗口 
```

### 生成新的图像

``` python
import cv2  
import numpy as np  
path = 'D:/MD/mnist/dataset/8.jpg'
img = cv2.imread(path)  
#通过np生成一个和img相同大小的
emptyImage = np.zeros(img.shape, np.uint8)  
  
emptyImage2 = img.copy()  
  
emptyImage3=cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)  
#emptyImage3[...]=0  
  
cv2.imshow("EmptyImage", emptyImage)  
cv2.imshow("Image", img)  
cv2.imshow("EmptyImage2", emptyImage2)  
cv2.imshow("EmptyImage3", emptyImage3)  

cv2.imwrite("./1.jpg", img, [int(cv2.IMWRITE_JPEG_QUALITY), 5])  
cv2.imwrite("./2.jpg", img, [int(cv2.IMWRITE_JPEG_QUALITY), 100])  
cv2.imwrite("./3.png", img, [int(cv2.IMWRITE_PNG_COMPRESSION), 0])  
cv2.imwrite("./4.png", img, [int(cv2.IMWRITE_PNG_COMPRESSION), 9])  
cv2.waitKey (0)  
cv2.destroyAllWindows()
```
### 图像修复
通过二值化处理，将指定范围的像素变成0，然后通过扩张待修复区域将图像复原

``` python
import cv2
import numpy as np
import os

# path = "inpaint.png"
predict_dir = 'D:/MD/mnist/dataset/cv/'
path =predict_dir + '4.jpg'

img = cv2.imread(path)
hight, width, depth = img.shape[0:3]

#图片二值化处理，把[240, 240, 240]~[255, 255, 255]以外的颜色变成0
thresh = cv2.inRange(img, np.array([230, 230, 230]), np.array([255, 255, 255]))

#创建形状和尺寸的结构元素
kernel = np.ones((3, 3), np.uint8)

#扩张待修复区域
hi_mask = cv2.dilate(thresh, kernel, iterations=1)
specular = cv2.inpaint(img, hi_mask, 5, flags=cv2.INPAINT_TELEA)

cv2.namedWindow("Image", 0)
cv2.resizeWindow("Image", int(width / 2), int(hight / 2))
cv2.imshow("Image", img)

cv2.namedWindow("newImage", 0)
cv2.resizeWindow("newImage", int(width / 2), int(hight / 2))
cv2.imshow("newImage", specular)
cv2.waitKey(0)
cv2.destroyAllWindows()
```




