---
layout:     post
title:      "死锁、volatile、原子操作"
subtitle:   ""
date:       2016-12-20 23:58:45
author:     "小白"
tags:
    - Java后端
    - 并发编程
---
# 死锁 #
当两个线程/逻辑都在等待对方释放锁的时候，就会造成**死锁**，现象就是程序无法继续执行。

举个例子，张三有一笔巨款，李四有个珍宝，张三必须等李四先把珍宝亲自交由手上才会付款，而李四必须先收到巨款才能奉上珍宝，由于条件限制无法做到同时交钱交货，就会造成双方僵持不下。

# volatile关键字 #
告诉编译器这个变量是易变的、不稳定的，不要试图对该变量进行缓存等优化机制，每次都应从内存地址中读取值。

# 原子操作 #
不会阻塞线程，相当于线程安全的加强版的volatile原子操作。有对基本数据类型的、数组类型的、引用类型的支持：

AtomicInteger、AtomicIntegerArray、AtomicReference、AtomicLongFieldUpdater等。