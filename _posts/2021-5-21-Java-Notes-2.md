---
layout:     post   				    # 使用的布局（不需要改）
title:      Java Notes(2)		# 标题 
subtitle:   线程，进程，协程；Java内存模型(JMM)；Java的线程实现 #副标题
date:       2021-05-21			# 时间
author:     Leo 						# 作者
header-img: img/post-bg-java.png	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Programming Language
    - Java
---

# 1 进程，线程，协程

## 1.1 进程
  
进程就是正在运行的应用程序。保存在磁盘上的程序运行以后，会在内存空间里形成一个独立的内存体，这个内存体有自己独立的地址空间，有自己的堆。进程是**资源分配的最小单位** 。操作系统会以每个进程为单位分配系统资源（CPU时间片，内存等资源）。

## 1.2 线程
  
线程是**CPU分配和调度的最小单位**。

- 线程的并发与并行
  
  并发是指多个线程交替进行。并行是指多个线程同时执行。并行意味着并发，但并发并不一定意味着并行，尤其是运行在单核CPU上时。

  单核多线程：单核CPU轮流执行多个线程，通过对每个线程分配CPU时间来实现，只能是并发的。

  多核多线程：可把多线程分配给不同的核心处理，其他的线程依旧等待，是并行执行。

- 线程状态
  
  ![](/img/post-img/post-Java-Notes-2/1.png)

  
## 1.3 进程与线程的区别与联系
    
- 区别：
  - 调度：线程作为调度和分配的基本单位，进程作为拥有资源的基本单位。

  - 并发性：不仅进程之间可以并发执行，同一个进程的多个线程之间也可并发执行。

  - 拥有资源：进程是拥有资源的一个独立单位，线程不拥有系统资源，但可以访问隶属于进程的资源。

  - 系统开销：在创建或撤消进程时，由于系统都要为之分配和回收资源，导致系统的开销明显大于创建或撤消线程时的开销。但是进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个进程死掉就等于所有的线程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。

- 联系：

  - 一个线程只能属于一个进程，而一个进程可以有多个线程，但至少有一个线程；

  - 资源分配给进程，同一进程的所有线程共享该进程的所有资源；

  - 处理机分给线程，即真正在处理机上运行的是线程；

  - 线程在执行过程中，需要协作同步。不同进程的线程间要利用消息通信的办法实现同步。


## 1.4 协程

- 概念
  
  协程是一种基于线程之上，更轻量级的存在，一个线程可以拥有多个协程。协程完全由程序所控制，也就是在用户态执行，对于内核是不可见的。

- 原理 
  
  子程序，或者称为函数，在所有语言中都是层级调用，比如A调用B，B在执行过程中又调用了C，C执行完毕返回，B执行完毕返回，最后是A执行完毕。所以子程序调用是通过栈实现的，一个线程就是执行一个子程序。子程序调用总是一个入口，一次返回，调用顺序是明确的。而协程的调用和子程序不同。

  协程在子程序内部是可中断的，然后转而执行别的子程序，在适当的时候再返回来接着执行。

- 协程的优势
    
    **极高的执行效率**：线程的切换由操作系统来负责调度，而协程的切换完全由程序自己控制，减少了上下文切换的开销，极大提高了执行效率。线程的阻塞状态是由操作系统内核来进行切换，发生在内核态上，而协程的暂停完全由程序控制，发生在用户态上。


    **占用更少的资源**：在jdk 1.5+版本中，一个线程默认占用的内存是1MB，而在GoLang中一个协程（go routine）平均占用的内存只有2.5KB。Go从语言层面原生支持协程，所以说Go原生支持高并发。
    
    
    **不需要多线程的锁机制**：因为只有一个线程，也不存在同时写变量冲突，在协程中**控制共享资源不加锁**，所以执行效率比多线程高很多。


## 2 Java内存模型（JMM）

### 2.1 硬件内存模型

![](/img/post-img/post-Java-Notes-2/5.png)

现代计算机一般都有2个以上CPU，而且每个CPU还有可能包含多个核心。因此，如果我们的应用是多线程的话，这些线程可能会在各个CPU核心中并行运行。

在CPU内部有一组CPU寄存器，也就是CPU的储存器。CPU操作寄存器的速度要比操作计算机主存快的多。在主存和CPU寄存器之间还存在一个CPU缓存，CPU操作CPU缓存的速度快于主存但慢于CPU寄存器。某些CPU可能有多个缓存层（一级缓存和二级缓存）。计算机的主存也称作RAM，所有的CPU都能够访问主存，而且主存比上面提到的缓存和寄存器大很多。

当cpu需要访问内存的时候，他会先读取一部分主内存到cache中，甚至，会读取一部分cache到内部的寄存器中，然后再在寄存器进行计算操作。当cpu将计算结果写回内存中时，他会flush寄存器和cache中的数据，然后将值写回至内存中。这样CPU就不需要等待缓慢的内存读写。

当cpu要求cache去存储其他内容时，也会将cache中的内容flush到内存中。cpu的cache可以边写入一部分数据到内存，边写入一部分到自己cache中，所以在更新数据，不必要全部清空cache，可以边读边写。

### 2.2 Java内存模型

![](/img/post-img/post-Java-Notes-2/4.png)
![](/img/post-img/post-Java-Notes-2/2.png)

jvm定义的一套java内存模型为了能够跨平台达到一致的内存访问效果，从而屏蔽掉了各种硬件和操作系统的内存访问差异。这点和c和c++并不一样，C和C++会直接使用物理硬件和操作系统的内存模型来处理，所以在各个平台上会有差异，这一点java不会。

java的内存模型规定了所有的变量都存储在主内存中，每个线程拥有自己的工作内存，工作内存保存了该线程使用到的变量的主内存拷贝，线程对变量所有操作，读取，赋值，都必须在工作内存中进行，不能直接写主内存变量，线程间变量值的传递均需要主内存来完成。

**JVM内部对Java内存模型的实现：**

![](/img/post-img/post-Java-Notes-2/7.png)

在JVM内部，Java内存模型把内存分成了两部分：线程栈区和堆区。

JVM中运行的每个线程都拥有自己的线程栈，线程栈包含了当前线程执行的方法调用相关信息，我们也把它称作调用栈。随着代码的不断执行，调用栈会不断变化。

线程栈还包含了当前方法的所有本地变量信息。一个线程只能读取自己的线程栈，也就是说，线程中的本地变量对其它线程是不可见的。即使两个线程执行的是同一段代码，它们也会各自在自己的线程栈中创建本地变量，因此，每个线程中的本地变量都会有自己的版本。

所有原始类型(boolean,byte,short,char,int,long,float,double)的本地变量都直接保存在线程栈当中，对于它们的值各个线程之间都是独立的。对于原始类型的本地变量，一个线程可以传递一个副本给另一个线程，当它们之间是无法共享的。

堆区包含了Java应用创建的所有对象信息，不管对象是哪个线程创建的，其中的对象包括原始类型的封装类（如Byte、Integer、Long等等）。不管对象是属于一个成员变量还是方法中的本地变量，它都会被存储在堆区。


### 2.3 Java内存模型和硬件内存模型的联系

Java内存模型和硬件内存模型是不同的。硬件内存模型不区分堆和栈。在硬件层面，所有的线程栈和堆都被存储在主内存中，一部分线程栈和堆可能有时候会出现在cpu cache中和cpu寄存器中。
![](/img/post-img/post-Java-Notes-2/6.png)

### 2.4 内存交互

![](/img/post-img/post-Java-Notes-2/3.png)

lock: 作用于主内存的变量，把变量标识为线程独占的状态
unlock: 与lock对应，把主内存中处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。

read: 作用于主内存的变量，把一个变量的值从主内存传输到线程的工作内存，便于随后的load使用。

load：作用于工作内存的变量，把read读取到的变量放入工作内存副本。

use： 作用于工作内存，把工作内存的变量值传递给执行引擎，每当虚拟机遇到一个需要使用到变量的值的字节码指令时将会执行这个操作。

assign: 作用于工作内存，把执行引擎收到的值赋给工作内存的变量，虚拟机遇到赋值字节码时候执行这个操作。

store：作用于工作内存，把变量的值传输到住内存中，以便随后的write使用。

write：作用于主内存，把store操作从工作内存得到的值放入主内存的变量中。

每一个操作都是**原子**的，不可再分的。

## 3 Java中的线程实现

继承Thread类，重写run()方法；实现Runnable接口，重写run()方法；实现Callable接口，重写call()方法，这种实现方式有返回值，且可以抛出异常；

下面是代码演示：

```Java
public class Thread1 extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("This is thread 1");
        }
    }
}

public class Thread2 implements  Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("This is thread 2");
        }
    }
}

import java.util.concurrent.Callable;

public class Thread3 implements Callable {
    @Override
    public Integer call() throws Exception {
        for (int i = 0; i < 10; i++) {
            System.out.println("This is thread 3");
        }
        return 1111;
    }
}

import java.util.concurrent.FutureTask;
import java.util.concurrent.ExecutionException;

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread1();

        Thread2 thread2 = new Thread2();
        Thread t2 = new Thread(thread2);

        Thread3 thread3 = new Thread3();
        //执行 Callable 方式，需要 FutureTask 实现类的支持，用于接收运算结果。
        FutureTask<Integer> result = new FutureTask<>(thread3);
        Thread t3 = new Thread(result);

        t1.start();
        t2.start();
        t3.start();

        //获取t3的返回值
        try {
            Integer res = result.get();  
            System.out.println(res);
            System.out.println("-----t3 end------");
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

- 继承Thread类和实现Runnable接口这两种方式的区别

1、Thread类实现了Runnable接口，这两种方式都需要重写run()方法。

2、实现Runnable的类更具有健壮性，避免了单继承的局限。

3、Runnable更容易实现资源共享，能多个线程同时处理一个资源（当多个线程同时处理一个资源时，需要注意线程安全）。继承Thread类的线程只能启动一次。



