---
layout:     post
title:      "虚拟DNS实现实时更换host"
subtitle:   ""
date:       2017-01-04 22:16:12
author:     "小白"
tags:
    - Java后端
---
起初只是想做到使用Java在运行过程中切换host，刚开始使用HttpClient，用着还挺顺手，后来公司HTTPS迁移，发现很多接口在设置代理ip后无法访问了，输出了响应才明白，原来公司是为域名分发的证书，如果直接用HTTPS访问ip是不可行的。这样可能说不清楚，举个例子：

HTTPS://app.api.com.cn/api/test?a=2直接访问自然是没有问题，有时候我们测试时会有多套环境，这就需要指向对应的ip，直接修改Windows的host文件当然是可行的，但是直接在浏览器中把域名更换为ip就不通了，会提示证书不可信，HTTPS://192.168.100.1/api/test?a=2

其实HttpClient不好使的原因也是如此，这个封装相当于在一个浏览器中把域名更换为ip然后点击访问按钮。

今天偶尔发现一个第三方扩展，可以实现虚拟DNS，我一看就乐了，熟悉网络了肯定明白，域名的解析就是靠DNS这个东东啦。

不啰嗦了，直接上用法，顺便感谢原作者。贴下他们的网站吧，[http://leopard.io/](http://leopard.io/ "http://leopard.io/")。

## 添加maven依赖： ##
```xml
<dependency>
        <groupId>io.leopard</groupId>
        <artifactId>javahost</artifactId>
        <version>0.3-SNAPSHOT</version>
</dependency>
```

## 在resource目录下加入配置文件 ##
就是指定虚拟DNS的映射关系咯。
vdns.properties
<code>
<pre>
app.api.com.cn=192.168.100.1

#多IP设置，域名解析时随机返回其中一个
user.api.com.cn=192.168.100.2,192.168.100.2
</pre>
</code>

## 启动代码 ##
API调用永远是很简单啦，不过有兴趣可以看看内部实现。
方法：
<code>
<pre>
import io.leopard.javahost.JavaHost;

import java.io.IOException;
import java.net.InetAddress;
import java.util.Properties;

import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
import org.springframework.core.io.support.PropertiesLoaderUtils;

public class VirtualDns {

	public static void loadDns() throws IOException {
		Resource resource = new ClassPathResource("/vdns.properties");
		Properties props = PropertiesLoaderUtils.loadProperties(resource);
		JavaHost.updateVirtualDns(props);
	}

}
</pre>
</code>

调用
<code>
<pre>
public class Test {

      public static void main(String[] args) 
        throws Exception {
		VirtualDns.loadDns();
		JavaHost.printAllVirtualDns();// 打印所有虚拟DNS记录
		System.out.println("IP:" + InetAddress.getByName("app.api.com.cn").getHostAddress());// 验证一下解析是否正确
       }

}
</pre>
</code>