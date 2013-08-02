--- 
layout: post
title: Hadoop之自定义InputFormat
categories:
    - hadoop
tags:
    - hadoop
---

**InputFormat**

一个输入分片（split）是由单个map处理的输入块。每一个map操作只处理一个输入分片。每个分片被划分为若干个记录，一个记录就是一个键/值对，map一个接一个的处理每条记录。

InputFormat负责产生输入分片并将他们分割成记录.



    public abstract class InputFormat<K, V> {
    
     
      public abstract 
    List<InputSplit> getSplits(JobContext context
       ) throws IOException, InterruptedException;
      
     
      public abstract 
    RecordReader<K,V> createRecordReader(InputSplit split,
     TaskAttemptContext context
    ) throws IOException, 
     InterruptedException;
    
    }

1. jobClient调用getSplits()方法,得到分片。
1. 在计算好分片数后，客户端将它们发送到jobtracker,jobtracker便使用其存储位置信息来调度map任务从而在tasktracker上处理这些分片数据。
1. 在tasktracker上，利用createRecordReader()方法来获得这个分片的RecordReader,来记录生成的键/值对，然后再传递给map函数。
  

也就是说InputFormat完成以下工作：InputFile --> splits --> <K,V>

**FileInputFormat**

FileInputFormat是所有文件使用文件作为其数据源的InputFormat实现的基类。它提供了两个功能：

1. 一个定义哪些文件包含在一个作业的输入之中；
1. 一个为输入文件生成分片的实现。

把分片分割成记录则由其子类来完成。


**自定义InputFormat**

然而系统所提供的这几种固定的将 InputFile转换为<K,V>的方式有时候并不能满足我们的需求，此时需要我们自定义 InputFormat，从而使Hadoop框架按照我们预设的方式来将InputFile解析为<K,V>。

由上述分析可知，只需要在自定义的InputFormat中实现getRecordReader方法即可，该方法实现了抽象类RecordReader。**定义一个InputFormat的核心是定义一个类似于LineRecordReader的，自己的RecordReader**。

**RecordReader**




    
    /**
     * The record reader breaks the data into key/value pairs for input to the
     * {@link Mapper}.
     * @param <KEYIN>
     * @param <VALUEIN>
     */
    public abstract class RecordReader<KEYIN, VALUEIN> implements Closeable {
    
      /**
       * Called once at initialization.
       * @param split the split that defines the range of records to read
       * @param context the information about the task
       * @throws IOException
       * @throws InterruptedException
       */
      public abstract void initialize(InputSplit split,
      TaskAttemptContext context
      ) throws IOException, InterruptedException;
    
      /**
       * Read the next key, value pair.
       * @return true if a key/value pair was read
       * @throws IOException
       * @throws InterruptedException
       */
      public abstract 
      boolean nextKeyValue() throws IOException, InterruptedException;
    
      /**
       * Get the current key
       * @return the current key or null if there is no current key
       * @throws IOException
       * @throws InterruptedException
       */
      public abstract
      KEYIN getCurrentKey() throws IOException, InterruptedException;
      
      /**
       * Get the current value.
       * @return the object that was read
       * @throws IOException
       * @throws InterruptedException
       */
      public abstract 
      VALUEIN getCurrentValue() throws IOException, InterruptedException;
      
      /**
       * The current progress of the record reader through its data.
       * @return a number between 0.0 and 1.0 that is the fraction of the data read
       * @throws IOException
       * @throws InterruptedException
       */
      public abstract float getProgress() throws IOException, InterruptedException;
      
      /**
       * Close the record reader.
       */
      public abstract void close() throws IOException;
    }
    


**自定义ObjectPositionInputFormat示例**

数据每一行为 “物体，x坐标，y坐标，z坐标”

   ball 3.5,12.7,9.0

   car 15,23.76,42.23

   device 0.0,12.4,-67.1

每一行将要被解析为<Text, Point3D>（Point3D是我们在上一篇日志中自定义的数据类型）


	public class ObjectPositionInputFormat extends FileInputFormat<Text, Point3D> {   


    @Override

    public RecordReader<Text, Point3D> createRecordReader(InputSplit inputsplit,

            TaskAttemptContext context) throws IOException, InterruptedException {

        // TODO Auto-generated method stub

        return new objPosRecordReader();

    }

    public static class objPosRecordReader extends RecordReader<Text,Point3D>{




        public LineReader in;

        public Text lineKey;

        public Point3D lineValue;

        public StringTokenizer token=null;       

        public Text line;      

        @Override

        public void close() throws IOException {

            // TODO Auto-generated method stub           

        }

     @Override

        public Text getCurrentKey() throws IOException, InterruptedException {

            // TODO Auto-generated method stub

            return lineKey;

        }

        @Override

        public Point3D getCurrentValue() throws IOException,

                InterruptedException {

            // TODO Auto-generated method stub           

            return lineValue;

        }

        @Override

        public float getProgress() throws IOException, InterruptedException {

            // TODO Auto-generated method stub

            return 0;

        }

        @Override

        public void initialize(InputSplit input, TaskAttemptContext context)

                throws IOException, InterruptedException {

            // TODO Auto-generated method stub

            FileSplit split=(FileSplit)input;

            Configuration job=context.getConfiguration();

            Path file=split.getPath();

            FileSystem fs=file.getFileSystem(job);

           

            FSDataInputStream filein=fs.open(file);

            in=new LineReader(filein,job);

           

            line=new Text();

            lineKey=new Text();

            lineValue=new Point3D();                     

        }

        @Override

        public boolean nextKeyValue() throws IOException, InterruptedException {

            // TODO Auto-generated method stub

            int linesize=in.readLine(line);

            if(linesize==0)

                return false;

               token=new StringTokenizer(line.toString());

            String []temp=new String[2];

            if(token.hasMoreElements()){

                temp[0]=token.nextToken();

                if(token.hasMoreElements()){

                    temp[1]=token.nextToken();

                }

            }


            String []points=temp[1].split(",");

            lineKey.set(temp[0]);

            lineValue.set(Float.parseFloat(points[0]),Float.parseFloat(points[1]), Float.parseFloat(points[2]));

            return true;

        }       

    }

	}


整个的继承层次关系：

InputFormat（getSplits（），createRecordReader（））  -->  FileInputFormat(实现getSplits（）)  -->  SelfDefineInputFomat(实现createRecordReader（），该函数返回值为SelfDefineRecordReader对象)

SelfDefineInputFomat.createRecordReader（） calls SelfDefineRecordReader

RecordReader-->SelfDefineRecordReader.




















































