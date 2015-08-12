## Chapter 2

### 2.1
uncached memory operation  
Uncached memory is typically used by device drivers or other components that interact directly with hardware.  
From: http://joryanick.com/retro/memcpy.htm

Link:  
http://www.blogjava.net/sunzhong/articles/295916.html  
http://www.ibm.com/developerworks/cn/java/j-jtp06197.html  
http://techpubs.sgi.com/library/dynaweb_docs/0620/SGI_Developer/books/DevDriver_PG/sgi_html/ch01.html


### 2.2
有可能导致访问内存地址时导致“错误的命中”，此时cache line中的数据是无效的。

### 2.3
因此高速缓存是按照行来操作的，因此每行需要有自己的标记。

### 2.4
<9..2>一共8位，取值范围是[0, 256)，因此回到512有一半的空间被浪费。如果是双路组相连则有256组，如果cache line大小不超过4字节，则可以这么来索引。

### 2.5
组数是2的整数幂，这样hash计算实现起来会简单高效一些。然后总函数是组数*组内行数，因此是否是2的整数幂是有组内行数决定的。

### 2.6
直接映射：  
不是一种好的方法，因此0x1000 ~ 0x4fff的hash值范围是[0x10,0x4f], cache的组id取值范围为[0,0x3FF]；同时0x1000 ～ 0x10FF会被映射到相同的行。   
16路组相连：  
cache组id取值范围为[0, 0x3F], [0x4000, 0x4F]映射到[0，0xF]；同时没组内存储16 Byte *16=0xFF Byte，正好填充满一个分组。

### 2.7
双路组相连，总共4096行，共2048个分组。2048 = 2 ** 11  
应该将<14..4>用于hash分组。  
需要17bit空间保持地址。  
节省了15/32 * 100%。

### 2.8
散列算法与组内的行数无关，256 = 2 ** 8, 512 = 2 ** 9，因此使用<16..8>地址位进行modulo hash。

### 2.9
4字节行：  
2KB / 4B/line = 512 line  
4 = 2 ** 2  
512 = 2 ** 9  
因此tag需要32-(2+9) = 21bit  
额外需要 1 bit 有效位
因为写直通，不需要dirty bit  
因此总共需要 22bit * 512 + 2KB = 11KB + 2 KB = 13KB  

32字节行：
2KB / 32B/line = 64 line
32 = 2 ** 5  
64 = 2 ** 6  
tag需要32-(5+6) = 21bit  
控制位需要1 bit，同上  
总共需要 22bit * 64 + 2KB = 11/8KB + 2KB = 3.375KB  

显然32Byte的成本更低，需要的bit位少。但是每次加载写回需要写32字节数据，cache line少，冲突几率更大，性能会比4Byte的差。

### 2.10
写回和写直通是没有差别的，因为这里只讲了对字节数组进行拷贝，没有提有没有额外的其他操作。因该使用写分配，这样在对新复制的字符串就不需要额外的加载操作了。

### 2.11
16Byte：  
8KB / 16 / 2 = 256 group  
sizeof(struct ...) = 4 + 16 + 4 + 4 = 28B  
而代码里只操作了value字段，偏移为20（假设编译器不进行字段位置调整）
20 > 16 每次都会从内存中加载数据。  
sum写可以认为没有影响。

256Byte：
如果一次加载256字节 比 16次加载16字节性能好的化， 这种性能更高。
sum写会导致颠簸现象的出现。


### 2.12
一次设置cache line大小的数组数据的指令（？）  
无缓存操作，直接写mem。



