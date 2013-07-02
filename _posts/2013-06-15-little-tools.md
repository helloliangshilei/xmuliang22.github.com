--- 
layout: post
title: 常用的小工具
categories:
    - tools
tags:
    - tools
---

在这里记录一些常用的命令，省得每次用到的时候还要去找，提高效率。

#git和github上搭建个人博客

- **第一步：** 安装git 
	
	apt-get install git

- **第二步：** clone版本库

	git clone [url]

- **第三步：** 在本地进行修改后提交
	
	git add .（添加所有文件）
	git commit -m"some message" （从工作区提交到暂存区）
	git push （推送到远程版本库）

#ubuntu12.04 ustc源

- **第一步：** 修改更新源

	vim /etc/apt/sources.list

- **第二步：** 厦大源

	deb http://mirrors.xmu.edu.cn/ubuntu/archive/ precise main restricted universe multiverse
	deb http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-backports restricted universe multiverse
	deb http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-proposed main restricted universe multiverse
	deb http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-security main restricted universe multiverse
	deb http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-updates main restricted universe multiverse
	deb-src http://mirrors.xmu.edu.cn/ubuntu/archive/ precise main restricted universe multiverse
	deb-src http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-backports main restricted universe multiverse
	deb-src http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-proposed main restricted universe multiverse
	deb-src http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-security main restricted universe multiverse
	deb-src http://mirrors.xmu.edu.cn/ubuntu/archive/ precise-updates main restricted universe multiverse

- **第三步：** 更新
	
	apt-get update
















































