---
title: OS Persistence
tags:
  - null
category:
  - 读书笔记
index: true
info: true
date: 2021-08-11 21:49:14
---
<!-- more -->

# 第三十六章 I/O设备

> 一个程序没有任何输入（每次运行结果相同），或者没有任何输出（那运行它干嘛?）都是不合理的

## 系统架构

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815111539.png" alt="image-20210815111539170" style="zoom:50%;" />

>  自上至下越来越慢

## 标准设备

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815112221.png" alt="image-20210815112221496" style="zoom:50%;" />

一个标准设备可以分为两个部分：

- 向系统其他部件展现的硬件接口

用来让系统软件控制硬件的操作，每个设备都有自己的特定接口和对应的协议

- 内部结构

包含接口的实现，简单的设备会用几个芯片，复杂的设备会包含简单的cpu，通用内存，一些特定芯片来实现。

## 标准协议

我们将设备接口简化为三个寄存器：

- 状态寄存器 记录设备状态
- 命令寄存器 通知设备执行具体指令
- 数据寄存器 存放数据

协议可能看起来像这样：

![image-20210815113128491](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815113128.png)

1. 轮询设备（判断是否就绪）
2. 发送数据
3. 写入指令
4. 轮询设备（判断是否完成）

>  这个简单的协议在轮询时浪费了大量CPU时间，如果此时操作系统可以切换下一个就绪进程可以大大提高CPU利用率

## 利用中断减少CPU开销

- CPU向设备发出一个请求，然后让对应进程休眠
- 当设备完成了自身的操作，会产生一个硬件中断，引发CPU跳转执行操作系统预定义好的中断服务例程（ISR）或更为简单的中断处理程序。它们会唤醒等待IO的进程继续执行。

中断允许计算和I/O重叠，提高了CPU利用率：

![image-20210815164021331](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815164021.png)

采用中断：

![image-20210815164037305](/Users/c1eye/Library/Application Support/typora-user-images/image-20210815164037305.png)

> 当设备处理很快时使用轮询效果更好，切换进程开销不小，如果设备很慢时使用中断更合适
>
> 如果设备为止，可以考虑使用混合的方法（两阶段）先轮询，如果设备没有完成就采用中断。
>
> 在服务器中最好也不要使用中断

## 利用DMA进行更高效的数据传送

> 如果让CPU来负责将内存中的数据复制到磁盘上，会浪费时间和算力

![image-20210815165651263](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815165651.png)

- c表示拷贝操作，拷贝完成后CPU才可以处理其他请求

解决这个问题的方案是DMA：

- DMA（Direct Memory Access）
- DMA引擎是系统中的一个特殊设备，可以协调完成内存和设备间的数据传递，不需要CPU介入

![image-20210815171639275](/Users/c1eye/Library/Application Support/typora-user-images/image-20210815171639275.png)

1. 首先操作系统告知DMA数据在内存中的位置，要拷贝的大小以及要拷贝到哪个设备，然后操作系统就可以处理其他请求了。

2. DMA完成任务后会抛出一个中断告知操作系统

## 设备交互的方法

- 使用明确的I/O指令

这种方式相对老一些，这些指令明确规定操作系统将数据发送到特定设备寄存器的方法。

例如x86上的in，out

这些指令通常都是「特权指令」操作系统是唯一可以与设备交互的实体。

- 内存映射

将设备寄存器作为内存地址提供，当需要访问时，操作系统装载或存入到该地址，然后硬件读取或写入到设备上。

> 二者没有明显的优势之分，当今都在使用

## 设备驱动程序

> 如何将每个设备具体的接口抽象为一个通用的操作系统的一部分？

在最底层，操作系统的一部分直到设备如何工作，将这部分软件称为「设备驱动程序」，所有交互细节封装其中。

不足之处在于，通用会让一些有特殊功能的设备无法使用这部分功能。

# 第三十七章 磁盘驱动器

> 持久数据存储的主要形式

## 接口

- 驱动器有大量扇区（512字节快）组成，每个扇区都可以读取或者写入。
- 扇区从0开始编号，因此我们可以将磁盘视为一组扇区，编号就是地址空间

- 对于块的写入是原子的

一些不成文的认识：

- 访问彼此靠近的块比访问相隔远的块快
- 访问连续的块最快

## 基本几何形状

![1](http://c.biancheng.net/cpp/uploads/allimg/140702/1-140F2020643419.jpg)

- 盘片

圆形的坚硬的片状物，通过引入磁性变化永久存储数据。磁盘可能有多个盘片，每个盘面两面都可以存储数据。

即使断电也能持久存储数据位。

- 主轴

所有盘片连接在主轴上，主轴连接电机，以恒定的转速来旋转盘片（通电时）。转速以每分钟转数来测量，典型的数值在7200~15000RPM范围。

- 磁道（track）

数据在扇区的每个同心圆上编码，称之为磁道。一个扇面包含数以千计磁道，数百个磁道只有头发宽。

- 磁头

读写由磁头完成，每个扇面都有一个磁头，磁头连接到磁盘臂上，磁盘臂控制磁头在扇面上的各个磁道移动。

## 简单的磁盘驱动器

先假设我们有这么个磁盘：

- 12个扇区（每个512字节大小）
- 逆时针旋转

![image-20210815220325166](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815220325.png)

### 单磁道延迟：旋转延迟

如果我们想读写某个块必须等待它旋转到磁头下，这就是**旋转延迟**。

如果完整的旋转延迟是R，平均就是R/2，最坏就是旋转方向相反的最近块，几乎完整转完一圈。

### 多磁道：寻道时间

![image-20210815221301436](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815221301.png)

可以看到在有多个磁道的情况下，又多了找到磁道的时间，代价高昂：

1. 得到请求
2. 磁盘臂移动到正确的磁道（这包括加速和减速两个阶段，最后还有停放时间以便确定正确的磁道）
3. 等待旋转延迟
4. 读写数据

### 一些其他细节

- 磁盘偏斜

![](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815222302.png)

磁盘偏斜（disl skew）是指每个磁道开始位置会依次向后一点，这样在磁道切换时可以避免刚好错过的情况。

- 外圈磁道比内圈有更多的扇区

连续的磁道被分为多个区域，每个区域拥有相同的扇区数量

- 磁道缓冲区

就是缓存，通常为8M或16M，读取扇区时可以将数据写入缓存，加快后序响应。

- 后写和至写

将数据放入缓存后回报完成还是写入磁盘之后回报完成？前者称为后写，这会让磁盘驱动器看起来更快，但是可能导致问题。

------

- 量纲分析得出传输512KB时间

![image-20210815230318342](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815230318.png)

## I/O时间

现在可以将I/O时间表示为三个主要部分之和：

![image-20210815230808716](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815230811.png)

比较驱动器用I/O速率更容易：

![image-20210815230931710](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815230931.png)

现在介绍两种常见的工作负载

- 随机工作负载

小（比如4KB），而且随机，常见于DBMS。

- 顺序工作负载

读取连续的大量扇区，不会跳过。

------

![image-20210815232133858](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815232133.png)

两种磁盘，一个是性能高，一个是容量大，具体计算就不算了。

下面是平均寻道时间计算：

![image-20210815232226920](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815232230.png)

## 磁盘调度

> 操作系统的磁盘调度程序将决定调度顺序，在磁盘调度中我们可以估算出每个请求所需的时间。而调度程序会尝试遵循最短任务优先（SJF）原则。

### SSTF:最短寻道时间优先

将请求按磁道远近排列，最近的优先。

![image-20210815233446571](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815233453.png)

- 对于块可以用最近块优先NBF来解决

- 饥饿（纯粹的SSTF将忽略其他磁道的请求）

### 电梯（SCAN、C-SCAN）

按某种顺序来回扫描磁道（比如从外到内再从内到外），如果请求的块包含在扫描的磁道中，下次处理就得等到再次扫描回来的时候。

- 解决了饥饿
- 没有考虑旋转

### SPTF： 最短定位时间优先（SATF）

就是看旋转和寻道哪个更短走哪个

实现比较复杂，一般直接在驱动器内部进行。

> 有时攒一波任务再处理可能性能更好，更多细节看更多论文和书

# 第三十八章 廉价冗余磁盘阵列（RAID）

> 更大，更快，更可靠！

RAID是由多个磁盘，内存（包含易失性和非易失性），一个或多个处理器管理的系统。

- 并行使用多个磁盘可以大大提高性能
- 冗余，可以允许部分磁盘丢失

当接收一个I/O请求时，RAID会在两个不同的磁盘上写入一个相同的块，也就是保存两份。

- 完全透明，也就是直接可以替换不需要改变上层的软件和系统

## 评价RAID的指标

> 评价RAID从三个方面：容量，可靠性，性能

- 单请求延迟

单个IO请求对RAID的延迟

- RAID稳定吞吐量

并发请求的总带宽

> 我们提出一些典型的工作负载

- 顺序

对于阵列的请求是连续的

- 随机

字面那意思

## RAID0级：条带化(striping)

> 以轮转的方式将磁盘阵列的块分布在磁盘上。

![image-20210816222910470](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210816222910.png)

- 同行的大块称为条带

![image-20210816223302638](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210816223302.png)

如果每个块4KB，那这个条带就是32KB。

- 计算RAID映射

![image-20210816223700388](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210816223700.png)

> 对于块大小不为1的，需要在磁盘数上乘块的数量

**关于大块大小**

大块的大小影响并行性和定位时间，更大的块需要更少的定位时间但是降低并行性，反之亦然。

### 评价

- 容量完全利用
- 可靠性最糟，任何故障都会导致数据丢失
- 性能良好

## RAID1级：镜像

> 在系统中为每个块准备多个副本（在不同磁盘）

![image-20210816230318550](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210816230318.png)

读取时可以读取任意副本，写入时必须要更新两个副本数据。

### 分析

-  只能获得一般的容量
- 可以容许一个磁盘故障
- 写入是并行的，所以取决于最慢的那个，比单个写入要慢一些

> 写入副本可能出现更新不一致的问题，解决方案是使用某种预写日志



## RAID4级：通过奇偶校验节省空间

![image-20210817230731619](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210817230732.png)

通过对每个数据添加一个奇偶校验块来保存冗余信息。

![image-20210819000523569](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819000653.png)

对于每一行数据进行异或，得出的结果保存在校验块中，恢复某一处数据时根据异或的性质（相同得0，不同得1）可以恢复数据。

对于一块数据可以将一行每个块的同一位进行异或。

![image-20210819001123826](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819001123.png)

### 分析

- 容量是磁盘数量-1（N-1）
- 只允许一个磁盘故障
- 顺序写入时通过全条带写入可以得到（N-1）* S的带宽

全条带写入是指一次写入整个条带，包括计算异或值

![image-20210819001945257](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819001945.png)

- 随机写入时

![](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819002121.png)

**加法奇偶校验**

并行读取一行的块进行重新计算异或值，问题在于这个方法随着磁盘数目的变化而变化。

**减法奇偶校验**

如果新值覆盖C2，我们获取C2的旧值和异或值，如果C2新旧值相同，校验位不变，否则就得取反

![image-20210819002211116](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819002211.png)

对于块中所有的位进行相同的操作就可以得到新的值。

这需要两次读取和两次写入。

而且如果两个块要更新而不唯一一个条带上，就会因为奇偶校验磁盘不能并行，这也叫小写入问题。

## RAID5级：旋转奇偶校验

![image-20210819002938664](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819002938.png)

原理和4一样，只不过校验块位置会变化如图。

### 分析

- 可以跨请求并行写入
- 除非确定没有小写入，否则完全优于RAID4，RAID4构建简单一点。

## 总结

![image-20210819003308405](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819003308.png)

# 第三十九章 文件和目录

> 如何管理持久存储设备？

## 文件和目录

存储虚拟化有两个关键的抽象

**文件**

- 文件就是一个线性的字节数组，每个字节都可以读取或写入。

- 每个文件都有某种低级名称，通常是某种名数字（inode号）
- 创建文件时实际上做了两件事

1. 创建一个结构（inode），用来跟踪文件的大部分信息，大小，文件块在磁盘上的位置等等
2. 将文件名链接到该文件上，也就是硬链接

**目录**

- 一个目录也有一个低级名字，包含目录下用户可读名字和低级名字的键值对列表
- 目录下的条目指向文件或者其他目录，构成目录树
- 目录层次以某种分隔符分开（UNIX是/）
- 目录和文件可以有相同名称，只要不位于同一层次

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819212031.png" alt="image-20210819212031268" style="zoom:50%;" />

- 空目录实际上包含两个内容，一个是自身引用'.'，一个是父目录引用'..'

- 文件名通常由两部分，一个任意名称和一个类型，但是这是约定而不是强制保证类型

## 关于api

![image-20210819213000573](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819213000.png)

## 读写文件

如下是追踪栈 cat做了什么

![image-20210819215043753](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819215043.png)

![image-20210819215050337](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819215053.png)

- cat以读模式打开文件
- 返回的文件描述符是3，因为每个运行的进程已经打开了三个文件，标准输入输出和标准错误
- 当尝试读取更多内容时发现没有了，read返回0
- 写也差不多

## 读取和写入，不按顺序

> 引入偏移量

![image-20210819223625710](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819223657.png)

- lseek只是改变内存上的值，和磁盘寻道没有关系
- 偏移量更新有两种方式，每次发生N个字节读写时隐式的添加偏移，或者直接指定

> 一些api可以立即写入磁盘而不是在内存中缓冲一段时间，如fsync(）

呃，中文版这部分都是一些api，没啥好记的...mac上可以玩玩dtruss这个命令

------

## 文件删除

之前讲了创建文件实际上是一个结构（inode）和指向他的链接，如果追踪rm指令，会发现删除文件时系统调用时unlink()，也就是取消链接，当一个inode没有任何指向他的链接时文件系统才会释放相关块，真正删除文件。

![image-20210819232553873](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819232553.png)

可以使用stat查看链接数

![image-20210819232240948](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819232241.png)

![image-20210819232250263](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819232250.png)

## 符号链接（软链接）

- 不能硬链接到目录

避免在文件树产生环

- 不能硬链接到其他磁盘分区

inode号只在本文件系统中唯一

![image-20210819232626435](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819232626.png)

看起来没什么不同？

- 符号链接本身实际上是一个不同类型的文件，除了文件和目录外的第三种类型

可以看到目录是d，文件是-，链接是l

![image-20210819232756338](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210819232756.png)

符号链接将指向文件的路径名作为数据，所以如果路径很长文件也会变大

- 有可能导致悬空引用，删除源文件会发生这种情况

## 创建挂载文件系统

略

# 文件系统实现

> 让我们建立模型

首先我们将磁盘分块，比如4KB。

在我们的简单文件系统中，一系列块自然就成了分区。

![image-20210820211651803](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820211651.png)

**要存储什么**

- 用户数据（实际上大多数空间应该是用户数据）

简单起见，我们将如下分配：

![image-20210820212839650](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820212839.png)

- inode

文件系统需要记录元数据，包含文件的数据块，文件的大小，所有者和访问权限，修改时间等等。为了存储这些信息，文件系统通常需要存储inode结构。

![image-20210820213306016](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820213306.png)

inode不太大，能分配多少个inode就意味着能拥有的最大文件数量。

- 分配结构

我们当然需要直到哪些块已经分配而哪些没有。

一种流行的结构是「位图」：

包含数据位图和inode位图

![image-20210820213929731](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820213943.png)

- 超级块

剩下的部分留给超级块，用来保存特定文件系统的信息，诸如有多少inode块和数据块等等。在挂在文件系统时，首先读取超级块，初始化各种参数，添加到文件树中。

![image-20210820214319093](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820214319.png)

## inode

> 是index node的缩写

- 每个inode有一个inumber隐式引用，这个数字我们之前叫它低级名称

在一个简单的文件系统中，给定一个inumber是可以直接计算出磁盘中相应节点的位置。

![image-20210820215926106](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820215926.png)

![image-20210820220009387](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820220009.png)

**inode包含如下信息**：

- 文件类型，大小，分配的块数
- 文件访问权限
- 时间信息
- 磁盘上的位置信息

称之为元数据。

![image-20210820221111016](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820221111.png)

### 多级索引

如果要指向的磁盘块太多，文件系统有时会使用一个中间块，这个块包含更多指针（位于数据区），间接指向文件。

![image-20210820221726768](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820221726.png)

- 或者使用范围来确定，但需要连续分配

![image-20210820221939290](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820221939.png)

## 目录组织

>  一个目录基本只包含inode号和文件名

![image-20210820222431322](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820222431.png)

- 包含.和..
- 一些长度信息
- 被删除的文件已某种方法表示
- 同样存储在数据块中，inode表中标记目录类型，所以不需要特殊结构

- 可能拥有任何合理的数据结构，比如B树

## 空闲空间管理

- 位图
- 预处理
- 分配给inode，标记为使用

## 访问路径

> 打开文件需要找到inode，文件系统必须遍历路径名

- 遍历从根目录开始，根目录的inode保存在超级块这样的众所周知的地方
- 然后就可以在文件树中搜索了
- 读取每层目录都需要访问一个inode，对于长路径需要读取很多块才能找到。每次都要查询inode和读取。

![image-20210820223748825](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820223748.png)

## 写入磁盘

可能稍微麻烦点

- 检查位图找到可用块
- 更新位图
- 读取inode找到文件树中的位置
- 写入inode，更新块的位置
- 写入新的块

5次IO！

![image-20210820224428227](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820224428.png)

![image-20210820224437132](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210820224437.png)

## 缓存

>  记录inode和数据

我们已经看到了访问文件的开销，自然就会想要使用缓存来优化它。

- 静态划分

早期的系统固定分配一些内存空间用来缓存

- 动态划分

现代的系统更灵活的分配内存

**延迟写入的好处**

- 延迟写入有时可以直接避免写入（比如创建后又删除）
- 积攒一批IO可以更有效地重排来提高性能

# 第四十一章 局部性和快速文件系统

> 一切为了提高文件系统性能

## FFS（快速文件系统）

**柱面组**

- FFS将磁盘换分为一些分组，称为柱面组

![](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823214622.png)

- 在一组中放置两个文件可以保证磁盘不会花太多时间寻道

- 每个组中都有超级块的一个副本，同样使用位图来分配块

![image-20210823214825322](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823214825.png)

![image-20210823215233484](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823215233.png)

**分配文件和目录**

> 相关的东西放在一起

- 找到分配数量少的柱面组和大量自由inode，将目录数据和inode放入该分组中
- 将同一目录下的文件放在一起（尝试）

**大文件**

将大文件填满一个块组可能破坏文件访问的局部性

![image-20210823220943411](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823220943.png)

- 将一定数量的块分配到第一个块组之后，将下一个大块放入另一个块组中

![image-20210823220951556](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823220951.png)

- 摊销寻道成本，块越大越接近峰值带宽

![image-20210823222023871](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823222023.png)

- FFS使用了inode简洁块来保存大文件

# 第四十二章 崩溃一致性：FSCK和日志

> 如何在断电和系统崩溃的情况下更新持久数据？

