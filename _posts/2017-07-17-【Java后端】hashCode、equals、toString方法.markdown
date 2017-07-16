---
layout:     post
title:      "hashCode、equals、toString方法"
subtitle:   ""
date:       2017-07-17 00:33:04
author:     "小白"
tags:
    - Java后端
---
> 摘自：http://blog.csdn.net/jiangwei0910410003/article/details/22739953

# toString() #
首先说这个方法，用于把对象转化为字符串，默认是类名+一串字符，一般对我们而言需要更有价值的打印信息时，就会重写这个方法（例如，一个可序列化的类，可以通过重写toString，使之打印相应的json格式）。

# equals() & hashCode() #
equals()比较两个对象的各个属性值是否相等。hashCode()返回一个对象的哈希值。
    
我们经常需要制定一些自定义规则，用来比较两个对象是否相等，这时重写equals()即可。但是有些对象，例如一些不允许重复元素存在的集合，在执行插入操作时，首先会通过查找新元素的hashCode()是否已存在，来判断是否重复数据，如果重复则该插入无效。这时，我们不仅要重写equals()，还要重写hashCode()。  
    
