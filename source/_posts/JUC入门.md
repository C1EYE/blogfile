---
title: JUC入门
date: 2021-08-22 17:31:15
tags: - JUC
category: 学习笔记
---

<!-- more -->

# JUC是什么？

- java.util.concurrent并发编程工具类

首先回顾一下相关知识：

1. 进程&线程是什么？

```
进程:
进程是程序的一次执行过程，是程序在执行过程中的分配和管理资源的基本单位，每个进程都有自己的地址空间，线程至少有 5 种状态：初始态、执行态、等待态、就绪态、终止态。

线程：
线程是CPU调度和分派的基本单位，它可以和同一进程下的其他线程共享全部资源

联系：
线程是进程中的一部分，一个进程可以有多个线程，但线程只能存在于一个进程中。

区别：
根本区别：进程是操作系统资源调度的基本单位，线程是任务的调度执行的基本单位
开销方面：进程都有自己的独立数据空间，程序之间的切换开销大；线程也有自己的运行栈和程序计数器，线程间的切换开销较小。
共享空间：进程拥有各自独立的地址空间、资源，所以共享复杂，需要用IPC（Inter-Process Communication，进程间通信），但是同步简单。而线程共享所属进程的资源，因此共享简单，但是同步复杂，需要用加锁等措施。
设计线程的原因：
操作系统模型中，进程有两个功能：

1、任务的调度执行基本单位

2、资源的所有权

线程的出现就是将这两个功能分离开来了：thread 执行任务的调度和执行 ； process 资源所有权

这样的好处是：

操作系统中有两个重要概念：并发和隔离

并发：提高硬件利用率，进程的上下文切换比线程的上下文切换效率低，所以线程可以提高并发的效率

隔离：计算机的资源是共享的，当程序发生奔溃时，需要保证这些资源要被回收，进程的资源是独立的，奔溃时不会影响其他程 序的进行，线程资源是共享的，奔溃时整个进程也会奔溃

线程和并发有关系，进程和隔离有关系
```

2. 线程状态有几种？

```java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

3. wait和sleep的区别？

功能都是暂停当前线程，区别在于：

wait会释放线程获得的锁，而sleep不会。

4. 并发&并行？

真正的多任务还是虚假的多任务

# Lock接口

> 开始学习

## 回顾Synchronized

在多线程使用共享资源的时候， 我们可以使用synchronized来锁定共享资源，使得同一时刻，只有一个线程可以访问和修改它，修改完毕后，其他线程才可以使用。这种方式叫做互斥锁。

当一个共享数据被当前正在访问到线程添加了互斥锁之后，在同一时刻，其他线程只能等待，直到当前线程释放该锁。

synchronized可以添加互斥锁，并且保证被其他线程看到。

**synchronized的三种应用方式**

- 修饰实例方法，作用于当前实例加锁，进入同步代码钱要获得当前实例的锁
- 修饰静态方法，作用于当前类对象加锁，进入同步代码前要获得当前类对象的锁
- 修饰代码块，指定加锁对象，对给定对象加锁，进入同步代码块前要获得给定对象的锁

## 卖票

> 让我们以卖票的例子开始

```java
package com.juc.demo;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class SaleTicket {

    public static void main(String[] args) {
      //开启三个线程
        Ticket ticket = new Ticket();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale2();
            }
        },"thread1").start();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale2();
            }
        },"thread2").start();
        new Thread(() -> {
            for (int i = 0; i < 30; i++) {
                ticket.sale2();
            }
        },"thread3").start();
    }
}
//资源类
class Ticket{
    private int number = 30;
    private Lock lock = new ReentrantLock();
  //使用JUC的lock
     void sale2(){
         lock.lock();
         try {
             if (number>0){
                 System.out.println(Thread.currentThread().getName()+
                         " sold"+number--+" ticket and "+number+"last");
             }
         } catch (Exception e) {
             e.printStackTrace();
         }finally {
             lock.unlock();
         }
     }
  //使用synchronized
     synchronized void sale1(){
         if (number>0){
             System.out.println(Thread.currentThread().getName()+
                     " sold "+number--+" ticket and "+number+"last");
         }
     }
}
```

多线程时一个常见的规范就是使用一个资源类来控制资源，在这里是Ticket，并用两种方法实现了原子性更新。基于锁的实现更灵活，当然也更需要经验。

- 线程操作资源类，高内聚低耦合

# 线程间通信

> 让我看一道面试题

![image-20210822203317933](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210822203318.png)

这就要我们合理进行线程间通信，首先我们看一个更简单的问题：

如何用两个线程交替打印0和1？

```java
public class TwoThread {
    public static void main(String[] args) {
        shareData shareData = new shareData();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    shareData.inc();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    shareData.dec();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

}
//资源类
class shareData{
  //使用一个信号量
    private int number = 0;

    synchronized void inc() throws InterruptedException {
      //通过判断信号量来决定是否执行
        if(number!=0){
            wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"produce one");
        this.notifyAll();
    }

    synchronized void dec() throws InterruptedException {
        if(number==0){
            wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"consume one");
        this.notifyAll();
    }
}

```

我们的代码看起来没什么问题，但实际上如果将线程增加，就会出问题。

问题在哪？

判断信号量的地方应该使用循环而不是单次判断，这样在唤醒之后发现条件不符合才会继续等待。

```java
 synchronized void inc() throws InterruptedException {
      //通过判断信号量来决定是否执行
        while(number!=0){
            wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"produce one");
        this.notifyAll();
    }

    synchronized void dec() throws InterruptedException {
        while(number==0){
            wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"consume one");
        this.notifyAll();
    }
```