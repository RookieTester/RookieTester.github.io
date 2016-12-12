---
layout:     post
title:      "Eclipse更换svn当前登录用户"
subtitle:   ""
date:       2016-09-27 21:42:43
author:     "小白"
tags:
    - Java后端
---

首先，svn的认证信息在C:\Users\xxx\AppData\Roaming\Subversion\auth\svn.simple这个目录下，用记事本或者其它文本编辑器打开，查看svn服务器和对应的用户名，然后把不需要的文件删掉即可。
![](http://chuantu.biz/t5/35/1474939141x1822613219.png)
下次进行svn操作时会提示重新验证身份。