--- 
layout: post
title: opencv里图像的输入与输出
categories:
    - study
tags:
    - opencv
    - image processing
---



opencv中常见的与图像操作有关的数据容器有Mat，cvMat和IplImage，这三种类型都可以代表和显示图像。

- Mat类型：侧重于计算，数学性较高，openCV对Mat类型的计算也进行了优化。


- CvMat和IplImage类型：更侧重于“图像”，opencv对其中的图像操作（缩放、单通道提取、图像阈值操作等）进行了优化。在opencv2.0之前，opencv是完全用C实现的，但是，IplImage类型与CvMat类型的关系类似于面向对象中的继承关系。

	实际上，CvMat之上还有一个更抽象的基类----CvArr，这在源代码中会常见。

**IplImage**





# 结构体定义 #

	

----------

	typedef struct _IplImage 
	{ 
    int nSize;    /* IplImage大小 */
    int ID;    /* 版本 (=0)*/
    int nChannels;  /* 大多数OPENCV函数支持1,2,3 或 4 个通道 */ 
    int alphaChannel;  /* 被OpenCV忽略 */ 
    int depth;   /* 像素的位深度: IPL_DEPTH_8U, IPL_DEPTH_8S, IPL_DEPTH_16U, 
                IPL_DEPTH_16S, IPL_DEPTH_32S, IPL_DEPTH_32F and IPL_DEPTH_64F 可支持 */ 
    
    char colorModel[4]; /* 被OpenCV忽略 */ 
    char channelSeq[4]; /* 被OpenCV忽略 */ 
    int dataOrder;      /* 0 - 交叉存取颜色通道, 1 - 分开的颜色通道. cvCreateImage只能创建交叉存取图像 */ 
    int origin;     /* 0 - 顶—左结构,1 - 底—左结构 (Windows bitmaps 风格) */ 
    int align;     /* 图像行排列 (4 or 8). OpenCV 忽略它，使用 widthStep 代替 */ 
    
    int width;     /* 图像宽像素数 */ 
    int height;    /* 图像高像素数*/ 
    
    struct _IplROI *roi;  /* 图像感兴趣区域. 当该值非空只对该区域进行处理 */ 
    struct _IplImage *maskROI; /* 在 OpenCV中必须置NULL */ 
    void *imageId;  /* 同上*/ 
    struct _IplTileInfo *tileInfo;  /*同上*/ 
    
    int imageSize;    /* 图像数据大小(在交叉存取格式下imageSize=image->height*image->widthStep），单位字节*/ 
    char *imageData;    /* 指向排列的图像数据 */ 
    int widthStep;     /* 排列的图像行大小，以字节为单位 */ 
    int BorderMode[4];     /* 边际结束模式, 被OpenCV忽略 */ 
    int BorderConst[4];    /* 同上 */ 
    
    char *imageDataOrigin;    /* 指针指向一个不同的图像数据结构（不是必须排列的），是为了纠正图像内存分配准备的 */ 
	} IplImage;

----------






# 存储和读取#

## 间接存取 ##
    
    IplImage* img=cvLoadImage("lena.jpg", 1);
    CvScalar s;   /*sizeof(s) == img->nChannels*/
    s=cvGet2D(img,i,j);  /*get the (i,j) pixel value*/
    cvSet2D(img,i,j,s);   /*set the (i,j) pixel value*/

## 宏操作 ##

    IplImage* img; //malloc memory by cvLoadImage or cvCreateImage
    for(int row = 0; row <　img->height; row++)
    {
    for (int col = 0; col < img->width; col++)
    {
    b = CV_IMAGE_ELEM(img, UCHAR, row, col * img->nChannels + 0); 
    g = CV_IMAGE_ELEM(img, UCHAR, row, col * img->nChannels + 1); 
    r = CV_IMAGE_ELEM(img, UCHAR, row, col * img->nChannels + 2);
    }
    }

	//输出的时候需进行强制类型转换

	cout<<(int)CV_IMAGE_ELEM(img, UCHAR, row, col * img->nChannels + 0);

## 直接存取 ##

	uchar b, g, r; // 3 channels
	for(int row = 0; row <　img->height; row++)
	{
    for (int col = 0; col < img->width; col++)
    {
        b = ((uchar *)(img->imageData + row * img->widthStep))[col * img->nChannels + 0]; 
        g = ((uchar *)(img->imageData + row * img->widthStep))[col * img->nChannels + 1]; 
        r = ((uchar *)(img->imageData + row * img->widthStep))[col * img->nChannels + 2];
    }
	}
	//同上，输出时进行强制类型转换
    

**CvMat**

# 结构体定义 #

    typedef struct CvMat 
    { 
    int type; 
    int step;  /*用字节表示行数据长度*/
    int* refcount; /*内部访问*/
    union {
    uchar*  ptr;
    short*  s;
    int*i;
    float*  fl;
    double* db;
    } data;/*数据指针*/
     union {
    int rows;
    int height;
    };
    union {
    int cols;   
    int width;
    };
    } CvMat; /*矩阵结构头*/


#存储和读取#

## 创建 ##

    CvMat * cvCreateMat(int rows, int cols, int type); /*创建矩阵头并分配内存*/
    CV_INLine CvMat cvMat((int rows, int cols, int type, void* data CV_DEFAULT); /*用已有数据data初始化矩阵*/
    CvMat * cvInitMatHeader(CvMat * mat, int rows, int cols, int type, void * data CV_DEFAULT(NULL), int step CV_DEFAULT(CV_AUTOSTEP)); /*(用已有数据data创建矩阵头)*/

## 对矩阵进行访问 ##

    /*间接访问*/
    /*访问CV_32F1和CV_64FC1*/
    cvmSet( CvMat* mat, int row, int col, double value);
    cvmGet( const CvMat* mat, int row, int col );
    
    /*访问多通道或者其他数据类型: scalar的大小为图像的通道值*/
    CvScalar cvGet2D(const CvArr * arr, int idx0, int idx1); //CvArr只作为函数的形参void cvSet2D(CvArr* arr, int idx0, int idx1, CvScalar value);
    
    
    /*直接访问: 取决于数组的数据类型*/
    /*CV_32FC1*/
    CvMat * cvmat = cvCreateMat(4, 4, CV_32FC1);
    cvmat->data.fl[row * cvmat->cols + col] = (float)3.0;
    
    /*CV_64FC1*/
    CvMat * cvmat = cvCreateMat(4, 4, CV_64FC1);
    cvmat->data.db[row * cvmat->cols + col] = 3.0;
    
    /*一般对于单通道*/
    CvMat * cvmat = cvCreateMat(4, 4, CV_64FC1);
    CV_MAT_ELEM(*cvmat, double, row, col) = 3.0; /*double是根据数组的数据类型传入,这个宏不能处理多通道*/
    
    /*一般对于多通道*/
    if (CV_MAT_DEPTH(cvmat->type) == CV_32F)
    CV_MAT_ELEM_CN(*cvmat, float, row, col * CV_MAT_CN(cvmat->type) + ch) = (float)3.0; // ch为通道值
    if (CV_MAT_DEPTH(cvmat->type) == CV_64F)
    CV_MAT_ELEM_CN(*cvmat, double, row, col * CV_MAT_CN(cvmat->type) + ch) = 3.0; // ch为通道值
    
    
    /*多通道数组*/
    /*3通道*/
    for (int row = 0; row < cvmat->rows; row++)
    {
    p = cvmat ->data.fl + row * (cvmat->step / 4);
    for (int col = 0; col < cvmat->cols; col++)   
    {   
     *p = (float) row + col;   
     *(p+1) = (float)row + col + 1;   
     *(p+2) = (float)row + col + 2;   
     p += 3;
    }
    }
    /*2通道*/
    CvMat * vector = cvCreateMat(1,3, CV_32SC2);CV_MAT_ELEM(*vector, CvPoint, 0, 0) = cvPoint(100,100);
    /*4通道*/
    CvMat * vector = cvCreateMat(1,3, CV_64FC4);CV_MAT_ELEM(*vector, CvScalar, 0, 0) = CvScalar(0, 0, 0, 0);
    

**Mat**

# 类定义 #

    class CV_EXPORTS Mat
    {
     
    public：
     
    /*..很多方法..*/
    /*............*/
     
    int flags;（Note ：目前还不知道flags做什么用的）
    int dims;  /*数据的维数*/
    int rows,cols; /*行和列的数量;数组超过2维时为(-1，-1)*/
    uchar *data;   /*指向数据*/
    int * refcount;   /*指针的引用计数器; 阵列指向用户分配的数据时，指针为 NULL
    
     
    /* 其他成员 */ 
    ...
     
    };


# 存储和读取 #

    /*对某行进行访问*/
    Mat M;
    M.row(3) = M.row(3) + M.row(5) * 3; /*第5行扩大三倍加到第3行*/
    
    /*对某列进行复制操作*/
    Mat M1 = M.col(1);
    M.col(7).copyTo(M1); /*第7列复制给第1列*/
    
    /*对某个元素的访问*/
    Mat M;
    M.at<double>(i,j); /*double*/
    M.at(uchar)(i,j);  /*CV_8UC1*/
    Vec3i bgr1 = M.at(Vec3b)(i,j) /*CV_8UC3*/
    Vec3s bgr2 = M.at(Vec3s)(i,j) /*CV_8SC3*/
    Vec3w bgr3 = M.at(Vec3w)(i,j) /*CV_16UC3*/
    
    /*遍历整个二维数组*/
    double sum = 0.0f;
    for(int row = 0; row < M.rows; row++)
    {
    const double * Mi = M.ptr<double>(row); 
    for (int col = 0; col < M.cols; col++)  
    sum += std::max(Mi[col], 0.);
    }
    
    /*STL iterator*/
    double sum=0;
    MatConstIterator<double> it = M.begin<double>(), it_end = M.end<double>();
    for(; it != it_end; ++it)
    sum += std::max(*it, 0.);

	//三个重要方法
	Mat mat = imread(const String* filename);           // 读取图像
	imshow(const string frameName, InputArray mat);  //    显示图像
	imwrite (const string& filename, InputArray img);    //储存图像










































