---
title: "opencv Mat 操作"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [opencv]
categories: [开源软件]
---

<!-- TOC -->

- [Mat](#mat)
- [成员变量](#成员变量)
- [Mat 通道理解](#mat-通道理解)
  - [通道](#通道)
  - [图像类型的转换与显示](#图像类型的转换与显示)
  - [通道的合成与分解](#通道的合成与分解)
- [创建一个Mat](#创建一个mat)
  - [Scalar](#scalar)
  - [cv::Mat::ones](#cvmatones)
  - [cv::Mat::zeros](#cvmatzeros)
  - [cv::Mat::eye](#cvmateye)
- [Mat类中数据部分组成](#mat类中数据部分组成)
- [参考](#参考)

<!-- /TOC -->

# Mat
The class Mat represents an n-dimensional dense numerical single-channel or multi-channel array. It can be used to store real or complex-valued vectors and matrices, grayscale or color images, voxel volumes, vector fields, point clouds, tensors, histograms (though, very high-dimensional histograms may be better stored in a SparseMat ).  
上面的一段话引用自官方的文档，Mat类用于表示一个多维的单通道或者多通道的稠密数组。能够用来保存实数或复数的向量、矩阵，灰度或彩色图像，立体元素，点云，张量以及直方图（高维的直方图使用SparseMat保存比较好）。简而言之，Mat就是用来保存多维的矩阵的。  
# 成员变量
* data:  uchar型的指针。Mat类分为了两个部分:矩阵头和指向矩阵数据部分的指针，data就是指向矩阵数据的指针。

* dims:  矩阵的维度，例如5*6矩阵是二维矩阵，则dims=2，三维矩阵dims=3.

* rows:  矩阵的行数

* cols:  矩阵的列数

* size:  矩阵的大小，size(cols,rows),如果矩阵的维数大于2，则是size(-1,-1)

* channels:  矩阵元素拥有的通道数，例如常见的彩色图像，每一个像素由BGR三部分组成，则channels = 3。

* type:  表示了矩阵中元素的类型以及矩阵的通道个数，它是一系列的预定义的常量，其命名规则为CV_(位数）+（数据类型）+（通道数）。这里U（unsigned integer）表示的是无符号整数，S（signed integer）是有符号整数，F（float）是浮点数。  

例如：CV_16UC2，表示的是元素类型是一个16位的无符号整数，通道为2. C1，C2，C3，C4则表示通道是1,2,3,4。type一般是在创建Mat对象时设定，如果要取得Mat的元素类型，则无需使用type，使用下面的depth
<div align=center><img src="../image/v2-9824973e7d9cd7cee0e1b45dd3d5b926_r.jpg"/></div>  

* depth  :矩阵中元素的一个通道的数据类型，这个值和type是相关的。

例如 type为 CV_16SC2，一个2通道的16位的有符号整数。那么，depth则是CV_16S。depth也是一系列的预定义值，将type的预定义值去掉通道信息就是depth值:CV_8U CV_8S CV_16U CV_16S CV_32S CV_32F CV_64F

* elemSize  :矩阵一个元素占用的字节数，例如：type是CV_16SC3，那么elemSize = 3 * 16 / 8 = 6 bytes

* elemSize1:矩阵元素一个通道占用的字节数，例如：type是CV_16CS3，那么elemSize1 = 16 / 8 = 2 bytes = elemSize / channels  



# Mat 通道理解
## 通道

一个图像的通道数是N，就表明每个像素点处有N个数，一个a×b的N通道图像，其图像矩阵实际上是b行N×a列的数字矩阵。

OpenCV中图像的通道可以是1、2、3和4。其中常见的是1通道和3通道，2通道和4通道不常见。

> 1通道的是灰度图。
> 3通道的是彩色图像，比如RGB图像。
> 4通道的图像是RGBA，是RGB加上一个A通道，也叫alpha通道，表示透明度。PNG图像是一种典型的4通道图像。alpha通道可以赋值0到1，或者0到255，表示透明到不透明。
> 2通道的图像是RGB555和RGB565。2通道图在程序处理中会用到，如傅里叶变换，可能会用到，一个通道为实数，一个通道为虚数，主要是编程方便。RGB555是16位的，2个字节，5+6+5，第一字节的前5位是R，后三位+第二字节是G，第二字节后5位是B，可见对原图像进行压缩了。

## 图像类型的转换与显示 

```c++
Mat image=imread("../images/RGB.jpg");  
Mat imageGRAY,imageRGBA,imageRGB555;
cvtColor(image,imageGRAY,CV_RGB2GRAY);            //RGB转GRAY
cvtColor(image,imageRGBA,CV_RGB2BGRA);            //RGB转RGBA
cvtColor(image,imageRGB555,CV_RGB2BGR555);        //RGB转RGB555

//来看看通道数
int n = image.channels();                        //n=3
int nGRAY = imageGRAY.channels();                //nGRAY = 1
int nRGBA = imageRGBA.channels();                //nRGBA = 4
int nRGB555 = imageRGB555.channels();            //nRGB555 = 2

cout << "n: " << n << "  nGRAY: " << nGRAY << "  nRGBA: " << nRGBA << "  nRGB555: " << nRGB555 << endl;

//显示GRAY、RGB和RGBA图像
imshow("image",image);
imshow("imageGRAY",imageGRAY);
imshow("imageRGBA",imageRGBA);
//imshow("imageRGB555",imageRGB555);             //无法显示
```

![image-20210422142025781](../image/RGB.png)



RGB转GRAY是根据一个心理学公式来的：Gray = R*0.299 + G*0.587 + B*0.114

RGB转GRBA，默认A通道的数值是255，也就是不透明的。



## 通道的合成与分解

**分解**

```c++
C++: void split(const Mat& mtx, Mat* mv)
C++: void split(const Mat& mtx, vector<Mat>& mv)
```
> 参数：mtx   输入矩阵
>            mv    输出矩阵或矩阵数组  
>            src   输入矩阵  
>           dst0、dst1、dst2、dst3   最多4个单通道的输出矩阵  

```c++
void Mat_split(void)
{
     Mat rgb(3, 4, CV_8UC3, Scalar(1, 2, 3, 4)); //这里 由于类型是 CV_8UC3 Scalar 第四个值没有用

     vector<Mat> channels;
     split(rgb, channels);
     Mat R = channels.at(0); //从vector中读数据用vector::at()
     Mat G = channels.at(1);
     Mat B = channels.at(2);

     cout << "RGB=" << endl
          << rgb << endl;

     cout << "R=" << endl
          << R << endl;

     cout << "G=" << endl
          << G << endl;

     cout << "B=" << endl
          << B << endl;
}
```

```
RGB=
[  1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3;
   1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3;
   1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3]
R=
[  1,   1,   1,   1;
   1,   1,   1,   1;
   1,   1,   1,   1]
G=
[  2,   2,   2,   2;
   2,   2,   2,   2;
   2,   2,   2,   2]
B=
[  3,   3,   3,   3;
   3,   3,   3,   3;
   3,   3,   3,   3]
```

**合成**

```c++
C++: void merge(const Mat* mv, size_t count, OutputArray dst)
C++: void merge(const vector<Mat>& mv, OutputArray dst)
```
> 参数：mv    输入矩阵
>            count   当mv是C形式的array时，count表示输入矩阵个数  
>           dst   输出矩阵  
>           src0、src1、src2、src3   最多4个单通道的输入矩阵  

```c++
void Mat_merge(void)
{
     Mat R(3, 4, CV_8UC1, Scalar(3));
     Mat G(3, 4, CV_8UC1, Scalar(2));
     Mat B(3, 4, CV_8UC1, Scalar(1));
     Mat RGB(3, 4, CV_8UC3);
     vector<Mat> src;
     src.push_back(R); //往vector里存数据要用vector::push_back()
     src.push_back(G);
     src.push_back(B);
     merge(src, RGB);

     cout << "R=" << endl
          << R << endl;

     cout << "G=" << endl
          << G << endl;

     cout << "B=" << endl
          << B << endl;

     cout << "RGB=" << endl
          << RGB << endl;
}
```

```
R=
[  3,   3,   3,   3;
   3,   3,   3,   3;
   3,   3,   3,   3]
G=
[  2,   2,   2,   2;
   2,   2,   2,   2;
   2,   2,   2,   2]
B=
[  1,   1,   1,   1;
   1,   1,   1,   1;
   1,   1,   1,   1]
RGB=
[  3,   2,   1,   3,   2,   1,   3,   2,   1,   3,   2,   1;
   3,   2,   1,   3,   2,   1,   3,   2,   1,   3,   2,   1;
   3,   2,   1,   3,   2,   1,   3,   2,   1,   3,   2,   1]
```

**split( )和merge( )都是mixChannels( )的特例**

```c++
C++: void mixChannels(const Mat* src, int nsrc, Mat* dst, int ndst, const int* fromTo, size_t npairs)
C++: void mixChannels(const vector<Mat>& src, vector<Mat>& dst, const int* fromTo, int npairs)
```

> 参数：src  输入的矩阵，可以是一个矩阵也可以是多个矩阵构成的vector  
>            nsrc   输入矩阵的个数  
>            dst   输出矩阵，可以是一个矩阵也可以是多个矩阵构成的vector  
>            ndst   输出矩阵的个数  
>            fromTo   src到dst通道对应数组     
>            npairs    fromTo中有几组对应关系  

mixChannels( )本质是改变了几个通道的顺序，输入一共有几个通道，输出肯定也有几个通道，所以定义fromTo时，要知道有多少个通道，而且通道的编号一定是0,1,2，...


```c++
void Mat_mixchannels(void)
{
     Mat RGB(3, 4, CV_8UC3, Scalar(1, 2, 3, 4));
     Mat A(3, 4, CV_8UC1, Scalar(6));
     cout << "RGB=" << endl
          << RGB << endl;
     cout << "A=" << endl
          << A << endl;

     //RGB+A合成为RGBA
     cout << "RGB+A合成为RGBA" << endl;
     Mat RGBA(3, 4, CV_8UC4);
     Mat in[] = {RGB, A};
     int fromTo1[] = {0, 0, 1, 1, 2, 2, 3, 3};
     mixChannels(in, 2, &RGBA, 1, fromTo1, 4);
     cout << "RGBA=" << endl
          << RGBA << endl;

     //RGB分解为R+GB
     cout << "RGB分解为R+GB" << endl;
     Mat R(3, 4, CV_8UC1);
     Mat GB(3, 4, CV_8UC2);
     Mat out[] = {R, GB};
     int fromTo2[] = {0, 2, 1, 1, 2, 0};
     mixChannels(&RGB, 1, out, 2, fromTo2, 3);
     cout << "R=" << endl
          << R << endl;
     cout << "GB=" << endl
          << GB << endl;
}
```

```
RGB=
[  1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3;
   1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3;
   1,   2,   3,   1,   2,   3,   1,   2,   3,   1,   2,   3]
A=
[  6,   6,   6,   6;
   6,   6,   6,   6;
   6,   6,   6,   6]
RGB+A合成为RGBA
RGBA=
[  1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6;
   1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6;
   1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6,   1,   2,   3,   6]
RGB分解为R+GB
R=
[  3,   3,   3,   3;
   3,   3,   3,   3;
   3,   3,   3,   3]
GB=
[  2,   1,   2,   1,   2,   1,   2,   1;
   2,   1,   2,   1,   2,   1,   2,   1;
   2,   1,   2,   1,   2,   1,   2,   1]
```



# 创建一个Mat

```c++
void Mat_demo()
{
    cv::Mat M(2, 2, CV_8UC3, Scalar(0, 0, 255));
    cout << M << endl;

    cv::Mat Z = cv::Mat::zeros(3, 3, CV_64F);
    cout << Z << endl;

    cv::Mat F = cv::Mat::ones(2, 2, CV_64F);
    cout << F << endl;

    cv::Mat M0(1,2,CV_8UC3);
    cout << M0 << endl;

    float K[3][3] = {1, 0, 1, 0, 1, 1, 0, 0, 1};
    cv::Mat M1 = cv::Mat(3,3,CV_32FC1,K);
    cout << M1 << endl;

    cv::Mat M2 = (cv::Mat_<float>(3,3) << 0,-1,0,-1,5,-1,0,-1,0);
    cout << M2 << endl;

    cv::Mat M3 = (Mat_<double>(3, 3) << 0, 1, 2, 1, 2, 3, 2, 3, 4);
    cout << M3 << endl;
}
```
## Scalar

 Scalar是一个由长度为4的数组作为元素构成的结构体，Scalar最多可以存储四个值，没有提供的值默认是0。

```c
  typedef struct Scalar
  {
      double val[4];
 }Scalar;
```

```c++
// 创建一个2通道，且每个通道的值都为（1,3），深度为32，4行4列的图像矩阵。
Mat M(4, 4, CV_32FC2, Scalar(1, 3));

/*
[1, 3, 1, 3, 1, 3, 1, 3;
 1, 3, 1, 3, 1, 3, 1, 3;
 1, 3, 1, 3, 1, 3, 1, 3;
 1, 3, 1, 3, 1, 3, 1, 3]
 */
```

```c++
// 创建一个3通道，且每个通道的值都为（1,2,3），深度为32，4行4列的图像矩阵。
Mat M(4, 4, CV_32FC3, Scalar(1, 2, 3));

/*
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3;
 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3;
 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3;
 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3]
*/
```

```c++
// 创建一个4通道，且每个通道的值都为（1,2,3,0）(默认为0)，深度为32，4行4列的图像矩阵。
Mat M(4, 4, CV_32FC4, Scalar(1, 2, 3));

/*
[1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0;
 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0;
 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0;
 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3, 0]
*/
```

```c++
Mat blue_m(256, 256, CV_8UC3, Scalar(255, 0, 0));
//cout<<blue_m<<endl;
Mat green_m(256, 256, CV_8UC3, Scalar(0, 255, 0));
//cout<<green_m<<endl;
Mat red_m(256, 256, CV_8UC3, Scalar(0, 0, 255));
//cout<<red_m<<endl;
Mat previewImg(256, 256, CV_8UC3);
//CV_EXPORTS void hconcat(InputArray src1, InputArray src2, OutputArray dst);
hconcat(blue_m, green_m, previewImg);
hconcat(previewImg, red_m, previewImg);
imshow("previewImg", previewImg);
waitKey(0);
```

![image-20210422142025781](../image/image-20210422142025781.png)


## cv::Mat::ones

```c++
Mat m = Mat::ones(2, 2, CV_8UC3); 相当于：Mat m = Mat(2, 2, CV_8UC3, 1);
```

// OpenCV replaces 1 with Scalar(1,0,0)相当于每个像素的第一个通道为1，其余两个通道为0；

```c++
cv::Mat F = cv::Mat::ones(3, 3, CV_64F);
cout << F << endl;
/*
[1, 1, 1;
 1, 1, 1;
 1, 1, 1]
*/
```

## cv::Mat::zeros

Mat m = Mat::zeros(2, 2, CV_8UC3);
//相当于创建一张黑色的图，每个像素的每个通道都为0,`Scalar(0,0,0)`；

```c++
cv::Mat Z = cv::Mat::zeros(3, 3, CV_64F);
cout << Z << endl;
/*
[0, 0, 0;
 0, 0, 0;
 0, 0, 0]
*/
```

## cv::Mat::eye

```c++
cv::Mat Y = cv::Mat::eye(3, 3, CV_64F);
cout << Y << endl;
/*
[1, 0, 0;
 0, 1, 0;
 0, 0, 1]
*/
```



# Mat类中数据部分组成

* 矩阵头：包括矩阵尺寸、存储方法、存储地址等。  
* 指针：该指针指向存储所有像素值的矩阵。  
因为图片的数据量比较大，所以OpenCV中的Mat定义都是只申请了矩阵头和指针，尽量避免对指针指向的内容进行拷贝操作。  
```c++
void Mat_demo1()
{
    Mat A, C; // Identify header only
    cout << "sizeof(A): " << sizeof(A) << "; sizeof(C): " << sizeof(C) << endl;
    cout << "A:\n"
         << A << endl;
    A = Mat::ones(Size(3, 3), CV_8UC1);
    C = A;
    cout << "sizeof(A): " << sizeof(A) << "; sizeof(C): " << sizeof(C) << endl;
    cout << "A:\n"
         << A << endl;

    Mat B(A);
    cout << "B:\n"
         << B << endl;
    B.at<uchar>(1, 1) = 255;
    cout << "C:\n"
         << C << endl;

    Mat D = A.clone();
    cout << "D:\n"
         << D << endl;
    D.at<uchar>(0, 0) = 123;
    cout << "A:\n"
         << A << endl;
    cout << "D:\n"
         << D << endl;

    Mat E;
    A.copyTo(E);
    cout << "E:\n"
         << E << endl;
    E.at<uchar>(2, 2) = 100;
    cout << "A:\n"
         << A << endl;
    cout << "E:\n"
         << E << endl;
}
```
Mat类型的变量定义以及赋值分为：
* 公用一个数据块
```
=
构造函数
```
* 不公用一个数据块
```
clone
copyto
```





# 参考

[OpenCV CV_RGB2GRAY与CV_BGR2GRAY的区别](https://blog.csdn.net/weixin_42142612/article/details/80804039)

[学习OpenCV2——Mat之通道的理解](https://blog.csdn.net/GDFSG/article/details/50927257)