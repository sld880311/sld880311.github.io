---
title: Java并发编程之守护线程
tags:
  - Java
  - 守护线程
categories:
  - [Java]
  - [并发编程]
  - [源码分析]
date: 2021-02-05 19:31:42
---

java中线程的分类：用户线程 (User Thread)、守护线程 (Daemon Thread)。  

1. **守护线程**：在程序运行的时候在后台提供一种通用服务的线程，比如垃圾回收线程就是一个很称职的守护者，并且这种线程并不属于程序中不可或缺的部分。因此，当所有的非守护线程结束时，程序也就终止了，同时会杀死进程中的所有守护线程。反过来说，只要任何非守护线程还在运行，程序就不会终止。  

2. **区别**：用户线程和守护线程两者几乎没有区别，唯一的不同之处就在于虚拟机的离开：如果用户线程已经全部退出运行了，只剩下守护线程存在了，虚拟机也就退出了。 因为没有了被守护者，守护线程也就没有工作可做了，也就没有继续运行程序的必要了。

## Java中的定义

通过Thread中的setDaemon方法完成设置。通过源码分析得知：**当线程只剩下守护线程的时候，JVM就会退出.但是如果还有其他的任意一个用户线程还在，JVM就不会退出**
<!--more-->

```java
/**
* Marks this thread as either a {@linkplain #isDaemon daemon} thread
* or a user thread. The Java Virtual Machine exits when the only
* threads running are all daemon threads.
*
* <p> This method must be invoked before the thread is started.
*
* @param  on
*         if {@code true}, marks this thread as a daemon thread
*
* @throws  IllegalThreadStateException
*          if this thread is {@linkplain #isAlive alive}
*
* @throws  SecurityException
*          if {@link #checkAccess} determines that the current
*          thread cannot modify this thread
*/
public final void setDaemon(boolean on) {
    checkAccess();
    if (isAlive()) {
        throw new IllegalThreadStateException();
    }
    daemon = on;
}
```

## 参考代码

```java
package com.sunld;
/**
 * @Title: MyDaemon.java
 * @Package com.sunld
 * <p>Description:</p>
 * @author sunld
 * @version V1.0.0 
 * <p>CreateDate:2017年10月9日 下午4:09:31</p>
*/

public class MyDaemon implements Runnable{

	public static void main(String[] args) {
		Thread daemonThread = new Thread(new MyDaemon());
		// 设置为守护进程
        daemonThread.setDaemon(true);
        daemonThread.start();
        System.out.println("isDaemon = " + daemonThread.isDaemon());
        //sleep完成之后,main线程结束，JVM退出!
        try {
			Thread.sleep(30000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
        
        //AddShutdownHook方法增加JVM停止时要做处理事件：
        //当JVM退出时，打印JVM Exit语句.
        Runtime.getRuntime().addShutdownHook(new Thread() {
        	@Override
        	public void run() {
        		System.out.println("JVM Exit!");
        	}

        });
	}

	@Override
	public void run() {
		for(int i = 0; i < 10;i++) {
			System.out.println(i+"=====MyDaemon=======");
		}
	}
}
```

## 注意事项

1. thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
2. 在Daemon线程中产生的新线程也是Daemon的。
3. 守护线程应该永远不去访问固有资源，如文件、数据库，因为它会在任何时候甚至在一个操作的中间发生中断。
4. Daemon线程被用作完成支持性工作，但是在Java虚拟机退出时Daemon线程中的finally块并不一定会执行,所以不能依靠finally块中的内容来确保执行关闭或清理资源的逻辑

## JAVA 后台线程总结

1.	定义：“服务线程”，一种后台线程，它有一个特性，即为用户线程提供公共服务，在没有用户线程可服务时会自动离开。 
2.	优先级：守护线程的优先级比较低，用于为系统中的其它对象和线程提供服务。 
3.	设置：通过 setDaemon(true)来设置线程为“守护线程”；将一个用户线程设置为守护线程的方式是在 线程对象创建 之前 用线程对象的 setDaemon 方法。 
4.	在 Daemon 线程中产生的新线程也是 Daemon 的。 
5.	线程则是 JVM 级别的，以 Tomcat 为例，如果你在 Web 应用中启动一个线程，这个线程的生命周期并不会和 Web 应用程序保持同步。也就是说，即使你停止了 Web 应用，这个线程依旧是活跃的。 
6.	example: 垃圾回收线程就是一个经典的守护线程，当我们的程序中不再有任何运行的Thread, 程序就不会再产生垃圾，垃圾回收器也就无事可做，所以当垃圾回收线程是 JVM 上仅剩的线程时，垃圾回收线程会自动离开。它始终在低级别的状态中运行，用于实时监控和管理系统中的可回收资源。 
7.	生命周期：守护进程（Daemon）是运行在后台的一种特殊进程。它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。也就是说守护线程不依赖于终端，但是依赖于系统，与系统“同生共死”。当 JVM 中所有的线程都是守护线程的时候，JVM 就可以退出了；如果还有一个或以上的非守护线程则 JVM 不会退出。 


## 参考

1. 《Java并发编程的艺术》
2. [Java中的多线程你只要看这一篇就够了](https://www.cnblogs.com/wxd0108/p/5479442.html)
3. [线程](https://baike.baidu.com/item/%E7%BA%BF%E7%A8%8B/103101?fr=aladdin)
4. [Java总结篇系列：Java多线程（一）](https://www.cnblogs.com/lwbqqyumidi/p/3804883.html)
5. 《java并发编程实战》
6. [多线程01：《疯狂Java讲义》学习笔记——线程概述](https://blog.csdn.net/hanhaiyinheguxing/article/details/51366541)
7. [java并发编程---如何创建线程以及Thread类的使用](https://blog.csdn.net/hla199106/article/details/47840505)
8. [Java并发编程：Thread类的使用](https://www.cnblogs.com/dolphin0520/p/3920357.html)
9. [Java中断机制](https://www.cnblogs.com/loveer/p/11518402.html)
10. [JAVA多线程之中断机制(如何处理中断？)](https://www.cnblogs.com/hapjin/p/5450779.html)
