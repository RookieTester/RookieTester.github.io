---
layout:     post
title:      "个人CentOS上软件安装部署记录"
subtitle:   ""
date:       2016-11-30 23:46:30
author:     "小白"
tags:
    - Java后端
    - Linux
---

# subversion #
**安装**：yum -y install subversion

**目录**：/home/svn

**项目**：touhou-tinder

**启动**：svnserve -d -r /home/svn

**建立项目**：svnadmin create /home/svn/touhou-tinder

**配置文件**：vi /home/svn/touhou-tinder/conf/svnserve.conf
<pre>
anon-access=read
auth-access=write
password-db=passwd
</pre>


**用户配置**：vi /home/svn/touhou-tinder/conf/passwd
<pre>
RookieTester = RookieTester
ycl = ycl
OniGenma = OniGenma
</pre>


svn://139.224.165.210/touhou-tinder


# gitlab #
**安装** https://about.gitlab.com/installation/#centos

**启动postfix时报错，解决方法**

修改etc/postfix/main.cf
<pre>
inet_interfaces=all
</pre>


**配置**：https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/configuration.md

**重启**：sudo gitlab-ctl reconfigure

http://139.224.165.210/gitlab

**网易163邮箱授权码**：zdm1994

使用13269360723@163.com作为邮箱服务器

**启动**：gitlab-ctl start

**停止**：gitlab-ctl stop

# docker #
yum install docker

# netdata #
bash <(curl -Ss https://my-netdata.io/kickstart.sh)

**启动**：netdata

**停止**：killall netdata

# JDK1.8 #
yum -y install java-1.8.0-openjdk*

# Tomcat8 #
/usr/local/tomcat 源码安装

cd /usr/local/tomcat/bin

**启动**：./startup.sh

**关闭**：./stop.sh

端口号12345

# rz和sz #
yum install lrzsz

# Jenkins #
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

yum install jenkins

**端口号8081**

**配置文件**：/etc/sysconfig/jenkins

**启动**：service jenkins start


# mysql #
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

rpm -ivh mysql-community-release-el7-5.noarch.rpm

yum install mysql-community-server

**启动方式**：service mysqld restart

**配置文件**：/etc/my.cnf
<pre>
character_set_server=utf8
</pre>

