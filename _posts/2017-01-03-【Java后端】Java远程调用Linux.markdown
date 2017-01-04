---
layout:     post
title:      "Java远程调用Linux"
subtitle:   ""
date:       2017-01-03 22:05:29
author:     "小白"
tags:
    - Java后端
---
添加依赖：
```xml
<dependency>
    <groupId>ch.ethz.ganymed</groupId>
    <artifactId>ganymed-ssh2</artifactId>
    <version>262</version>
</dependency>
```
示例代码：
```java
public class Test {
    public static void main(String[] args) {

        String hostname = "xxx.xxx.xxx.xxx";
        String username = "zdm";
        String password = "zdm";
        //指明连接主机的IP地址
        Connection conn = new Connection(hostname);
        Session ssh = null;
        try {
            //连接到主机
            conn.connect();
            //使用用户名和密码校验
            boolean isconn = conn.authenticateWithPassword(username, password);
            if (!isconn) {
                System.out.println("用户名或密码不正确");
            } else {
                System.out.println("连接OK");
                ssh = conn.openSession();
                //使用多个命令用分号隔开
                //只允许使用一行命令，即ssh对象只能使用一次execCommand这个方法，多次使用则会出现异常
                ssh.execCommand("pwd;cd /tmp;mkdir hb;ls;ps -ef|grep weblogic");
                //将屏幕上的文字全部打印出来
                InputStream is = new StreamGobbler(ssh.getStdout());
                BufferedReader brs = new BufferedReader(new InputStreamReader(is));
                while (true) {
                    String line = brs.readLine();
                    if (line == null) {
                        break;
                    }
                    System.out.println(line);
                }
            }
            //连接的Session和Connection对象都需要关闭
            ssh.close();
            conn.close();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    }

}
```

 