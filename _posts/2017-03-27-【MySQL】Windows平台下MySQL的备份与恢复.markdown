---
layout:     post
title:      "Windows平台下MySQL的备份与恢复"
subtitle:   ""
date:       2017-03-27 13:58:45
author:     "小白"
tags:
    - MySQL
---
> 在网上查到了三种方式，通过dump备份与还原、xcopy复制数据库文件夹和WinRAR对数据库文件夹定时备份，这里我只使用了前两种，感觉第三种没必要，因为我不喜欢现在的WinRAR（广告弹窗神烦）。

# 一、xcopy命令复制数据库文件夹 #
优势：恢复比较简单。

劣势：可能需要重启数据库服务

## 备份方法 ##
- 建立批处理.bat脚本：
<pre>
::先停止MySQL服务
net stop mysql56

::设置备份文件夹的起名格式
set "Ymd=%date:~,4%%date:~5,2%%date:~8,2%"

::使用xcopy命令进行拷贝(路径仅供参考)
xcopy "C:\ProgramData\MySQL\MySQL Server 5.6\data\数据库名\*.*" "D:\db_backup\%Ymd%\"

::启动MySQL服务
net start mysql56

::可以不要这个，执行完命令以后不会自动关闭
pause
</pre>
注意：mysql56是本机MySQL服务的名字，这个是在安装MySQL server时自定义的，默认是mysql或者mysql+版本号（例如安装的MySQL server版本是5.6，那么服务名很可能默认是mysql56）。


- 加入Windows计划任务

在开始菜单搜索“任务计划程序”，回车确认以呼出窗口。
![](http://upload-images.jianshu.io/upload_images/3071749-6a0f0888cbbd58d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后点击创建任务：
![图片.png](http://upload-images.jianshu.io/upload_images/3071749-25a3e3f3a63bf174.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建一个触发器，用来设置脚本的执行周期，也就是数据库备份的周期：

![图片.png](http://upload-images.jianshu.io/upload_images/3071749-06bd267c4bf13991.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建一个操作：

![图片.png](http://upload-images.jianshu.io/upload_images/3071749-7f573a1a196c96dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
程序或脚本选择批处理脚本的路径，起始于那里填写批处理脚本所在文件夹的路径（**有些人发现批处理单独运行时没问题，放在Windows计划任务里执行就会有错误，就是因为没选择这个**）。

然后一路点击确认即可。

## 恢复方法 ##
到我们的备份目录下瞧一瞧，发现一堆.frm .ibd文件，还有一个db.opt文件，把这些全选，然后覆盖到C:\ProgramData\MySQL\MySQL Server 5.6\data\数据库名\即可。

注意：如果覆盖时提示文件正在使用，说明MySQL服务正在占用这些文件，需要我们先停止服务。

以下是停止MySQL服务的几种方法：
1. 命令行停止
在MySQL的安装路径，这里是C:\Program Files\MySQL\MySQL Server 5.6\bin，按住shift右击鼠标，选择“在此处打开命令窗口”，输入命令停止服务：

```
net stop mysql56
```

2. 任务管理器杀进程

找到mysqld进程，然后结束掉。

3. 服务管理区管理
控制面板——>管理工具——>服务，找到MySQL56这个服务，点击停止按钮。

# 二、通过dump备份与还原 #
优势：速度快，热备份（无需重启MySQL）。

劣势：恢复较为麻烦，且dump的数据可能不完整（失败是不能容忍的哈）。

## 备份方法 ##
- 建立批处理文件
<pre>
::设置时间格式，也就是文件命名方式
set "Ymd=%date:~,4%%date:~5,2%%date:~8,2%"  

::执行dump
mysqldump --opt -u root --password=111111 server > D:/db_backup/bbs_%Ymd%.sql
</pre>

- 加入Windows计划任务

和上面一样，就不说了。

## 恢复方法 ##
```
mysql -uroot -p111111 server < d:\db_backup\server_20170327.sql
```