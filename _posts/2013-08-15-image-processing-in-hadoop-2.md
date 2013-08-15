--- 
layout: post
title: 基于hadoop的图像处理之ImageHeader,FloatImage,ImageBundleInputFormat
categories:
    - study
tags:
    - hadoop
    - image processing
---


**ImageHeader类**：存储图像文件头的一些信息，包括width,height,bitDepth,ImageType，EXIF等。实现readFields()和write()函数，作为key类型。

**FloatImage类**：主要为一个float数组，存储各个点的像素值，作为value类型。

**ImageBundleInputFormat**:包括函数getSplites()和RecordReader()。

getSplites():根据index file 和 data file 得到文件的分片信息，存储为list。

RecorderReader():将分片信息转换为key/value键值对<ImageHeader,FloatImage>。

过程如下：

原始图像数据  -->   hib（index file 和 data file）  -->   ImageHeader/FloatImage  --> 处理后的图像

其中第二步：



- 调用getCurrentKey(),通过调用decodeImageHeader()函数，得到ImageHeader


- 调用getCurrentValue(),通过调用decodeImage()函数，得到FloatImage

附：在map中，处理完数据以后，需要调用encoderImage()，将数据还原为图像数据存储。




































