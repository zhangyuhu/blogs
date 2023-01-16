---
title: "opencv 核心函数整理"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [opencv]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

### 1.HighGUI模块做为高层GUI图形用户界面模块，包含媒体的输入输出、视频捕捉、图像和视频的编码解码、图像交互界面的接口等内容。

- imread：用于读取文件中的图片到opencv中
- imshow : 在指定的窗口中显示一幅图像
- namedWindow：用于创建一个窗口
- imwrite：输出图像到文件
- createTrackbar：用于创建一个可以调整数值的轨迹条
- getTrackbarPos：用于获取轨迹条的当前位置
- SetMouseCallback：为指定的窗口设置鼠标回调函数

### 2.opencv中的core组件是核心功能模块主要包含以下的内容：数据基本结构、动态数据结构、绘图函数、数组操作相关函数、辅助功能和系统函数和宏、与OpenGL的互操作。

- Mat::Mat() : Mat类的构造函数
- Mat::Create() : Mat类的成员函数，可用于Mat类的初始化操作
- Point : 用于表示点的数据结构
- Scalar : 用于表示颜色的数据结构
- Size : 用于表示尺寸的数据结构
- Rect : 用于表示矩阵的数据结构
- CvtColor() : 用于颜色的空间转换

### 3.操作图像中的像素、设置感兴趣区域（ROI）、进行图像混合、分离颜色通道、进行多通道图像混合、调整图像的对比对和亮度值、对图像进行离散傅里叶变换、输入输出XML 和YAML文件

- addWeighted : 计算两个数组（图像阵列）的加权和

- split : 将一个多通道数组分离成单个通道数组

- merge : 将多个数组组合合并成一个多通道的数组

- dft : 对一维或者二维浮点数数组进行正向或反向离散傅里叶变换

- getOptimalDFTSize : 返回给定向尺寸的傅里叶最优化尺寸大小

- copyMakeBorder : 扩充图像边界

- magnitude : 计算二维矢量的幅值

- log：计算每个数组元素绝对值的自然对数

- normalize：进行矩阵归一化

- FileStorage类：进行文件操作的类

### 4.imgproc组件是Image和Process这两个单词的缩写，即图像处理模块，这个模块有线性和非线性的图像滤波、图像的几何变换、其他（Miscellaneous）图像转换、直方图相关、结构分析和形状描述、运动分析和对象追踪、特征检测、目标检测等内容。

- boxFilter : 使用方框滤波来模糊一张图片
- blur : 对输入的图像进行均值滤波操作
- GaussianBlur : 用高斯滤波器来模糊一张图片
- medianBlur : 使用中值滤波器来模糊一张图片
- bilateralFilter : 使用双边滤波器来模糊处理一张图片
- dilate : 使用像素领域内的局部极大运算符来膨胀一张图片
- erode : 使用像素领域内的局部极小运算符来腐蚀一张图片
- morphologyEx : 利用基本的膨胀和腐蚀技术，来执行更加高级形态学变换，如开闭运算、形态学梯度、顶帽、黑帽等，也可以实现最基本的图像膨胀和腐蚀
- floodFill : 用指定的颜色从种子点开始填充一个连接域，实现漫水填充算法
- pyrUp : 向上采样并模糊一张图片，说白了就是放大一张图片
- pyrDown : 向下采样并模糊一张图片，说白了就是缩小一张图片
- Threshold : 对单通道数组应用固定阈值操作
- adaptiveThreshold : 对矩阵采用自适应阈值操作

### 5.图像变换（image transfrom）即将一幅图像转变成图像数据的另一种表现形式。变换最常见的例子就是傅里叶变换（Fourier transfrom），即将图像转换成源图像数据的另一种表示形式。这类操作的结果仍然保存为OpenCV图像结构的形式，但是新图像的每个单独像素表示原始输出图像的频谱分量，而不是通常所考虑的空间分量。基于OpenCV的边缘检测、霍夫变换、重映射、仿射变换、直方图均衡化。

- Canny : 利用canny算子来进行图像的边缘检测
- Sobel : 使用拓展的Sobel算子，来计算一阶、二阶、三阶或混合图像差分
- Laplacian : 计算出图像经过拉普拉斯变换后的结果
- Scharr : 使用Scharr滤波器运算符计算X或Y方向的图像差分
- HoughLinesP : 找出采用标准霍夫变换的二值图像线条
- HoughCireles : 采用累计概率霍夫变换（PPHT）来找出二值图像中的直线
- remap : 根据指定的映射形式，将源图像进行重映射几何变换
- warpAffine : 依据公式对图像做仿射变换
- getRotationMatrix2D : 计算二维旋转变换矩阵
- equalizeHist : 实现图像的直方图均衡化

### 6.图像轮廓与图像分割修复，虽然Canny之类的边缘检测算法可以根据像素之间的差异，检测出轮廓边界的像素，但是并没有将轮廓作为一个整体。所以下一步便是把这些边缘像素组装成轮廓。查找并绘制轮廓、寻找物体的凸包、使用多边形逼近物体、认识图像的矩、利用OpenCV进行图像修补。

- findContours : 在二值图像中寻找轮廓
- drawContours : 在图像中绘制外部或内部轮廓
- convexHull : 寻找图像点集中的凸包
- BoundingRect : 计算并返回指定点集最外面（up -right）的矩形边界
- minAreaRect : 寻找可旋转的最小面积的包围矩形
- minEnclosingCircle : 利用一种迭代算法，对给定的2D点集，寻找面积最小的可包围他们的圆形
- fitEllipse : 用椭圆拟合二维点集合 
- approxPolyDP：用指定精度逼近多边形曲线
- moments : 计算多边形和光栅形状的最高达三阶的所有矩
- contourArea : 计算整个轮廓或部分轮廓的面积
- arcLength : 计算封闭轮廓的周长或曲线的长度
- watershed : 实现分水岭算法
- inpaint : 进行图像修补，从扫描的照片中清除灰尘和划痕，或者从静态图像或视频中去除不需要的物体。

### 7.直方图和匹配，在进行物体图像和视频信息分析的过程中，我们常常会习惯于将眼中看到的物体用直方图（histogram）表示出来，得到比较直观的数据官感展示。直方图可以用来描述各种不同的参数和事物，如物体的彩色分布、物体边缘梯度模板、以及表示目标位置的当前假设的概率分布。

- calcHist : 计算一个或多个阵列的直方图
- minMaxLoc : 在数组中找到全局最小值和最大值
- compareHist : 对两幅直方图进行比较
- calcBackProject : 计算直方图反向投影
- mixChannels : 由输入参数复制某通道到输出参数特定的通道中
- matchTemplate : 匹配出和模板重叠的图像区域

### 8.feature2d组件，也就是Features 2D，是OpenCV的2D功能框架。自OpenCV以来的众多著名的特征检测算子（如SIFT、SURF、ORB算子等）所依赖的稳定版的特征检测与匹配相关的核心源代码已经从OpenCV3中移除了，转移到xfeature2d的第三方库当中了。特征检测器（Feature Detectors）通用接口、描述符提取器（Descriptor Extractors）通用接口、描述符匹配器（Descriptor Matchers）匹配器通用接口、关键点绘制函数和匹配功能绘制函数。

- cornerHarris : 运行Harris角点检测算子来进行角点检测
- goodFeaturesToTrack : 结合Shi-Tomasi 算子确定图像的强角点
- cornerSubPix: 寻找亚像素角点位置


### 9.特征点的检测和匹配是计算机视觉中非常重要的技术之一。在物体检测、视觉跟踪、三维重构等领域都有很广泛的应用。

- SURF类 、SurfFeatureDetector类 、SurfDescriptorExtractor类 ： 三者等价，同用与在OpenCV中进行SURF特征检测
- drawKeypoints函数 ：绘制关键点
- drawMatches函数 ：绘制出相匹配的两个图像的关键点
- KeyPoint类：用于表示特征点的信息
- BruteForceMatcher类：进行暴力匹配相关的操作
- FlannBasedMatcher类：实现FLANN特征匹配
- DescriptorMatcher::match函数：从每个描述符查询集中找到最佳匹配
- findHomography函数：找到并返回源图像和目标图像之间的透视变换H
- perspectiveTransfrom函数：进行向量透视矩阵变换
- ORB类、OrbFeatureDetector类、OrbDescriptorExtractor类：三者等价，同用于在OpenCV中进行ORB特征检测



参考：

[opencv核心函数名单](https://juejin.cn/post/6844904143337816078)