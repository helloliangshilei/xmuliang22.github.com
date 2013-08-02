--- 
layout: post
title: Hadoop配置JobConf
categories:
    - hadoop
tags:
    - hadoop
---

JobConf的配置：


- setInputFormat：设置map的输入格式，默认为TextInputFormat，key为LongWritable, value为Text

<table>
    <tr>
        <td>输入格式</td>
        <td>描述</td>
        <td>键</td>
        <td>值</td>
    </tr>
    <tr>
        <td>TextInputFormat</td>
        <td>默认格式，读取文件的行</td>
        <td>行的字节偏移量(LongWritable)</td>
        <td>行的内容(Text)</td>
    </tr>
    <tr>
        <td>KeyValueInputFormat</td>
        <td>把行解析为键值对</td>
        <td>第一个tab字符前的所有字符(Text)</td>
        <td>行的剩余内容(Text)</td>
    </tr>
    <tr>
        <td>NLineInputFormat</td>
        <td>希望mapper收到固定行数的输入，其它与TextInputFormat一样</td>
        <td>行的字节偏移量(LongWritable)</td>
        <td>行的内容(Text)</td>
    </tr>
	<tr>
        <td>SequenceFileInputFormat</td>
        <td>Hadoop定义的高性能二进制格式</td>
        <td>用户自定义</td>
        <td>用户自定义</td>
    </tr>
    
</table>



- setOutputFormat：提供给OutputCollector的键值对会被写到输出文件中，写入的方式由输出格式控制。OutputFormat的功能跟前面描述的InputFormat类很像，Hadoop提供的OutputFormat的实例会把文件写在本地磁盘或HDFS上，它们都是继承自公共的FileInputFormat类。每一个reducer会把结果输出写在公共文件夹中一个单独的文件内，这些文件的命名一般是part-nnnnn，nnnnn是关联到某个reduce任务的partition的id，输出文件夹通过FileOutputFormat.setOutputPath() 来设置。你可以通过具体MapReduce作业的JobConf对象的setOutputFormat()方法来设置具体用到的输出格式。

	默认输出格式是TextOuputFormat,它把每条记录写到文本行。它的键和值可以是任意类型。因为TextOutputFormat调用toString()方法把他们转换为字符串。每个键/值由制表符进行分割。

<table>
    <tr>
        <td>输出格式</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>TextOutputFormat</td>
        <td>默认的输出格式， 以 "key \t value" 的方式输出行</td>
    </tr>
    <tr>
        <td>NullOutputFormat</td>
        <td>忽略收到的数据，即不做输出</td>
    </tr>
    <tr>
        <td>SequenceFileOutputFormat</td>
        <td>输出二进制文件，适合于读取为子MapReduce作业的输入</td>
    </tr>
</table>




- setMapOutputKeyClass和setMapOutputValueClass：设置Mapper的输出的key-value对的格式。如果没有显示设置，中间的类型默认为（最终的）输出类型。



- setOutputKeyClass和setOutputValueClass：设置Reducer的输出的key-value对的格式。




- setMapperClass和setReducerClass：设置Mapper，默认为IdentityMapper；设置Reducer，默认为IdentityReducer。



- FileInputFormat.addInputPath：设置输入文件的路径，可以使一个文件，一个路径，一个通配符。可以被调用多次添加多个路径。



- FileOutputFormat.setOutputPath：设置输出文件的路径，在job运行前此路径不应该存在
































