## Chapter 1

### 1.1
触发protection trap，导致段错误。

### 1.2
子程序可能递归返回，然后栈空间逐步缩小并最终程序退出，因此此时是可以缩小栈的。  
如果缩小栈的话，释放的空间有可能导致虚拟内存和物理内存以及对应的连接关系被释放，如果程序在这个边界上重复入栈、出栈会导致额外性能开销。如果有额外的物理空间长时间不被使用，系统是可以把他交换的硬盘上，释放出空间给其他进程使用的。

### 1.3
呈线性的父子继承关系。  
C退出，导致各个页面的引用计数减少。

### 1.4
如果使用写时复制，fork期间不分配物理空间。因为有新的内存映射关系产生，可能为新映射关系的保存分配物理页面（？）  
如果不使用写时复制，则需要分配7页：一页数据、三页bss、三页栈，正文是只写的，可以共享不需要复制。

### 1.5
子进程的内存映射表会改写，增加范围映射等，但是不会有物理页面的分配，因为此时没有写物理页面的操作。

### 1.6
read的缓冲区位于子进程的数据段内，因此跟write一样，包含的对页面的写操作，会导致物理页面的复制。

### 1.7
read不会导致页面的赋值，write会。

### 1.8
fork时，共享库的正文段是可以被共享的，数据段依赖于是否使用cow，如果支持，可以增加应用然后等到写时再复制。

### 1.9
应该跟cow无关，内核只是对共享内存增加一次引用。

### 1.10
因为没有写操作，映射关系应该不变。

### 1.11
如果0x12000 & 0x15000 处于同一个物理页面上，此时读出的是0x1234。
如果不处于同一的物理页面上，则读出的值不确定，可能映射上来的不是原物理页，或者原物理页这段时间内被映射给其他进程使用然后释放导致数据覆盖。

### 1.12
对应物理页的引用计数减少。

### 1.13
带有多个线程的一个进程有更多的共享资源，比如完全相同的地址空间和使用的系统资源。
使用共享内存的多个进程仅仅共享“同一片数据”，映射到进程地址空间的地址都可能不同。