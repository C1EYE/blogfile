---
title: OS Concurrent
date: 2021-07-27 23:17:40
tags:
- OS
category: 读书笔记
---

并发

<!-- more -->

# 第二十六章 并发：介绍

**线程**

一个多线程程序有多个执行点（多个程序计数器，每个都用于取指令和执行）。

每个线程看起来像是一个独立的进程，区别在于**线程共享地址空间**，因此可以访问到相同的数据。

**线程拥有自己的程序计数器PC和一组寄存器**，因此线程间切换必然会发生**上下文切换**。

对于进程，我们将状态保存到PCB进程控制块（内存上），而对于线程同样需要保存状态，保存到TCB线程控制块。但是**地址空间不变，也就是说不需要切换页表。**

另一个区别在于栈，如下所示：

![image-20210728203824188](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728203824.png)

**每个线程都有一个栈**，所有位于栈上的变量，参数，返回值和其他什么东西都被放在线程本地存储的地方，即相关线程的栈。

## 线程创建

> 一个c线程demo

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728222831.png" alt="image-20210728222831594" style="zoom:50%;" />

线程创建后的执行顺序并非是先创建先执行，而是取决于调度程序

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728222911.png" alt="image-20210728222911779" style="zoom:50%;" />

线程可以独立于调用者运行。

## 共享数据

> 当线程间共享数据并且有修改时就会发生问题

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728223946.png" alt="image-20210728223946770" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728224006.png" alt="image-20210728224006934" style="zoom:50%;" />

## 核心问题：不可控调度

事实上，为了将某个变量的值加1，编译器会生成不止一条汇编指令。

比如在x86中：

![image-20210728224143696](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728224143.png)

首先将对应的变量值移入寄存器，然后加一，然后再赋值给原来的变量。

然而，如果在第二条指令之后时钟中断发生，寄存器里的信息被保存到TCB，另一个线程同样获取这个变量，此时获取到的还是未增加的值，执行完后原来的线程会将这次增加覆盖掉，导致丢失了一次增加。

这种情况称为**竞态条件**（race condition），结果是不确定的，由于这种代码访问共享变量，被称为临界区，临界区代码一定不能被多个线程同时执行。

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210728225913.png" alt="image-20210728225913228" style="zoom:50%;" />

我们需要的是互斥，即只能有一个线程在临界区执行。

## 原子性愿望

- 超级指令？

不，理智的指令集不会提供这样的指令。

我们需要的是一些硬件提供的有用的指令，在这之上构建一个集合，即同步原语（synchronization primitive）。

# 第二十七章 线程APi

## 创建

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729201450.png" alt="image-20210729201450140" style="zoom:50%;" />

- 第一个参数是与线程交互的结构体指针，第二个是指定线程属性的结构体，第三个指定线程开始的函数指针，第四个是传递的参数。具体RTM

<img src="/Users/c1eye/Library/Application Support/typora-user-images/image-20210729201919995.png" alt="image-20210729201919995" style="zoom:50%;" />

- 等待线程完成

![image-20210729202359733](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729202434.png)

> 不要返回指向栈内存的指针！

## 锁

![image-20210729205417089](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205417.png)

![image-20210729205429738](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205429.png)

如果锁没有被其他程序获取，就获取锁，否则等待

- 锁需要被初始化，静态和动态

![image-20210729205504497](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205504.png)

- 两个其他版本，失败返回或者固定时间返回
![image-20210729205610296](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205610.png)

## 条件变量

> 线程间通信

![image-20210729205857052](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205857.png)

- 第一个使调用线程进入休眠

![image-20210729205921381](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729205921.png)

- 唤醒线程的代码

![image-20210729210223668](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729210223.png)

> 不要这么做！

![image-20210729210431597](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729210431.png)

- 自旋浪费CPU
- 出错可能性大

# 第二十八章 锁

> 在临界区代码加锁可以保证指令像原子指令一样执行

![image-20210729212524789](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729212524.png)

- 锁就是一个变量，声明时要指定类型
- 锁保存了某一刻的状态，可用表示没有线程持有锁，被占用表示有线程在临界区持有锁
- 锁的持有者释放锁后，锁就可用了

- 通常会用不同的锁保护不同的数据和结构，以允许更多线程进入临界区

## 评价锁

- 互斥

最基本的，锁是否有效，能够阻止多个线程进入临界区

- 公平性

当锁可用时，是否每一个竞争线程都有公平的机会获得锁？

- 性能

使用锁的时间开销，在不同场景

## 控制中断

最早的互斥解决方案之一，在临界区关闭时钟中断。

![image-20210729213840021](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729213840.png)

- 简单（这是优点）
- 这要求允许线程执行特权操作，而且恶意程序会开始时调用锁独占CPU
- 不支持多处理器
- 可能导致中断丢失
- 效率低

> 某些时候操作系统可能会采用这种方式，毕竟操作系统内部没有信任问题...

## 自旋锁：测试并设置指令（原子交换）

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729215550.png" alt="image-20210729215550654" style="zoom:67%;" />

用一个变量标志锁是否被占用，这里是1表示占用。

然而，通过一个不合时宜的中断，会出现下面的情况：

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729215651.png" alt="image-20210729215651712" style="zoom:50%;" />

两个线程都进入临界区，没有满足互斥。而且自旋性能太低。

> 这时就要用到原子交换指令了

**测试并设置（test-and-set）**

也叫原子交换指令，他做了以下事情：

![image-20210729220804565](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729220804.png)

获取旧值，赋新的值，而且是原子的。

基于这个指令实现的自旋锁如下：

![image-20210729220916207](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210729220916.png)

- 当锁未被持有时值为0，原子交换获得0，赋值1，结束循环
- 这时其他线程获取锁会得到1，并且赋值1，会自旋
- 释放锁时赋值0

**这就是自旋锁（spin lock）！一种简单的锁，利用CPU周期，直到锁可用。**

显然只能那个在抢占式调度中使用，因为自旋线程不会放弃CPU。

评价：

- 自旋锁提供互斥
- 不保证公平，可能会有线程饿死
- 在多核CPU表现不错

## 比较并交换

这个指令的伪代码看起来像这样：

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801191509.png" alt="image-20210801191509238" style="zoom:50%;" />

检测ptr指向的值是否与expected相等，如果相等，更新，否则什么也不做。

可以这样实现锁：

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801191959.png" alt="image-20210801191959448" style="zoom:50%;" />

## 链接的加载和条件是存储指令

![image-20210801192657903](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801192658.png)

条件式存储只有上一次加载的地址在期间未被更新时才会存储成功。

由此实现自旋锁

![image-20210801192826281](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801192826.png)

## 获取并增加

这个指令原子的返回特定的地址的旧值，并让该值自增1。

![image-20210801194312018](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801194312.png)

这个锁的实现基于获取并增加，它可以保证每个线程都能抢到锁，只要获得ticket就一定会调度。

## 避免自旋

> 如何避免自旋？这会浪费整个时间片

**简单方法**

![image-20210801202350858](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801202350.png)

这种方法，我们调用yield（假设存在这么个原语）主动放弃CPU来取消调度自身。

比原来的要好一些，节省一些自旋的时间。但是切换上下文的成本还是实实在在的，线程还有可能饿死，需要更好的方法。

**使用队列，休眠代替自旋**

我们引入一个队列来保存等待锁的进程，并用一些操作系统api。

![image-20210801210447352](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801210447.png)

- guard起到自旋锁的作用
- 如果线程不能获得锁，就加入队列
- 归还锁时从队列唤醒线程

> 在park调用之前如果切换到另一个线程并释放了锁，这个线程有可能永远休眠下去
>
> 这称为「唤醒/等待竞争」

为了避免上述情况，需要第三个系统调用“setpark”。

通过这个调用，表示马上要park，如果这是其他线程unpark，park就会直接返回。

![image-20210801211734685](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210801211734.png)

或者直接将gurad传入内核，保证原子性。

# 基于锁的并发数据结构

> 对于特定的数据结构如何加锁？

## 并发计数器

![image-20210802205734408](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802205734.png)

一个带锁的简单计数器，如果并发会导致运行速度太慢就需要优化，否则就不用。

如果多线程运行的像单线程一样快就称为完美扩展。

**懒惰计数器**

- 每个CPU有一个局部计数器，还有一个全局计数器，每个计数器都有自己的锁
- 线程获取局部的锁，增加对应核心的局部计数器，局部计数器定期获得全局计数器的锁更新全局计数器并将局部计数器置零

这种做法有一个决定局部转全局的阈值，越大则扩展性越强（越接近单线程的速度），但是全局和局部的差值越大。反之，扩展性越趋近于非扩展计数器。

![image-20210802211209857](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802211209.png)

提高阈值性能好，降低阈值准确度高，懒惰计数器在准确性和性能中折中

![image-20210802211302064](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802211302.png)

一个实现

![image-20210802211240374](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802211240.png)

## 并发链表

![image-20210802213236120](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802213236.png)

在临界区代码加锁，就得到了一个并发链表。

这个链表扩展性并不好...

**过手锁（锁耦合）**

- 每个节点都有一个锁，遍历时获取下一个节点的锁。

这个方案由于每个节点加锁，开销巨大，很难比单锁快。或许有某种复合的方式会更好...

## 并发队列

![image-20210802220037868](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802220037.png)

- 两个锁分别负责列头和列尾，使出队入队可以并发进行

## 并发散列表

![image-20210802221103223](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802221103.png)

- 使用之前的并发链表实现桶，扩展性良好

![image-20210802221241410](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802221241.png)

> 通常都是从一个大锁开始再优化

# 条件变量

> 线程需要检查某一条件满足之后才会继续运行，比如父线程检查子线程是否执行完毕

![image-20210802222158287](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802222158.png)

我们期望能看到：

![image-20210802222401863](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802222401.png)

可以使用共享变量，自旋检查，当然效率极其低下。

![image-20210802222339201](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802222339.png)

**条件变量**

线程使用条件变量等待一个条件成真。条件变量是一个显式队列，当某些条件不满足时，线程将自己加入队列，等待条件。在另外的线程中如果改变了条件，就可以唤醒一个或多个等待线程。

![image-20210802224159200](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210802224159.png)

- 使用done变量
- 改变信号要加锁

## 生产者/消费者 （有界缓冲区）问题

假设有一个或多个生产者线程和一个或多个消费者线程。

生产者把生成的数据放入缓冲区，消费者从缓冲区取走数据，以某种方式消费。

> 比如HTTP服务器将请求放入队列等待处理

这种缓冲区必须要用同步机制访问，以避免产生竞态条件。

![image-20210803204727690](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803204727.png)

- 生产者消费者队列第一版（有问题）

![image-20210803210835609](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803210835.png)

- 如果只有一对生产者和消费者是没问题，但是如果有两个消费者会有可能出现一个消费者抢先消费了缓冲区导致另一个无法消费的情况。

![image-20210803210959831](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803211004.png)

原因在于，Tc1被唤醒后在运行之前，缓冲区状态改变。这里的信号只表示状态发生变化，但不保证在运行前不变，也就是Mesa语义。

另一种是Hoare语义，会保证被唤醒的线程立即执行。

**使用while代替if**

修复上述问题的简单方案就是使用while代替if

![image-20210803221111818](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803221111.png)

> 基于Mesa语义，我们需要记住一个简单的规则：总是使用while循环

此时还有一个问题，消费者线程在唤醒时可能唤醒其他消费者（按理说他应该唤醒生产者）而导致所有线程全部睡眠。

![image-20210803222000932](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803222017.png)

> 信号必须更有指向性，消费者不应该唤醒消费者（反之亦然）。

**单值缓冲区的生产者/消费者方案**

使用两个条件变量，而不是一个。

![image-20210803222251027](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803222257.png)

现在生产者等待empty，发信号给fill，消费者相反。这样就不会唤醒错误的对象。

## 最终的生产者/消费者方案

>  增加更多的缓冲区，支持并发生产消费

![image-20210803223600597](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803223603.png)

**覆盖条件**

> 当程序只有改成广播时才能够工作，很可能程序有缺陷

考虑这种情况：

两个线程请求分配内存，一个请求多一些比如100，一个少一些比如10。它们现在都因为内存不足而休眠等待。

现在有个线程释放了50的空间，这时他应该唤醒哪个线程？如果唤醒了请求100的线程，程序就无法正常工作，这时只能广播，这种情况被称为覆盖条件。

# 第三十一章 信号量

>  信号量是有一个整数值的对象，可以用两个函数来操作它

POSIX标准中，是sem_wait() 和sem_post()

信号量的初始值决定其行为，因此我们首先初始化信号量。

![image-20210803232146614](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803232146.png)

![image-20210803232631647](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803232631.png)

- 调用wait时信号量-1，如果信号量大于等于0直接返回，否则挂起。
- 调用post时直接让信号量+1，如果有等待的线程，唤醒其中一个

- 信号量为负数表示等待的线程个数

## **二值信号量**（锁）

初始化信号量为1

![image-20210803233206823](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803233206.png)

![image-20210803233241532](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803233241.png)

因为锁只有两个状态，所以叫做**二值信号量**

## 信号量用作条件变量

> 信号量也可以用在让一个线程暂停执行，等待某一条件成立。

![image-20210803235141270](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803235141.png)

这里是创建子线程的一个程序，信号量初始化为0

![image-20210803235243187](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210803235243.png)

## 生产者/消费者（有界缓冲区）问题

 ![image-20210809232531607](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210809232531.png)

![image-20210809232542792](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210809232542.png)

- 在临界区加锁，使用一个二值信号量即可

## 读者-写者锁

![image-20210809234358552](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210809234358.png)

第一个读者获取锁时同时会获取写锁，其他读者可以获取读锁，当所有读者结束后等待的写者才可以获取锁。

- 这种实现写者很容易饿死，应该在有写者等待时避免更多读者

## 哲学家就餐问题

> 实用性不强，但是很出名

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210809235606.png" alt="image-20210809235606389" style="zoom:50%;" />

每位哲学家有时思考（不用餐具）有时吃饭（需要左右手餐具），如何设计函数提高并发而且没有死锁和饿死。看起来像下面这样：

![image-20210810000149806](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810000149.png)

- 辅助函数

![image-20210810000120793](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810000120.png)

![image-20210810000737726](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810000737.png)

- 为避免死锁，有一个哲学家的获取餐具顺序不同（也可以是某些）

> 诸如此类问题有很多，吸烟者，理发师什么的

## 实现信号量

> 使用底层同步原语实现自己的信号量

![image-20210810001753660](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810001753.png)

# 第三十二章 常见并发问题

![image-20210810002616940](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810002617.png)

## 非死锁缺陷

