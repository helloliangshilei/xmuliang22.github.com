--- 
layout: post
title: 基于hadoop的图像处理之HipiImageBundle
categories:
    - study
tags:
    - hadoop
    - image processing
---


为了能够在hadoop平台下能够对图像进行处理，将图像打包成一种特定的格式：HipiImageBundle。 下面就来梳理一下，如何将图像打包成这种格式。

AbstractImageBundle：抽象类，是hipi中所有image bundle的基类。所有继承该类的子类都要实现open,read,close等方法。

HipiImageBundle：实现上述类。

成员变量：
_index_file：index file ,和输入文件名相同
_data_file:存放数据的地方，名字为_index_file.dat

成员函数：


- writeBundleHeader():写index file,index file主要存储内容如下：


- 4个字节的标识：0x81911618   “HIPIIBIH”的ASCII码表示
- 2个字节的_data_file名的长度
- 8个字节的_data_file的名字
- 8个字节的图像的个数，初始时为-1
- 16个字节保留空间
- 4个字节，需要skip多少字节后，跳到记录图像文件的初始位置，每一个图像对应一个初始位置

    private void writeBundleHeader() throws IOException {
    		// the index file header designed like this:
    		// 4-byte magic signature (0x81911618) for "HIPIIbIH"
    		// 2-byte (short int) to denote the length of data file name
    		// variable bytes of data file name
    		// 8-byte of image count (not mandated)
    		// 16-byte of reserved field
    		// 4-byte points to how much to skip in order to reach the start of
    		// offsets (default 0)
    		// 8-byte of offsets (of the end position) starts from here until EOF
    		_index_output_stream.writeInt(0x81911b18);
    		String data_name = _data_file.getName();
    		// write out filename in UTF-8 encoding
    		byte[] name_byte = data_name.getBytes("UTF-8");
    		_index_output_stream.writeShort(name_byte.length);
    		_index_output_stream.write(name_byte);
    		// write out image count (default -1 (unknown count))
    		_index_output_stream.writeLong(-1);
    		// write out reserved field
    		_index_output_stream.writeLong(0);
    		_index_output_stream.writeLong(0);
    		// skip 0 to reach start offset (potentially, you could put some
    		// metadata in between
    		_index_output_stream.writeInt(0);
    	}



- openForWrite()：写文件，包括写index文件和dat文件

   		 protected void openForWrite() throws IOException {
    		// Check if the instance is already in some read/write states
    		if (_data_output_stream != null || _reader != null
    				|| _index_output_stream != null || _index_input_stream != null) {
    			throw new IOException("File " + _file_path.getName()
    					+ " already opened for reading/writing");
    		}
    
    		// HipiImageBundle will create two files: an index file and a data file
    		// by default, the Path of output_file is the Path of index file, and
    		// data
    		// file will simply append .dat suffix
    		_index_file = _file_path;
    		FileSystem fs = FileSystem.get(_conf);

    		_index_output_stream = new DataOutputStream(fs.create(_index_file));//输出流，关联到_index_file

    		_data_file = _file_path.suffix(".dat");
    		if (_blockSize <= 0)
    			_blockSize = fs.getDefaultBlockSize();
    		if (_replication <= 0)
    			_replication = fs.getDefaultReplication();

    		_data_output_stream = new DataOutputStream(fs.create(_data_file, true, fs.getConf().getInt("io.file.buffer.size", 4096), _replication, _blockSize));//输出流，关联_data_file

    		_countingOffset = 0;
    		writeBundleHeader();//写indelx file,在addimage的过程中会
    	}




- addImage(InputStream image_stream,ImageType type):添加图像，分别将图像长度，图像类型，图像添加到_data_output_stream中；将图像的位置信息添加到_index_output_stream.

  		  public void addImage(InputStream image_stream, ImageType type)
    	throws IOException {
    		byte data[] = readBytes(image_stream);
    		_cacheLength = data.length;
    		_cacheType = type.toValue();
    		_sig[0] = (byte) (_cacheLength >> 24);
    		_sig[1] = (byte) ((_cacheLength >> 16) & 0xff);
    		_sig[2] = (byte) ((_cacheLength >> 8) & 0xff);
    		_sig[3] = (byte) (_cacheLength & 0xff);
    		_sig[4] = (byte) (_cacheType >> 24);
    		_sig[5] = (byte) ((_cacheType >> 16) & 0xff);
    		_sig[6] = (byte) ((_cacheType >> 8) & 0xff);
    		_sig[7] = (byte) (_cacheType & 0xff);
    		_data_output_stream.write(_sig);
    		_data_output_stream.write(data);
    		_countingOffset += 8 + data.length;
    		_index_output_stream.writeLong(_countingOffset);
    	}
































