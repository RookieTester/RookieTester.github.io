---
layout:     post
title:      "Linux上用tomcat部署一个Web应用"
subtitle:   ""
date:       2016-11-30 23:46:30
author:     "小白"
tags:
    - Java后端
    - Linux
---

# 一、安装tomcat #
**获取软件更新：**sudo apt-get update
</br>
**安装tomcat7，并以默认配置的tomcat用户运行：**sudo apt-get install tomcat7
</br>
**安装jdk：**sudo apt-get install default-jdk
</br>
**安装ant和git：**sudo apt-get install ant git
</br>
# 二、向Linux服务器传送文件 #
[官网](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下载PSCP工具。添加到windows系统变量中：在path变量中加入pscp.exe的绝对路径即可。
</br>
**基本命令格式：**pscp 文件 用户名@LinuxIP:目录
</br>
然后上传到tomcat的webapps目录就可以了，我的是在/var/lib/tomcat7/webapps/