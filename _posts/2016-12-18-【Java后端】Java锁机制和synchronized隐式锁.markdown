---
layout:     post
title:      "Java锁机制与synchronized隐式锁"
subtitle:   ""
date:       2016-12-18 23:11:59
author:     "小白"
tags:
    - Java后端
    - 并发编程
---
# 锁机制 #
一段多线程代码在执行的过程中，在操纵对象的地方，必须有个执行的顺序。每一个对象对应一个锁，线程要想对该对象进行操作，首先要获得这个锁，如果执行过程中锁被其它线程拿走，就会进入阻塞状态；又或者线程顺利执行完毕后，会把锁归还对象，此时线程池中的某个线程就可以获取该锁并执行相应的代码了。

# synchronized隐式锁 #
在方法或者代码块前声明关键字synchronized，就可以起到同步的作用，该部分会隐式地声明一个锁，所有线程必须获得该锁才能进行。**但是只有在对象锁是共享且唯一时才会起到同步作用。**

> 在java虚拟机中，每个对象和类在逻辑上都是和一个监视器相关联的。
> 对于对象来说，相关联的监视器保护对象的实例变量。对于类来说，监视器保护类的类变量。（如果一个对象没有实例变量，或者一个类没有变量，相关联的监视器就什么也不监视）。
> 
> 为了实现监视器的排他性监视能力，java虚拟机为每一个对象和类都关联一个锁。代表任何时候只允许一个线程拥有的特权。线程访问实例变量或者类变量不需锁。但是如果线程获取了锁，那么在它释放这个锁之前，就没有其他线程可以获取同样数据的锁了。（锁住一个对象就是获取对象相关联的监视器）。类锁实际上用对象锁来实现。当虚拟机装载一个class文件的时候，它就会创建一个java.lang.Class类的实例。当锁住一个对象的时候，实际上锁住的是那个类的Class对象。
> 
> 一个线程可以多次对同一个对象上锁。对于每一个对象，java虚拟机维护一个加锁计数器，线程每获得一次该对象，计数器就加1，每释放一次，计数器就减 1，当计数器值为0时，锁就被完全释放了。
> 
> java编程人员不需要自己动手加锁，对象锁是java虚拟机内部使用的。在java程序中，只需要使用synchronized块或者synchronized方法就可以标志一个监视区域。当每次进入一个监视区域时，java 虚拟机都会自动锁上对象或者类。

# 实例分析： #
**例一**

```java
package com.server.example.util.thread.multi;

/**
 * Created by Administrator on 2016/12/18.
 */
public class SynchronizedDemo extends Thread {

    private int threadNo;

    public SynchronizedDemo(int threadNo) {
        this.threadNo = threadNo;
    }

    public static void main(String[] args) throws Exception {
        for (int i = 1; i < 10; i++) {
            new SynchronizedDemo(i).start();
            Thread.sleep(1);
        }
    }

    @Override
    public synchronized void run() {
        for (int i = 1; i < 10000; i++) {
            System.out.println("No." + threadNo + ":" + i);
        }
    }


}
```
在这里，我们在run()方法前加了synchronized，本意是希望这段代码能实现同步报数。但是需要注意的是，**类的成员方法如果声明为synchronized，则该锁对应的对象就是该类的对象，这里每个线程都新建了一个对象，所以持有的是不同的锁，自然也起不到同步的作用。**

**例二**

```java
    private int threadNo;
    private String lock;

    public SynchronizedDemo(int threadNo, String lock) {
        this.threadNo = threadNo;
        this.lock = lock;
    }

    public static void main(String[] args) throws Exception {
        String lock = new String("lock");
        for (int i = 1; i < 10; i++) {
            new SynchronizedDemo(i, lock).start();
            Thread.sleep(1);
        }
    }
    
    @Override
    public void run() {
        synchronized (lock) {
            for (int i = 1; i < 10000; i++) {
                System.out.println("No." + threadNo + ":" + i);
            }
        }
    }
```

还是刚才那个类，这次可以看到我们在启动线程前新建一个String对象lock，并且该对象作为构造参数，用于所有线程的启动。由于多线程共用这个对象，所以这次起到了同步的作用。

**例三**

```java
private int threadNo;
    private String lock;

    public SynchronizedDemo(int threadNo) {
        this.threadNo = threadNo;
    }

    public static void main(String[] args) throws Exception {
        for (int i = 1; i < 20; i++) {
            new SynchronizedDemo(i).start();
            Thread.sleep(1);
        }
    }

    public static synchronized void execute(int threadNo) {
        for (int i = 1; i < 10000; i++) {
            System.out.println("No." + threadNo + ":" + i);
        }
    }

    @Override
    public void run() {
        execute(threadNo);
    }
```
这里通过在run方法中调用本线程中一个静态的同步方法execute而实现了线程的同步。

我们知道，对于同步静态方法，对象锁就是该静态方法所在的类的Class实例，由于在JVM中，所有被加载的类都有唯一的类对象，具体到本例，就是唯一的 SynchronizedDemo.class对象。**不管我们创建了该类的多少实例，但是它（静态方法）的类实例仍然是一个。**

最后总结一下：


- 对于同步的方法或者代码块来说，必须获得对象锁才能够进入同步方法或者代码块进行操作；
- 如果采用method级别的同步，则对象锁即为method所在的对象，如果是静态方法，对象锁即指method所在的
Class对象(唯一)；
- 最重要的一点就是，要想真正达到同步，需要对象锁是共享且唯一的。




