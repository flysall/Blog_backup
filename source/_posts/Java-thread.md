---
title: java线程及线程安全性
date: 2017-08-28 15:28:31
tags: Java
---

前段时间写了一个java爬虫，借着这个机会我再次学习了线程，对线程的使用熟悉了不少，下面简单谈谈.  
<!-- more -->

## 进程与线程的区别
在操作系统中，存在两种基本的执行单元：进程和线程。当我们写的程序进入内存中运行时，就成了我们所说的进程。对单核CPU来说，某一时刻只能执行一个进程。但CPU可以保存当前进程的状态并快速切换到其它进程，使得系统中的每一个进程都得到执行的机会，这涉及到进程的调度算法来实现并发执行。由于CPU的切换进程的速度超过了人类的感官，所以我们打开多个程序时仿佛不同的程序可以同时执行，然而这只是假象。  
而线程可以理解为一种更为轻量的进程。它是进程的组成部分，一个线程必须拥有属于它的父进程，而进程则可以拥有多个线程。  进程与线程的主要差异有如下几点：1.不同的进程之间不共享资源，而同一个进程下的线程共享父进程的资源，通过共享资源从而可以实现线程间的通信。2.创建线程的成本要远低于进程，所以我们更愿意用线程来实现程序的并发执行，因为它的性能要比进程高。此外，线程之间的切换是由操作系统底层来实现的，所以存在一定的不可控性。

## 用java实现线程的常见手段
想要创建线程，首先需要明确所有的线程对象都必须是`Thread`或者`Thread`子类的实例。第一种方法就是直接继承`Thread`类并重写它的`run()`方法，`run()`方法即为我们希望该线程执行的任务。第二种方法就是通过一个实现`Runnable`或者`Callable`接口的类，并把它作为`Thread`构造器的参数。最后一种常见的方法则是使用线程池来实现多线程。值得注意的是我们不能直接调用`Thrad.run()`，这样java将不会把它看做一个线程而只是一个普通的类，正确的姿势是使用`Thread.start()`来调用该线程。参考如下：
```
class A implements Runnable{
	run(){
		//线程执行体
	}
	public static void main(String[] args){
		new Thread(new A()).start();
	}
}
```

## 多线程带来的问题
使用多线程虽然可以提高程序的性能，但如果没有引入某些机制，则有可能导致线程之间的冲突。

### 线程干扰

看一个小例子
```
class Count{
	private int a;
	public void increase(){
		a++;
	}
	public void decrease(){
		a--;
	}
}
```
当两个线程作用于同一个数据，则两个线程则可能发生干扰。在这个例子中，我们来看一下`a++`这个简单的语句。在计算机底层，它将分为三个步骤才能执行完成：  

1. 读取a的当前值。  
2. 对读取到的值加1。  
3. 将增加的值存回a。

对a--也是同理。
假如有一个线程A调用调用`increase()`方法， 另一个线程B调用`decrease()`方法，`a`初始值为0，由于系统底层对线程之间的切换则可能出现以下的执行顺序：

1. 线程A：检索a
2. 线程B：检索a
3. 线程A：增加检索值; 结果是1
4. 线程B：减去检索值; 结果为-1
5. 线程A：将结果存储在a中; a现在是1
6. 线程B：存储结果a; a现在是-1

很不幸，线程A的执行结果丢失，被线程B覆盖，显然这不是我们所希望看到的，我们的初衷是A和B交替执行，使得`a`最后为0。

### 内存一致性错误
其实这个问题我认为和上面提到的线程干扰区别不是很大，只是同一问题的不同表现形式。还是可以看上面的例子，因为线程A对`a`加1，并回存，最后被线程B给覆盖掉了，导致线程A对`a`的修改对线程B不可见。换而言之，线程B没有做到在线程A对`a`修改并存回后才对`a`进行读取并操作，从而引发了内存一致性错误。

## 解决之道
不管怎么看，让线程肆意妄为是极为不妥的，我们需要引入相应的机制来解决上面出现的两个问题，java中提供了能满足我们需求的办法，常见的有同步机制(synchonized)和锁机制(Lock),其本质都是一样的，通过保证对象或者变量被线程A占用时，其他线程将不能对该对象或者变量进行访问并修改，直到线程A放弃对它们的占用。关于同步和锁就不展开谈了，以后有机会再写。
