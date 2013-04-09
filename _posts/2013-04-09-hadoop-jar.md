--- 
layout: post
title: 将mapreduce程序打包成jar文件的方法
categories:
    - cloud computing
tags:
    - hadoop
    - mapreduce
    - jar
---

在元智因为老师没有给配电脑,所以就不怎么去实验室了,因为电脑懒得拿,拿来拿去的很不方便.所以一般就呆在图书馆,用图书馆的电脑远程到寝室里的电脑.图书馆的电脑没有管理员的权限,所以用起来不是很方便.

用putty远程登录的话,就没有办法用eclipse了.所以想运行hadoop的程序只能先将它打包成jar文件,然后放在hadoop的目录下执行.

#将程序打包成jar文件的方法
- **第一步:** 编写源程序,例如test.java,存放的是要被执行的源代码;

	   vim test.java 

- **第二步:** 编译源程序,很简单,利用javac test.java,编译生成test.class文件,但针对于hadoop的程序,这样可是不行的,需要将关于hadoop下的jar包include进来,此jar包存放在hadoop的安装目录下,名称为hadoop-*(版本号)-core.jar.所以完整的命令如下所示:

	   javac -classpath path/to/hadoop/hadoop-*-core.jar test.java 

或者,当编译出来多个class文件的时候,将它们置于同一目录下:

	   javac -classpath path/to/hadoop/hadoop-*-core.jar -d DIR(文件名) test.java

- **第三步:** 将编译完成的class文件打包成jar包,格式为 jar -cvfm jar文件名 manifest.mf文件 class文件,关于manifest.mf文件,它是jar文件的配置文件,可以用别的名称,但后缀名必须是.mf,里面的内容主要是用来指定主类名,用来指示程序的入口,例如 Main-Class: test.:后必须有一个空格,

	   jar -cvfm test.jar manifest.mf test.class

当含有多个class文件位于同一目录下的时候,可以这样来写:

	   jar -cvf test.jar -C DIR(class文件的存放目录)

这种方法就无须编写manifest文件,但只有一个class文件的时候采用此方法会产生错误,需要手动更改自动生成的manifest文件.


#jar包在hadoop下的运行
- **命令格式**
	    path/to/bin/hadoop jar jar包 mainclass 参数 


后记,今天程序打包好以后,但是在启动hadoop时的时候namenode和datenode老是起不来,花了很多时间来解决.

- namenode起不来
需要重新格式化在core-site.xml中配置的文件,命令

            hadoop namenode -format 

- datenode起不来
需要手动删除core-site.xml配置的文件下的所有文件,然后重新格式化文件系统.

有时候是某个进程一直在运行，导致无法成功启动hadoop.这时候需要手动杀死该进程，首先需要找到该进程的pid,然后
	    kill -9 pid







