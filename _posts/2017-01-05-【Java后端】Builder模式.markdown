---
layout:     post
title:      "Builder模式"
subtitle:   ""
date:       2017-01-05 22:59:34
author:     "小白"
tags:
    - Java后端
    - 设计模式
---
先上代码~
<code>
<pre>
public class People {
    private final String sex;
    private final String name;
    private final int id;
    private final String face;

    public static class Builder {
        //必选参数
        private final String sex;
        private final String name;

        //非必须参数
        private int id = 0;
        private String face = "nice";

        public Builder(String sex, String name) {
            this.sex = sex;
            this.name = name;
        }

        public Builder id(int val) {
            id = val;
            return this;
        }

        public Builder face(String val) {
            face = val;
            return this;
        }

        public People build() {
            return new People(this);
        }
    }

    private People(Builder builder) {
        sex = builder.sex;
        name = builder.name;
        id = builder.id;
        face = builder.face;
    }
}
</pre>
</code>
Builder是一个内部类，将必选参数在有参构造函数中初始化，可以保证新建对象时一定能初始化且该对象是不变的。之后可选参数放在内部类的方法中，注意返回的是对象本身（this），方便继续连接其它方法。

客户端调用举例：
<code>
<pre>
People p=new People.Builder("男","朱东明").id(1).face("凑合").build();
</pre>
</code>

Builder模式的好处：
- 相比多参数的构造函数而言，参数很多时可读性强。
- 相比Javabean而言，可以保证对象的一致性，尤其是多线程的安全性。
- 适合多参数，且有些参数是必选，有些参数是可选的情况。方便客户端调用。

缺点：

性能稍微下降。