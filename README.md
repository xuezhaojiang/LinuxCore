# Linux内核分析
薛兆江 + 原创作品转载请注明出处 + [《Linux内核分析》MOOC课程](http://mooc.study.163.com/course/USTC-1000029000)
http://mooc.study.163.com/course/USTC-1000029000     
#### 打开链接的时候，按住ctrl在点击左键，将会在新窗口打开    
## 实验报告列表
### 1.[分析一个简单C程序的汇编代码，理解计算机如何工作](https://xuezhaojiang.github.io/LinuxCore/lab1/lab1.html)     
&nbsp;&nbsp; 汇编语言基础知识、C语言函数调用过程中的堆栈变化和参数传递过程。     
### 2.[完成一个简单的时间片轮转多道程序内核代码](https://xuezhaojiang.github.io/LinuxCore/lab2/lab2.html)    
&nbsp;&nbsp; 模拟存储程序计算机工作模型，时钟中断和进程切换      
### 3.[跟踪分析Linux内核的启动过程](https://xuezhaojiang.github.io/LinuxCore/lab3/lab3.html)     
&nbsp;&nbsp; 源代码主要结构、内核启动及调试方法和start_kernel的解读       
### 4.[使用库函数API和C代码中嵌入汇编代码两种方式使用同一个系统调用](https://xuezhaojiang.github.io/LinuxCore/lab4/lab4.html)     
&nbsp;&nbsp; 对系统调用与用户态、内核态的理解、通过编写汇编代码了解系统调用机制       
### 5.[分析system_call中断处理过程](https://xuezhaojiang.github.io/LinuxCore/lab5/lab5.html)     
&nbsp;&nbsp; 添加一个自己编写的系统调用，加深对系统调用在内核代码中的处理过程的理解。      
### 6.[分析Linux内核创建一个新进程的过程](https://xuezhaojiang.github.io/LinuxCore/lab6/lab6.html)     
&nbsp;&nbsp; 关于进程的描述和创建。主要包括PCB的组织形式、进程的数据结构进程描述符、fork系统调用的关键执行过程。      
### 7.[Linux内核如何装载和启动一个可执行程序](https://xuezhaojiang.github.io/LinuxCore/lab7/lab7.html)     
&nbsp;&nbsp; 静态链接、动态链接（加载时链接、运行时链接）；exec库函数加载可执行文件。   
### 8.[进程调度与进程切换的过程](https://xuezhaojiang.github.io/LinuxCore/lab8/lab8.html)     
&nbsp;&nbsp; 进程调度与进程切换      
     
#### Linux内核,主要学习了进程调度，中断过程和系统调用。可以说，这些都是linux内核中的核心功能。    
####  中断过程 中断可以说是系统中最重要的功能之一，如果没有中断，那么一个操作系统就将退化成一个单道处理系统。中断大概过程就是主动或者被动发起一个中断，一断系统允许中断，那么首先就会保存CS，SS，FLAGS，SS，SP，这些都由硬件来自动保存，接着进入到内核态。然后保存现场，即是保存通用寄存器，堆栈等等。然后，根据中断号来调用相应的中断处理程序，处理完成以后，恢复现场，中断返回。    
#### 系统调用 系统调用就是特殊的中断过程，它一般由程序主动调用。当一个程序调用int ox80时，便会是一个系统调用，首先，通过中断进入内核态，然后根据向量号（保存在eax中）进行相应的分发。它的相应参数都保存在寄存器中（%ebx，%ecx，%edx，%esi，%edi和%ebp），最后，处理完成以后，也会恢复现场，中断返回。     
#### 进程调度 最后，来说一下进程调度。关于进程调度，最重要的便是要理解进程是如何从一个进程切换到另一个进程的。从第一次实验，我们便写了一个模仿进程切换到的例子，它包括保存当前的eip，esp，然后切换到下一个进程的eip和esp，这中间要保证能切换回来。最后一次实验也是分析进程切换的，真实的源码内容多，对比实验一中的简洁版，理解得更清楚。    
#### 最大的遗憾可能就是课程中没有详细地讲讲内存管理以及文件系统。     
    
#### 自己简单补充一些吧，留个基本的概念，也算让整个linux学习完善一些。
###linux文件系统    
#### 文件系统指文件存在的物理空间，linux系统中每个分区都是一个文件系统，都有自己的目录层次结构。linux会将这些分属不同分区的、单独的文件系统按一定的方式形成一个系统的总的目录层次结构。一个操作系统的运行离不开对文件的操作，因此必然要拥有并维护自己的文件系统。    
#### Llinux文件系统使用索引节点来记录文件信息，作用像windows的文件分配表。    
#### 索引节点是一个结构，它包含了一个文件的长度、创建及修改时间、权限、所属关系、磁盘中的位置等信息。一个文件系统维护了一个索引节点的数组，每个文件或目录都与索引节点数组中的唯一一个元素对应。系统给每个索引节点分配了一个号码，也就是该节点在数组中的索引号，称为索引节点号。    
#### linux文件系统将文件索引节点号和文件名同时保存在目录中。所以，目录只是将文件的名称和它的索引节点号结合在一起的一张表，目录中每一对文件名称和索引节点号称为一个连接。    
#### 挂载的概念：当要使用某个设备时，例如要读取硬盘中的一个格式化好的分区、光盘或软件等设备时，必须先把这些设备对应到某个目录上，而这个目录就称为“挂载点（mount point）”，这样才可以读取这些设备，而这些对应的动作就是“挂载”。将物理分区细节屏蔽掉。用户只有统一的逻辑概念。    
#### Linux正统的文件系统(如ext2、3等)将硬盘分区时会划分出目录块、inode Table区块和data block数据区域。一个文件由一个目录项、inode和数据区域块组成。Inode包含文件的属性(如读写属性、owner等，以及指向数据块的指针)，数据区域块则是文件内容。当查看某个文件时，会先从inode table中查出文件属性及数据存放点，再从数据块中读取数据。    
    
    
    
###linux内存管理    
#### Linux将物理地址按4KB的大小划分成“帧（Frame）”。为什么是4KB？因为每一个帧都需要用一个C结构体来描述，称之为“帧描述单元（Frame Discriptor）”，如果太小，帧描述单元显然太多了，如果太大，那么在内存分配时又会造成“内碎片（InnerFragments）”。早些时候，计算机的内存址都是直接映射的，由于程序里的地址是写死的，这就意味着每段程序每次都只能映射对应的地址空间。这无论对程序设计者与系统都是相当大的负担。Linux使用“分段”加“分页”来解决此问题。由于它们的存在，内存地址进入了逻辑地址时代。    
#### 程序里申请的内存的时候，linux内核其实只分配一个虚拟内存（ 线性地址），并没有分配实际的物理内存。只有当程序真正使用这块内存时，才会分配物理内存。这就叫做延迟分配和请页机制。释放内存时，先释放线性区对应的物理内存，然后释放线性区；"请页机制"将物理内存的分配延后了，这样是充分利用了程序的局部性原来，节约内存空间，提高系统吞吐；就是说一个函数可能只在物理内存中呆了一会，用完了就被清除出去了，虽然在虚拟地址空间还在。（不过虚拟地址空间不是事实上的存储，所以只能说这个函数占据了一段虚拟地址空间，当你访问这段地址时，就会产生缺页处理，从交换区把对应的代码搬到物理内存上来）。    
    
    
    
###参考资料    
[Linux文件系统简介](http://www.iteye.com/topic/816268)    
[linux内存管理](http://www.cnblogs.com/autum/archive/2012/10/12/linuxmalloc.html)    