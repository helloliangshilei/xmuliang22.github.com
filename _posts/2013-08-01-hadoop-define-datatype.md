--- 
layout: post
title: Hadoop之自定义DataType
categories:
    - hadoop
tags:
    - hadoop
---

Hadoop是用JAVA语言实现，然而它的基本数据类型却不是标准的JAVA对象，而是对他们的一个封装，序列化。序列化是指将结构化对象转换为字节流，以便于在网络上进行传输或写到磁盘进行永久存储。

常用的Hadoop的数据类型：

    • BooleanWritable :  标准布尔型数值
    • ByteWritable    :  单字节数值
    • DoubleWritable  :  双字节数值
    • FloatWritable   :  浮点数
    • IntWritable     :  整型数
    • LongWritable    :  长整型数
    • Text            :  使用UTF8格式存储的文本
    • NullWritable    :  当<key, value>中的key或value为空时使用

 这一套数据类型虽然能满足大部分的需求，但有些情况下要写出更灵活的程序，还是需要定制自己的Writable类型。

 
**- 实现的数据类型如果要作为value**

   继承接口Writable,实现其方法write()和readFields()

1.   方法readFields()用于从DataInput二进制流中解析数据

1.   方法write()用于向DataOutput二进制流中封装数据
   
         public interface Writable { 
 
            void readFields(DataInput in);

            void write(DataOutput out);

          }

**- 实现的数据类型如果要作为key**

  实现WritalbeComparable接口,实现其方法

	    	write()
    		readFields()
    		toString()
            compareTo() //Key排序将要使用
			equals()
    		hashCode()  //Partitioner将要使用
			set()
      		get()

  接口WritableComparable

    public interface WritableComparable<T> extends Writable, Comparable<T> {
    }

**示例** 

例如将一个三维坐标结构体struct point3d { float x; float y; float z;}定义为hadoop数据类型。


- 作为value值


        public class Point3D implements Writable {

      		public float x;
    	    public float y;
     	    public float z;
    
      		public Point3D(float x, float y, float z) {
    		this.x = x;
    		this.y = y;
    		this.z = z;
     		 }
    
     	    public Point3D() {
    		this(0.0f, 0.0f, 0.0f);
    		 }
    
     	    public void write(DataOutput out) throws IOException {
    		out.writeFloat(x);
  			out.writeFloat(y);
   		    out.writeFloat(z);
             }
    
     		public void readFields(DataInput in) throws IOException {
    		x = in.readFloat();
    		y = in.readFloat();
  		    z = in.readFloat();
    		}
    
    	    public String toString() {
    		return Float.toString(x) + ", "
    		+ Float.toString(y) + ", "
    		+ Float.toString(z);
      		}
 			}


- 作为key值


    	 public class Point3D implements WritableComparable {

    	  public float x;
    	  public float y;
    	  public float z;
    
    	  public Point3D(float x, float y, float z) {

    		set(x,y,z);
      	  }
    
      	  public Point3D() {

    		this(0.0f, 0.0f, 0.0f);
         }
    
     	 public void write(DataOutput out) throws IOException {

   		   out.writeFloat(x);
   		   out.writeFloat(y);
    	   out.writeFloat(z);
         }
    
      	 public void readFields(DataInput in) throws IOException {

   		   x = in.readFloat();
    	   y = in.readFloat();
    	   z = in.readFloat();
         }
    
      	 public String toString() {

    		return Float.toString(x) + ", "
    		+ Float.toString(y) + ", "
    		+ Float.toString(z);
      	 }
    
    	  /** return the Euclidean distance from (0, 0, 0) */
        public float distanceFromOrigin() {

    		return (float)Math.sqrt(x*x + y*y + z*z);
         }
    
      	public int compareTo(Object o) {

   		  Point3D other = (Point3D)o;
          float myDistance = distanceFromOrigin();
    	  float otherDistance = other.distanceFromOrigin();
    
    	  return Float.compare(myDistance, otherDistance);
      	}
    
        public boolean equals(Object o) {
      	  
          if (!(o instanceof Point3D)) {
     	  return false;
          }
  		  Point3D other = (Point3D)o;
    	  return this.x == other.x && this.y == other.y
    	  && this.z == other.z;
    	}
    
        public int hashCode() {
   		
			 return Float.floatToIntBits(x)
    	 	 ^ Float.floatToIntBits(y)
    	     ^ Float.floatToIntBits(z);
     	}

		public void set(float x, float y, float z) {

    		this.x = x;
    		this.y = y;
    		this.z = z;
      	}

		public void get(float x, float y, float z) {

    		x = this.x;
    		y = this.y;
    		z = this.z;
      	}
        
   	    }

**参考**

Hadoop中关于LongWritable的源码





    /** A WritableComparable for longs. */
    public class LongWritable implements WritableComparable {
      private long value;
    
      public LongWritable() {}
    
      public LongWritable(long value) { set(value); }
    
      /** Set the value of this LongWritable. */
      public void set(long value) { this.value = value; }
    
      /** Return the value of this LongWritable. */
      public long get() { return value; }
    
      public void readFields(DataInput in) throws IOException {
    value = in.readLong();
      }
    
      public void write(DataOutput out) throws IOException {
    out.writeLong(value);
      }
    
      /** Returns true iff <code>o</code> is a LongWritable with the same value. */
      public boolean equals(Object o) {
    if (!(o instanceof LongWritable))
      return false;
    LongWritable other = (LongWritable)o;
    return this.value == other.value;
      }
    
      public int hashCode() {
    return (int)value;
      }
    
      /** Compares two LongWritables. */
      public int compareTo(Object o) {
    long thisValue = this.value;
    long thatValue = ((LongWritable)o).value;
    return (thisValue<thatValue ? -1 : (thisValue==thatValue ? 0 : 1));
      }
    
      public String toString() {
    return Long.toString(value);
      }
    
      /** A Comparator optimized for LongWritable. */ 
      public static class Comparator extends WritableComparator {
    public Comparator() {
      super(LongWritable.class);
    }
    
    public int compare(byte[] b1, int s1, int l1,
       byte[] b2, int s2, int l2) {
      long thisValue = readLong(b1, s1);
      long thatValue = readLong(b2, s2);
      return (thisValue<thatValue ? -1 : (thisValue==thatValue ? 0 : 1));
    }
      }
    
      /** A decreasing Comparator optimized for LongWritable. */ 
      public static class DecreasingComparator extends Comparator {
    public int compare(WritableComparable a, WritableComparable b) {
      return -super.compare(a, b);
    }
    public int compare(byte[] b1, int s1, int l1, byte[] b2, int s2, int l2) {
      return -super.compare(b1, s1, l1, b2, s2, l2);
    }
      }
    
      static {   // register default comparator
    WritableComparator.define(LongWritable.class, new Comparator());
      }
    
    }
    




























































