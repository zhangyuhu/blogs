---
title: "opencv 常用操作"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [opencv]
categories: [开源软件]
---

# unsigned char* 转换为 Mat类型

```c++
cv::Mat mmat = cv::Mat(stFrameInfo.nHeight, stFrameInfo.nWidth, CV_8UC1, stImageInfo.pBufAddr, 0);
```



# BYTE* 转换为 Mat类型

```c++
cv::Mat sMat = Mat(sFrameHead.iHeight, sFrameHead.iWidth, CV_8UC1);;
		int nLen = sMat.rows * sMat.cols * sMat.channels();
		memcpy(sMat.data, m_pFrameBuffer, nLen);
```



# Mat转unsigned char*

```c++
unsigned char pdata= srcMat.data; // Mat => unsigned char
```



# box 渲染

```c++
void BoxVisualization(cv::Mat frame, int tl_x, int tl_y, int br_x, int br_y, int label, float score)
{
    // 渲染box
    int fontface    = cv::FONT_HERSHEY_SIMPLEX;
    int thickness   = 1;
    float fontScale = 0.5;
    int baseline    = 0;
    cv::Scalar color(140, 199, 0);

    cv::Rect box((int)tl_x, (int)tl_y, (int)(br_x - tl_x), (int)(br_y - tl_y));
    cv::rectangle(frame, box, color, 2, 8, 0);
    cv::String label_str = cv::format("l %d, s: %.2f", label, score);
    cv::Size fontSize    = cv::getTextSize(label_str, fontface, fontScale, thickness, &baseline);
    cv::rectangle(frame, cv::Size(box.tl().x, box.tl().y - fontSize.height - baseline), cv::Size(box.tl().x + fontSize.width, box.tl().y),
                  cv::Scalar(34, 43, 59), -1, 8);

    cv::putText(frame, label_str, cv::Point(box.tl().x, box.tl().y - baseline), fontface, fontScale, cv::Scalar(255, 0, 255), thickness, 8);
}
```



# 画圆

```c++
void Visualization(cv::Mat frame, int tl_x, int tl_y, int br_x, int br_y)
{
    // 渲染box
    cv::Point circle_point((br_x + tl_x) / 2, (br_y + tl_y) / 2);
    int len = std::min((br_y - tl_y) / 2, (br_x - tl_x) / 2);
    cv::circle(frame, circle_point, len, cv::Scalar(255, 0, 0), 4);
}
```



# 常用颜色定义

