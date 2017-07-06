---
layout:     post
title:      "Java类加载机制"
subtitle:   ""
date:       2017-07-06 23:18:21
author:     "小白"
tags:
    - Java后端
---
> 摘自：http://wingjay.com/2017/05/08/java_classloader/

# 类加载机制简述 #
Java虚拟机一般使用Java类的流程为：首先将开发者编写的Java源代码（.java 文件）编译成Java字节码（.class 文件），然后类加载器会读取这个.class文件，并转换成java.lang.Class的实例。有了该Class实例后，Java虚拟机可以利用newInstance之类的方法创建其真正对象了。

ClassLoader是Java提供的类加载器，绝大多数的类加载器都继承自ClassLoader，它们被用来加载不同来源的Class文件。    

# class文件有哪些来源 #   
1. 开发者编写的类；
2. 有Java内部自带的核心类，如java.lang、java.math、java.io等 package内部的类，位于$JAVA_HOME/jre/lib/目录下；
3. 还有 Java 核心扩展类，位于$JAVA_HOME/jre/lib/ext目录下。开发者也可以把自己编写的类打包成jar文件放入该目录下；
4. 最后还有一种，是动态加载远程的.class文件。
    
# classloader分类 #   
实际上，针对上面四种来源的类，分别有不同的加载器负责加载。

首先，我们来看级别最高的Java核心类 ，即$JAVA_HOME/jre/lib里的核心jar文件。这些类是Java运行的基础类，由一个名为BootstrapClassLoader加载器负责加载，它也被称作根加载器／引导加载器。注意，BootstrapClassLoader比较特殊，它不继承ClassLoader，而是由JVM内部实现；

然后，需要加载Java核心扩展类 ，即$JAVA_HOME/jre/lib/ext目录下的jar文件。这些文件由ExtensionClassLoader负责加载，它也被称作扩展类加载器。当然，用户如果把自己开发的jar文件放在这个目录，也会被ExtClassLoader加载；

接下来是开发者在项目中编写的类，这些文件将由AppClassLoader加载器进行加载，它也被称作系统类加载器 System ClassLoader；

最后，如果想远程加载如（本地文件／网络下载）的方式，则必须要自己自定义一个ClassLoader，复写其中的findClass()方法才能得以实现。 

# 双亲委托 #
我们知道了不同类型的类会由哪种classloader来加载。那么考虑下，如果我们伪造了一个String类，JVM会加载错吗？答案当然是不会的。

当查找一个类时，优先遍历最高级别的Java核心类，然后再去遍历Java核心扩展类，最后再遍历用户自定义类，而且这个遍历过程是一旦找到就立即停止遍历。

每次需要加载一个类，先获取一个系统加载器AppClassLoader的实例（ClassLoader.getSystemClassLoader()），然后向上级层层请求，由最上级优先去加载，如果上级觉得这些类不属于核心类，就可以下放到各子级负责人去自行加载。这就是双亲委托。
    
    
    

    
    
    
