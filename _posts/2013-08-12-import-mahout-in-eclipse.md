--- 
layout: post
title: mahout导入到eclipse
categories:
    - study
tags:
    - mahout
---



mahout是基于hadoop平台的ml库，里面囊括了很多的机器学习的算法，最近一直在学习里面关于random forests的部分。

在eclipse里面导入mahout源代码的过程中遇到了一点问题。

mahout源代码是构建过程，用到maven(项目管理工具)。

所以导入的过程如下所示：



- 第一步：安装maven,设置环境变量


- 第二步：设置仓库的位置，里面存放项目所依赖的jar包。打开 &maven_home/conf/setting.xml文件，设置我们创建的仓库位置。

![repo](/mypicture/repo.jpg)



- 第三步：下载mahout源代码，其并不是eclipse所需要的项目格式。我们需要将其转化成eclipse可以导入的项目格式。

	
 <blockquote> 首先，进入mahout项目目录，执行： </blockquote>
	
	mvn clean compile

 <blockquote> 然后，再执行一条命令： </blockquote>

	mvn ecipse:eclipse

就可以得到我们所需要的项目目录。


第四步：在eclipse中配置maven仓库的路径

window->perferences->java->build path->classpath variables.

new一个新的变量路径。

此时，用eclipse可以成功导入项目。







































