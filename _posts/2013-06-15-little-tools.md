--- 
layout: post
title: 常用的小工具
categories:
    - tools
tags:
    - tools
---

在这里记录一些常用的命令，省得每次用到的时候还要去找，提高效率。

**git和github上搭建个人博客**

- **第一步：** 安装git 
	
	`apt-get install git`

- **第二步：** clone版本库（https://github.com/xmuliang/xmuliang.github.com.git）

	`git clone [url]`

- **第三步：** 在本地进行修改后提交
	
	    git add .（添加所有文件）
    	git commit -m"some message" （从工作区提交到暂存区）
    	git push （推送到远程版本库）

**ubuntu12.04 厦大源**

- **第一步：** 修改更新源

	`vim /etc/apt/sources.list`

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
	
	`apt-get update`

**设置jdk环境变量(linux)**

	    export JAVA_HOME=/usr/jdk
    	export JRE_HOME=/usr/jdk/jre
    	export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
    	export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/lib

**eclipse**

- **第一项：** java.lang.OutOfMemoryError:Java heap space

	    window->Preferences->Java->Installed JREs
    
    	edit:-Xms128M -Xmx512M

- **第二项：** 添加桌面启动图标
	
	`vim /usr/share/applications/eclipse.desktop`

	添加:
	
	    [Desktop Entry]
    	Type=Application
    	Name=Eclipse
    	Comment=Eclipse Integrated Development Environment
    	Icon=/usr/eclipse/icon.xpm
    	Exec=/usr/eclipse/eclipse
    	Terminal=false
    	Categories=Development;IDE;Java;

**教程**
    

- mysql中文教程


     	http://dev.mysql.com/doc/refman/5.1/zh/index.html



- hadoop教程

		 http://www.cnblogs.com/xia520pi/archive/2012/04/08/2437875.html












































