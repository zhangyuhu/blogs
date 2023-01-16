---
title: "opencv 常用算子"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [opencv]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [常用到的算子](#常用到的算子)
    - [1. cv::abs()](#1-cvabs)
    - [2. cv::add()](#2-cvadd)
    - [3. cv::addWeighted()](#3-cvaddweighted)
    - [4. cv::bitwise_and()](#4-cvbitwise_and)
    - [5. cv::bitwise_not()](#5-cvbitwise_not)
    - [6. cv::bitwise_or()](#6-cvbitwise_or)
    - [7. cv::bitwise_xor()](#7-cvbitwise_xor)
    - [8. cv::calcCovarMatrix()](#8-cvcalccovarmatrix)
    - [9. cv::cartToPolar()](#9-cvcarttopolar)
    - [10. cv::checkRange()](#10-cvcheckrange)
    - [11. cv::compare()](#11-cvcompare)
    - [12. cv::completeSymm()](#12-cvcompletesymm)
    - [13. cv::convertScaleAbs()](#13-cvconvertscaleabs)
    - [14. cv::countNonZero()](#14-cvcountnonzero)
    - [15. cv::cvarrToMat()](#15-cvcvarrtomat)
    - [16. cv::dct()](#16-cvdct)
    - [17. cv::dft()](#17-cvdft)
    - [18. cv::cvtColor()](#18-cvcvtcolor)
    - [19. cv::determinant()](#19-cvdeterminant)
    - [20. cv::divide()](#20-cvdivide)
    - [21. cv::eigen()](#21-cveigen)
    - [22. cv::exp()](#22-cvexp)
    - [23. cv::flip()](#23-cvflip)
    - [24. cv::gemm()](#24-cvgemm)
    - [25. cv::idct()](#25-cvidct)
    - [26. cv::idft()](#26-cvidft)
    - [27. cv::inRange()](#27-cvinrange)
    - [28. cv::invert()](#28-cvinvert)
    - [29. cv::log()](#29-cvlog)
    - [30. cv::magnitude()](#30-cvmagnitude)
    - [31. cv::Mahalanobis()](#31-cvmahalanobis)
    - [32. cv::max()](#32-cvmax)
    - [33. cv::min()](#33-cvmin)
    - [34. cv::mean()](#34-cvmean)
    - [35. cv::meanStdDev()](#35-cvmeanstddev)
    - [36. cv::merge()](#36-cvmerge)
    - [37. cv::split()](#37-cvsplit)
    - [38. cv::minMaxIdx()](#38-cvminmaxidx)
    - [39. cv::minMaxLoc()](#39-cvminmaxloc)
    - [40. cv::mixChannels()](#40-cvmixchannels)
    - [41. cv::multiply()](#41-cvmultiply)
    - [42. cv::mulTransposed()](#42-cvmultransposed)
    - [43. cv::norm()](#43-cvnorm)
    - [44. cv::normalize()](#44-cvnormalize)
    - [45. cv::perspectiveTransform()](#45-cvperspectivetransform)
    - [46. cv::phase()](#46-cvphase)
    - [47. cv::polarToCart()](#47-cvpolartocart)
    - [48. cv::pow()](#48-cvpow)
    - [49. cv::randu()](#49-cvrandu)
    - [50. cv::randn()](#50-cvrandn)
    - [51. cv::repeat()](#51-cvrepeat)
    - [52. cv::scaleAdd()](#52-cvscaleadd)
    - [53. cv::setIdentity()](#53-cvsetidentity)
    - [54. cv::solve()](#54-cvsolve)
    - [55. cv::solveCubic()](#55-cvsolvecubic)
    - [56. cv::solvePoly()](#56-cvsolvepoly)
    - [57. cv::sort()](#57-cvsort)
    - [58. cv::sortIdx()](#58-cvsortidx)
    - [59. cv::sqrt()](#59-cvsqrt)
    - [60 cv::subtract()](#60-cvsubtract)
    - [61. cv::sum()](#61-cvsum)
    - [62. cv::trace()](#62-cvtrace)
    - [63. cv::transform()](#63-cvtransform)
    - [64. cv::transpose()](#64-cvtranspose)

<!-- /TOC -->


# 常用到的算子

## 1. cv::abs()

计算矩阵的绝对值

```cpp
cv::MatExpr cv::abs( cv::InputArray src );
cv::MatExpr cv::abs( const cv::MatExpr& src ); // Matrix expression
```

## 2. cv::add()

计算矩阵src1和src2的加和，并把结果存放在dst中。

```cpp
void cv::add(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
    int dtype = -1 // Output type for result array
);
```

## 3. cv::addWeighted()

该函数和上一个add()函数比较类似，是根据下面公式将结果写入dst中。可用于图像混合。

```cpp
void cv::addWeighted(
    cv::InputArray src1, // First input array
    double alpha, // Weight for first input array
    cv::InputArray src2, // Second input array
    double beta, // Weight for second input array
    double gamma, // Offset added to weighted sum
    cv::OutputArray dst, // Result array
    int dtype = -1 // Output type for result array
);
```

## 4. cv::bitwise_and()

逐元素按位与操作。

```cpp
void cv::bitwise_and(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 5. cv::bitwise_not()

逐元素按位取反操作。

```cpp
void cv::bitwise_not(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 6. cv::bitwise_or()

逐元素按位取或操作。

```cpp
void cv::bitwise_or(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 7. cv::bitwise_xor()

逐元素按位异或操作。

```cpp
void cv::bitwise_xor(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 8. cv::calcCovarMatrix()

给定一些向量，假设向量表示的点是近似的高斯分布，，那么该函数将计算这些点的均值和协方差矩阵。

```cpp
void cv::calcCovarMatrix(
    const cv::Mat* samples, // C-array of n-by-1 or 1-by-n matrices
    int nsamples, // num matrices pointed to by 'samples'
    cv::Mat& covar, // ref to return array for covariance
    cv::Mat& mean, // ref to return array for mean
    int flags, // special variations, see Table bellow.
    int ctype = cv::F64 // output matrix type for covar
);
void cv::calcCovarMatrix(
    cv::InputArray samples, // n-by-m matrix, but see 'flags' below
    cv::Mat& covar, // ref to return array for covariance
    cv::Mat& mean, // ref to return array for mean
    int flags, // special variations.
    int ctype = cv::F64 // output matrix type for covar
);
```

该函数有两种调用方式，如上codes。

## 9. cv::cartToPolar()

直角坐标系转为极坐标系。

```cpp
void cv::cartToPolar(
    cv::InputArray x,
    cv::InputArray y,
    cv::OutputArray magnitude,
    cv::OutputArray angle,
    bool angleInDegrees = false
);
```

## 10. cv::checkRange()

检查src中每个元素是否在给定的范围之内。范围有minVal和maxVal设置。除非将quiet设置为true，否则抛出异常。如果都在范围内，则返回true，否则将返回false，并将第一个异常值的位置存放在pos中。

```cpp
bool cv::checkRange(
    cv::InputArray src,
    bool quiet = true,
    cv::Point* pos = 0, // if non-Null, location of first outlier
    double minVal = -DBL_MAX, // Lower check bound (inclusive)
    double maxVal = DBL_MAX // Upper check bound (exclusive)
);
```

## 11. cv::compare()

逐元素比较src1和src2，并将比较结果放在dst中,匹配标记为255，否则设置为0。

```cpp
bool cv::compare(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    int cmpop // Comparison operator.
);
```

## 12. cv::completeSymm()

将矩阵mtx通过复制使其对称，默认情况下将上三角的所有元素都被复制到下三角对应转置的位置上，对角元素不变。如果lowerToUpper = true，那么则将下三角复制到上三角位置。

```cpp
bool cv::completeSymm(
    cv::InputArray mtx,
    bool lowerToUpper = false
);
```

## 13. cv::convertScaleAbs()

该函数按照如下公式进行转换原图像。实现对原图像像素值的缩放和平移。

```cpp
void cv::convertScaleAbs(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    double alpha = 1.0, // Multiplicative scale factor
    double beta = 0.0 // Additive offset factor
);
```

## 14. cv::countNonZero()

返回矩阵mtx中非零元素的个数。

```cpp
int cv::countNonZero( // Return number of nonzero elements in mtx
    cv::InputArray mtx, // Input array
);
```

## 15. cv::cvarrToMat()

将旧版本的图像格式转为Mat类图像。

```cpp
cv::Mat cv::cvarrToMat(
    const CvArr* src, // Input array: CvMat, IplImage, or CvMatND
    bool copyData = false, // if false just make new header, else copy data
    bool allowND = true, // if true and possible, convert CvMatND to Mat
    int coiMode = 0 // if 0: error if COI set, if 1: ignore COI
);
```

## 16. cv::dct()

根据flags执行离散余弦变换或者离散余弦逆变换。

```cpp
void cv::dct(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    int flags, // for inverse transform or row-by-row
);
```

## 17. cv::dft()

实现离散傅里叶变换及其逆变换。

```cpp
void cv::dft(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    int flags = 0, // for inverse transform or row-by-row
    int nonzeroRows = 0 // only this many entries are nonzero
);
```

## 18. cv::cvtColor()

保留相同数据类型的同时将图像从一个颜色空间转换到另一个颜色空间。

```cpp
void cv::cvtColor(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    int code, // color mapping code, see Table 5-4.
    int dstCn = 0 // channels in output (0='automatic')
);
```

## 19. cv::determinant()

计算一个方阵的行列式。

```cpp
double cv::determinant(
    cv::InputArray mat
);
```

## 20. cv::divide()

将src1或scale中的元素除以src2中相应的元素，然后将结果放在dst中。

```cpp
void cv::divide(
    cv::InputArray src1, // First input array (numerators)
    cv::InputArray src2, // Second input array (denominators)
    cv::OutputArray dst, // Results array (scale*src1/src2)
    double scale = 1.0, // Multiplicative scale factor
    int dtype = -1 // dst data type, -1 to get from src2
);
void cv::divide(
    double scale, // Numerator for all divisions
    cv::InputArray src2, // Input array (denominators)
    cv::OutputArray dst, // Results array (scale/src2)
    int dtype = -1 // dst data type, -1 to get from src2
);
```

## 21. cv::eigen()

计算矩阵的特征向量和特征值。

```cpp
bool cv::eigen(
    cv::InputArray src,
    cv::OutputArray eigenvalues,
    int lowindex = -1,
    int highindex = -1
);
bool cv::eigen(
    cv::InputArray src,
    cv::OutputArray eigenvalues,
    cv::OutputArray eigenvectors,
    int lowindex = -1,
    int highindex = -1
);   
```

## 22. cv::exp()

计算src中所有元素的指数，并将结果存放在dst中。

```cpp
void cv::exp(
    cv::InputArray src,
    cv::OutputArray dst
);
```

## 23. cv::flip()

绕x轴或者y轴旋转。

```cpp
void cv::flip(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array, size and type of 'src'
    int flipCode = 0 // >0: y-flip, 0: x-flip, <0: both
);
```

## 24. cv::gemm()

广义矩阵乘法（GEMM）。

```cpp
void cv::gemm(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    double alpha, // Weight for 'src1' * 'src2' product
    cv::InputArray src3, // Third (offset) input array
    double beta, // Weight for 'src3' array
    cv::OutputArray dst, // Results array
    int flags = 0 // Use to transpose source arrays
);
```

## 25. cv::idct()

离散余弦逆变换，相当于调用带参数的dct()。

```cpp
void cv::idct(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    int flags, // for row-by-row
);
```

## 26. cv::idft()

离散傅里叶逆变换，相当于调用带参数的dft()。

```cpp
void cv::idft(
    cv::InputArray src, // Input array
    cv::OutputArray dst, // Result array
    int flags = 0, // for row-by-row, etc.
    int nonzeroRows = 0 // only this many entries are nonzero
);
```

## 27. cv::inRange()

判断src中的元素是否在upperb和lowerb之间，满足置为255，否则0。

```cpp
void cv::inRange(
    cv::InputArray src, // Input array
    cv::InputArray upperb, // Array of upper bounds (inclusive)
    cv::InputArray lowerb, // Array of lower bounds (inclusive)
    cv::OutputArray dst // Result array, cv::U8C1 type
);
```

## 28. cv::invert()

求矩阵的逆。

```cpp
double cv::invert( // Return 0 if 'src' is singular
    cv::InputArray src, // Input Array, m-by-n
    cv::OutputArray dst // Result array, n-by-m
    int method = cv::DECOMP_LU // Method for (pseudo) inverse
);
```

## 29. cv::log()

求原图像的自然对数。

```cpp
void cv::log(
    cv::InputArray src,
    cv::OutputArray dst
);
```

## 30. cv::magnitude()

计算直角坐标系转换成极坐标系的幅值。

```cpp
void cv::magnitude(
    cv::InputArray x,
    cv::InputArray y,
    cv::OutputArray dst
);
```

## 31. cv::Mahalanobis()

计算两个向量的马氏距离。

```cpp
cv::Size cv::mahalanobis(
    cv::InputArray vec1,
    cv::InputArray vec2,
    cv::OutputArray icovar
);
```

## 32. cv::max()

计算矩阵src1和src2中每个对应的像素对的最大值。

```cpp
cv::MatExpr cv::max(
    const cv::Mat& src1, // First input array (first position)
    const cv::Mat& src2 // Second input array
);
MatExpr cv::max( // A matrix expression, not a matrix
    const cv::Mat& src1, // First input array (first position)
    double value // Scalar in second position
);
MatExpr cv::max( // A matrix expression, not a matrix
    double value, // Scalar in first position
    const cv::Mat& src1 // Input array (second position)
);
void cv::max(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst // Result array
);
void cv::max(
    const Mat& src1, // First input array
    const Mat& src2, // Second input array
    Mat& dst // Result array
);
void cv::max(
    const Mat& src1, // Input array
    double value, // Scalar input
    Mat& dst // Result array
);
```

## 33. cv::min()

计算矩阵src1和src2中每个对应的像素对的最小值。

```cpp
cv::MatExpr cv::min( // A matrix expression, not a matrix
    const cv::Mat& src1, // First input array
    const cv::Mat& src2 // Second input array
);
MatExpr cv::min( // A matrix expression, not a matrix
    const cv::Mat& src1, // First input array (first position)
    double value // Scalar in second position
);
MatExpr cv::min( // A matrix expression, not a matrix
    double value, // Scalar in first position
    const cv::Mat& src1 // Input array (second position)
);
void cv::min(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst // Result array
);
void cv::min(
    const Mat& src1, // First input array
    const Mat& src2, // Second input array
    Mat& dst // Result array
);
void cv::min(
    const Mat& src1, // Input array
    double value, // Scalar input
    Mat& dst // Result array
);
```

## 34. cv::mean()

计算src矩阵的均值。

```cpp
cv::Scalar cv::mean(
    cv::InputArray src,
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 35. cv::meanStdDev()

计算矩阵的均值和标准差。

```cpp
void cv::meanStdDev(
    cv::InputArray src,
    cv::OutputArray mean,
    cv::OutputArray stddev,
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
);
```

## 36. cv::merge()

将多个二维矩阵合并成一个多维矩阵。

```cpp
void cv::merge(
    const cv::Mat* mv, // C-style array of arrays
    size_t count, // Number of arrays pointed to by 'mv'
    cv::OutputArray dst // Contains all channels in 'mv'
);
void merge(
    const vector<cv::Mat>& mv, // STL-style array of arrays
    cv::OutputArray dst // Contains all channels in 'mv'
);
```

## 37. cv::split()

将多通道矩阵拆分成多个单通道矩阵。

```cpp
void cv::split(
    const cv::Mat& mtx,
    cv::Mat* mv
);
void cv::split(
    const cv::Mat& mtx,
    vector<Mat>& mv // STL-style vector of n 1-channel cv::Mat's
);
```

## 38. cv::minMaxIdx()

计算单通道矩阵的最大最小值，并返回其位置。

```cpp
void cv::minMaxIdx(
    cv::InputArray src, // Input array, single channel only
    double* minVal, // min value goes here (in not NULL)
    double* maxVal, // min value goes here (in not NULL)
    int* minIdx, // loc of min goes here (if not NULL)
    int* maxIdx, // loc of max goes here (if not NULL)
    cv::InputArray mask = cv::noArray() // search only nonzero values
);
void cv::minMaxIdx(
    const cv::SparseMat& src, // Input sparse array
    double* minVal, // min value goes here (in not NULL)
    double* maxVal, // min value goes here (in not NULL)
    int* minIdx, // C-style array, indices of min locs
    int* maxIdx, // C-style array, indices of max locs
);
```

## 39. cv::minMaxLoc()

计算矩阵的最大最小值，并返回其位置。

```cpp
void cv::minMaxLoc(
    cv::InputArray src, // Input array
    double* minVal, // min value goes here (in not NULL)
    double* maxVal, // min value goes here (in not NULL)
    cv::Point* minLoc, // loc of min goes here (if not NULL)
    cv::Point* maxLoc, // loc of max goes here (if not NULL)
    cv::InputArray mask = cv::noArray() // search only nonzero values
);
void cv::minMaxLoc(
    const cv::SparseMat& src, // Input sparse array
    double* minVal, // min value goes here (in not NULL)
    double* maxVal, // min value goes here (in not NULL)
    cv::Point* minLoc, // C-style array, indices of min locs
    cv::Point* maxLoc, // C-style array, indices of max locs
);
```

## 40. cv::mixChannels()

对于输入图像的通道按照给定的顺序重新组合。

```cpp
void cv::mixChannels(
    const cv::Mat* srcv, // C-style array of matrices
    int nsrc, // Number of elements in 'srcv'
    cv::Mat* dstv, // C-style array of target matrices
    int ndst, // Number of elements in 'dstv'
    const int* fromTo, // C-style array of pairs, ...from,to...
    size_t n_pairs // Number of pairs in 'fromTo'
);
void cv::mixChannels(
    const vector<cv::Mat>& srcv, // STL-style vector of matrices
    vector<cv::Mat>& dstv, // STL-style vector of target matrices
    const int* fromTo, // C-style array of pairs, ...from,to...
    size_t n_pairs // Number of pairs in 'fromTo'
);
```

## 41. cv::multiply()

矩阵对应元素相乘。

```cpp
void cv::multiply(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    double scale = 1.0, // overall scale factor
    int dtype = -1 // Output type for result array
);
```

## 42. cv::mulTransposed()

矩阵和其转置的乘积。

```cpp
void cv::mulTransposed(
    cv::InputArray src1, // Input matrix
    cv::OutputArray dst, // Result array
    bool aTa, // If true, transpose then multiply
    cv::InputArray delta = cv::noArray(), // subtract from 'src1' before multiply
    double scale = 1.0, // overall scale factor
    int dtype = -1 // Output type for result array
);
```

## 43. cv::norm()

计算一个矩阵的范数，或者如果提供两个矩阵，该函数也可以计算两个矩阵间的各种距离范数。

```cpp
double cv::norm( // Return norm in double precision
    cv::InputArray src1, // Input matrix
    int normType = cv::NORM_L2, // Type of norm to compute
    cv::InputArray mask = cv::noArray() // do for nonzero values (if present)
);
double cv::norm( // Return computed norm of difference
    cv::InputArray src1, // Input matrix
    cv::InputArray src2, // Second input matrix
    int normType = cv::NORM_L2, // Type of norm to compute
    cv::InputArray mask = cv::noArray() // do for nonzero values (if present)
);
double cv::norm(
    const cv::SparseMat& src, // Input sparse matrix
    int normType = cv::NORM_L2, // Type of norm to compute
);
```

## 44. cv::normalize()

将原图像映射到一个特定范围内并放到dst中。

```cpp
void cv::normalize(
    cv::InputArray src1, // Input matrix
    cv::OutputArray dst, // Result matrix
    double alpha = 1, // first parameter (see Table 5-8)
    double beta = 0, // second parameter (see Table 5-8)
    int normType = cv::NORM_L2, // Type of norm to compute
    int dtype = -1 // Output type for result array
    cv::InputArray mask = cv::noArray() // do for nonzero values (if present)
);
void cv::normalize(
    const cv::SparseMat& src, // Input sparse matrix
    cv::SparseMat& dst, // Result sparse matrix
    double alpha = 1, // first parameter (see Table 5-8)
    int normType = cv::NORM_L2, // Type of norm to compute
);
```

## 45. cv::perspectiveTransform()

执行一系列点的平面投影变换。

```cpp
void cv::perspectiveTransform(
    cv::InputArray src, // Input array, 2 or 3 channels
    cv::OutputArray dst, // Result array, size, type, as src1
    cv::InputArray mtx // 3-by-3 or 4-by-4 transoform matrix
);
```

## 46. cv::phase()

对二维矢量场计算笛卡尔-极坐标转换的方位角（角度）部分。

```cpp
void cv::phase(
    cv::InputArray x, // Input array of x-components
    cv::InputArray y, // Input array of y-components
    cv::OutputArray dst, // Output array of angles (radians)
    bool angleInDegrees = false // degrees (if true), radians (if false)
);
```

## 47. cv::polarToCart()

从向量场的极坐标中计算笛卡尔坐标(x,y)。

```cpp
void cv::polarToCart(
    cv::InputArray magnitude, // Input array of magnitudes
    cv::InputArray angle, // Input array of angles
    cv::OutputArray x, // Output array of x-components
    cv::OutputArray y, // Output array of y-components
    bool angleInDegrees = false // degrees (if true) radians (if false)
);
```

## 48. cv::pow()

对矩阵逐元素取p次幂。

```cpp
void cv::pow(
    cv::InputArray src, // Input array
    double p, // power for exponentiation
    cv::OutputArray dst // Result array
);
```

## 49. cv::randu()

返回适当类型的随机值。

```cpp
template<typename _Tp> _Tp randu(); // Return random number of specific type
void cv::randu(
    cv::InputOutArray mtx, // All values will be randomized
    cv::InputArray low, // minimum, 1-by-1 (Nc=1,4), or 1-by-4 (Nc=1)
    cv::InputArray high // maximum, 1-by-1 (Nc=1,4), or 1-by-4 (Nc=1)
);
```

## 50. cv::randn()

使用正态分布的值填充矩阵mtx。

```cpp
void cv::randn(
    cv::InputOutArray mtx, // All values will be randomized
    cv::InputArray mean, // mean values, array is in channel space
    cv::InputArray stddev // standard deviations, channel space
);
```

## 51. cv::repeat()

将src的内容复制到dst中。

```cpp
void cv::repeat(
    cv::InputArray src, // Input 2-dimensional array
    int nx, // Copies in x-direction
    int ny, // Copies in y-direction
    cv::OutputArray dst // Result array
);
cv::Mat cv::repeat( // Return result array
    cv::InputArray src, // Input 2-dimensional array
    int nx, // Copies in x-direction
    int ny // Copies in y-direction
);
```

## 52. cv::scaleAdd()

```cpp
void cv::scaleAdd(
    cv::InputArray src1, // First input array
    double scale, // Scale factor applied to first array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
);
```

## 53. cv::setIdentity()

除了将行数和列数相等的元素设置为1（或者给定数值）外，其他所有矩阵元素设置为0。

```cpp
void cv::setIdentity(
    cv::InputOutputArray dst, // Array to reset values
    const cv::Scalar& value = cv::Scalar(1.0) // Apply to diagonal elements
);
```

## 54. cv::solve()

基于invert()的函数，solve()为求解线性系统提供一条捷径，具体公式如下：

```cpp
int cv::solve(
    cv::InputArray lhs, // Lefthand side of system, n-by-n
    cv::InputArray rhs, // Righthand side of system, n-by-1
    cv::OutputArray dst, // Results array, will be n-by-1
    int method = cv::DECOMP_LU // Method for solver
);
```

## 55. cv::solveCubic()

计算三次多项式的实根。

```cpp
int cv::solveCubic(
    cv::InputArray coeffs,
    cv::OutputArray roots
);
```

## 56. cv::solvePoly()

计算任意阶数多项式的根。

```cpp
int cv::solvePoly (
    cv::InputArray coeffs,
    cv::OutputArray roots // n complex roots (2-channels)
    int maxIters = 300 // maximum iterations for solver
);
```

## 57. cv::sort()

opencv的排序函数，分别对每行或列进行排序。

```cpp
void cv::sort(
    cv::InputArray src,
    cv::OutputArray dst,
    int flags
);
```

## 58. cv::sortIdx()

opencv的排序函数，分别对每行或列进行排序，将排序索引结果存在dst中。

```cpp
void cv::sortIdx(
    cv::InputArray src,
    cv::OutputArray dst,
    int flags
);
```

## 59. cv::sqrt()

计算逐元素的平方根。

```cpp
void cv::sqrt(
    cv::InputArray src,
    cv::OutputArray dst
);
```

## 60 cv::subtract()

从src1中减去src2的相应元素。

```cpp
void cv::subtract(
    cv::InputArray src1, // First input array
    cv::InputArray src2, // Second input array
    cv::OutputArray dst, // Result array
    cv::InputArray mask = cv::noArray(), // Optional, do only where nonzero
    int dtype = -1 // Output type for result array
);
```

## 61. cv::sum()

计算矩阵各个通道的所有像素的总和。

```cpp
cv::Scalar cv::sum(
    cv::InputArray arr
);
```

## 62. cv::trace()

计算矩阵的迹。

```cpp
cv::Scalar cv::trace(
    cv::InputArray mat
);
```

## 63. cv::transform()

计算任意线性图像变换。

```cpp
void cv::transform(
    cv::InputArray src,
    cv::OutputArray dst,
    cv::InputArray mtx
);
```

## 64. cv::transpose()

将src中每一个元素复制到dst中行号与列数相调换的位置上。

```cpp
void cv::transpose(
    cv::InputArray src, // Input array, 2-dimensional, n-by-m
    cv::OutputArray dst, // Result array, 2-dimensional, m-by-n
);
```