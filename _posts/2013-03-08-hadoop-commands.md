--- 
layout: post
title: hadoop中常用的命令
date: 2012-03-08 16:20:23
categories:
    - cloud computing
tags:
    - hadoop
    - command
---

#启动hadoop
- start-all.sh
#关闭hadoop
- stop-all.sh
#对目录或文件进行操作
- 创建目录: hadoop fs -mkdir [指定目录]（默认保存在"/user/用户名/"下）.
- 从本地上传文件或目录: hadoop fs -put [本地文件或目录] [目标目录].
- 从hadoop上下载文件到本地: hadoop fs -get [目标文件] [本地文件夹].
- 删除目录: hadoop fs -rmr [指定目录].
- 删除文件: hadoop fs -rm [指定文件].
- 打开已经存在的文件: hadoop fs -cat [指定文件].
- 显示指定目录下的内容: hadoop fs -ls [指定目录].
























































