---
title: "opencv 常用函数"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [opencv]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [资料](#资料)
- [convertTo / cvtColor](#convertto--cvtcolor)
  - [convertTo](#convertto)
  - [cvtColor](#cvtcolor)
- [setTo](#setto)
- [直方图均衡化](#直方图均衡化)
- [均值滤波blur](#均值滤波blur)
- [边缘检测Sobel](#边缘检测sobel)
- [OpenCV笛卡尔坐标转极坐标cartToPolar()](#opencv笛卡尔坐标转极坐标carttopolar)

<!-- /TOC -->

# 资料
[gitbook](https://shawnzhang31.com/)  
[learn opencv](https://www.learnopencv.com/)  
[OpenCV:知乎](https://zhuanlan.zhihu.com/lowkeyway-OpenCV)  


# convertTo / cvtColor
不是所有格式的Mat型数据都能被使用保存为图片，目前OpenCV主要只支持单通道和3通道的图像，并且此时要求其深度为8bit和16bit无符号(即CV_16U)，所以其他一些数据类型是不支持的，比如说float型等。  
如果Mat类型数据的深度和通道数不满足上面的要求，则需要使用convertTo()函数和cvtColor()函数来进行转换。  
convertTo()函数负责转换数据类型不同的Mat，即可以将类似float型的Mat转换到imwrite()函数能够接受的类型。  
cvtColor()函数是负责转换不同通道的Mat，因为该函数的第4个参数就可以设置目的Mat数据的通道数（只是我们一般没有用到它，一般情况下这个函数是用来进行色彩空间转换的）。  

## convertTo
图像的对比度和亮度的调整 : dst = alpha * src + beta （alpha控制对比度，beta控制亮度）
```c++
//! converts matrix to another datatype with optional scalng. See cvConvertScale.
void convertTo( OutputArray m, int rtype, double alpha=1, double beta=0 ) const;


cv::Mat samples;
cv::Mat tdata;
samples.convertTo(tdata, CV_32FC1);

convertTo(depth_vis, CV_8UC1, 1.0 / 255, 0);
```
    缩放并转换到另外一种数据类型：
    dst：目的矩阵
    type：需要的输出矩阵类型，或者更明确的，是输出矩阵的深度，如果是负值（常用-1）则输出矩阵和输入矩阵类型相同
    scale和shift：缩放参数，也可以写为alpha和beta
    这个命令也等价于下面的转换公式：
    m(x,y) = saturate_cast(α(*this)(x,y)+β)
    
    如果scale=1，shift=0，则不进行比例缩放。  
    如果输入数组与输出数组的类型相同，则函数可以被用于缩放和平移矩阵或图像；  

## cvtColor
```c++
cv.cvtColor(src, code[, dst[, dstCn]]

void cv::cvtColor	(	InputArray 	src,
                         OutputArray 	dst,
                       int 	code,
                      int 	dstCn = 0 
                      )	

//RGB图，转换成灰度图
cv.cvtColor(img, cv.COLOR_BGR2GRAY)
//灰度图，转换成RGB图
cv::cvtColor(depth_vis, frame->vis_data, CV_GRAY2BGR);
```
cvtColor RGB转成其他格式时，需要注意RGB图像的三个通道的顺序，是RGB还是BGR.大部分情况下opencv为RGB,但实际上是BGR,这一点需要注意。  
需要注意各个值的范围：  

    CV_8U图像 其通道值范围为0到255
    CV_16U时其值通道值范围为0到65535
    CV_32F时，其通道值范围为0到1

在线性转换时，其范围没有较大关系，但是如果是非线性转换，RGB图像需要做正确的归一化，以扩展到响应的范围。例如TGB->L*U*V转换，如果32位的浮点图像之间是由8位的图像转换而来，那么将0到255的范围转换成0,1，那么转换之前首先需要需要所需其图像。
```c++
img *= 1./255;
cvtColor(img, img, COLOR_BGR2Luv);
```


# setTo
```c++
  /** @brief Sets all or some of the array elements to the specified value.
    This is an advanced variant of the Mat::operator=(const Scalar& s) operator.
    @param value Assigned scalar converted to the actual array type.
    @param mask Operation mask of the same size as \*this. Its non-zero elements indicate which matrix
    elements need to be copied. The mask has to be of type CV_8U and can have 1 or multiple channels
     */
   Mat& setTo(InputArray value, InputArray mask=noArray()); 
```
说明：
1、功能：把矩阵mask中元素不为0的点全部变为value值；  
2、当默认不添加mask的时候，表明mask是一个与原图尺寸大小一致的且元素值全为非0的矩阵，因此不加mask的时候，会将原矩阵的像素值全部赋值为value；

```c++
Mat src(3, 3, CV_8UC1);
Mat mask(3, 3, CV_8UC1, Scalar(0));
src.setTo(100, mask);
cout << src << endl; 
```
    [  0,   0,   0;
    0,   0,   0;
    0,   0,   0]

```c++
Mat src(3, 3, CV_8UC1);
Mat mask(3, 3, CV_8UC1, Scalar(5));
src.setTo(100, mask);
cout << src << endl;
```

    [100, 100, 100;
    100, 100, 100;
    100, 100, 100]

# 直方图均衡化





# 均值滤波blur

均值滤波是一种典型的线性滤波算法，主要是利用像素点邻域的像素值来计算像素点的值。其具体方法是首先给出一个滤波模板kernel，该模板将覆盖像素点周围的其他邻域像素点，去掉像素本身，将其邻域像素点相加然后取平均值即为该像素点的新的像素值，这就是均值滤波的本质。

opencv提供了blur函数实现均值滤波操作，其原型如下：

```c++
void blur(InputArray src, OutputArray dst, Size ksize, Point anchor=Point(-1,-1), int borderType=BORDER_DEFAULT )
```


参数解释：

> . InputArray src: 输入图像，可以是Mat类型，图像深度是CV_8U、CV_16U、CV_16S、CV_32F以及CV_64F其中的某一个。
> . OutputArray dst: 输出图像，深度和类型与输入图像一致
> . Size ksize: 滤波模板kernel的尺寸，一般使用Size(w, h)来指定，如Size(3,3)
> . Point anchor=Point(-1, -1): 字面意思是锚点，也就是处理的像素位于kernel的什么位置，默认值为(-1, -1)即位于kernel中心点，如果没有特殊需要则不需要更改
> . int borderType=BORDER_DEFAULT: 用于推断图像外部像素的某种边界模式，有默认值BORDER_DEFAULT



# 边缘检测Sobel

检测边缘:以灰度图像为例，它的理论基础是这样的:如果出现一个边缘，那么图像的灰度就会有一定的变化。为了方便假设由黑渐变为白代表一个边界，那么对其灰度分析，在边缘的灰度函数就是一个一次函数y=kx，对其求一阶导数就是其斜率k，就是说边缘的一阶导数是一个常数，而由于非边缘的一阶导数为零，这样通过求一阶导数就能初步判断图像的边缘了。通常是X方向和Y方向的导数，也就是梯度。计算机就是通过这种方式来获得图像的边缘。

```c++
 void  cvSobel( const CvArr* src, CvArr* dst, int xorder, int yorder, intaperture_size=3 );
```

  >src：输入图像；
  >dst：输出图像；
   xorder：x方向上的差分阶数；
  yorder：y方向上的差分阶数；
  aperture_size扩展Sobel核的大小（既窗口阶数），必须是1（注意这是一个3×1或1×3向量而不是一个方阵）,3, 5 或7。



# 笛卡尔坐标转极坐标cartToPolar

直角坐标系转为极坐标系。

```c++
void cv::cartToPolar(
    cv::InputArray x,
    cv::InputArray y,
    cv::OutputArray magnitude,
    cv::OutputArray angle,
    bool angleInDegrees = false
);
```



# OpenCV中的阈值(threshold)函数

OpenCV中提供了阈值化函数threshold，该函数原型如下：

```cpp
double threshold(InputArray src,
                 OutputArray dst,
                 double thresh,
                 double maxval,
                 int type);
```

参数说明：

src ： 表示输入图像

dst : 表示输出图像（尺寸和类型和输入图像一样）

thresh : 表示阈值

maxval : 表示预设最大值

type : 表示阈值化处理的类型设置（type类型有一下几种）

- `THRESH_BINARY`表示大于`thresh`的取`maxval`,否则取0;
- `THRESH_BINARY_INV`表示大于`thresh`的取0,否则取`maxvalue`;
- `THRESH_TRUNC`表示大于`thresh`取`threshold`,否则不改变灰度值;
- `THRESH_TOZERO`表示大于`thresh`的不改变灰度值,否则取0;
- `THRESH_TOZERO_INV`表示大于`thresh`取0,窦泽不改变灰度值;
- `THRESH_OTSU`表示使用`otsu`自动计算阈值;
- `THRESH_TRIANGLE`表示使用`Triangle`自动计算阈值;

[基本的阈值操作](http://www.opencv.org.cn/opencvdoc/2.3.2/html/doc/tutorials/imgproc/threshold/threshold.html)

[OpenCV基础——threshold函数的使用](https://blog.csdn.net/u012566751/article/details/77046445/)



