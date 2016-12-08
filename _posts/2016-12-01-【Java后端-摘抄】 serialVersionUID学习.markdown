#【Java后端-摘抄】 serialVersionUID学习 #
## Intellij IDEA 自动生成 serialVersionUID ##
Setting->Inspections->Serialization issues->Serializable class without ’serialVersionUID’ 
</br>
![](http://static.oschina.net/uploads/img/201407/12170417_rxRm.jpg)
选中以上后，在你的class中：光标定位在类名，按 Alt+Enter 就会提示自动创建 serialVersionUID 了。

## serialVersionUID作用 ##
serialVersionUID适用于Java的序列化机制。简单来说，Java的序列化机制是通过判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常，即是InvalidCastException。
</br>
serialVersionUID有两种显示的生成方式：        
一是默认的1L，比如：private static final long serialVersionUID = 1L;        
二是根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段，比如：        
private static final  long   serialVersionUID = xxxxL;

当实现java.io.Serializable接口的类没有显式地定义一个serialVersionUID变量时候，Java序列化机制会根据编译的Class自动生成一个serialVersionUID作序列化版本比较用，这种情况下，如果Class文件(类名，方法明等)没有发生变化(增加空格，换行，增加注释等等)，就算再编译多次，serialVersionUID也不会变化的。

如果我们不希望通过编译来强制划分软件版本，即实现序列化接口的实体能够兼容先前版本，就需要显式地定义一个名为serialVersionUID，类型为long的变量，不修改这个变量值的序列化实体都可以相互进行串行化和反串行化。

下面用代码说明一下serialVersionUID在应用中常见的几种情况。

（1）序列化实体类

import java.io.Serializable;

public class Person implements Serializable

{
    private static final long serialVersionUID = 1234567890L;
    public int id;
    public String name;
 
    public Person(int id, String name)
    {
        this.id = id;
        this.name = name;
    }
 
    public String toString()
    {
        return "Person: " + id + " " + name;
    }
}

（2）序列化功能：

	
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
 
public class SerialTest
{
 
    public static void main(String[] args) throws IOException
    {
        Person person = new Person(1234, "wang");
        System.out.println("Person Serial" + person);
        FileOutputStream fos = new FileOutputStream("Person.txt");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(person);
        oos.flush();
        oos.close();
    }
}

（3）反序列化功能：

	
import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
public class DeserialTest
{
    public static void main(String[] args) throws IOException, ClassNotFoundException
    {
        Person person;
 
        FileInputStream fis = new FileInputStream("Person.txt");
        ObjectInputStream ois = new ObjectInputStream(fis);
        person = (Person) ois.readObject();
        ois.close();
        System.out.println("Person Deserial" + person);
    }
 
}

情况一：假设Person类序列化之后，从A端传输到B端，然后在B端进行反序列化。在序列化Person和反序列化Person的时候，A端和B端都需要存在一个相同的类。如果两处的serialVersionUID不一致，会产生什么错误呢?
【答案】可以利用上面的代码做个试验来验证：
先执行测试类SerialTest，生成序列化文件，代表A端序列化后的文件，然后修改serialVersion值，再执行测试类DeserialTest，代表B端使用不同serialVersion的类去反序列化，结果报错:
Exception in thread "main" java.io.InvalidClassException: test.Person; local class incompatible: stream classdesc serialVersionUID = 1234567890, local class serialVersionUID = 123456789
    at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:560)
    at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1580)
    at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1493)
    at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1729)
    at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1326)
    at java.io.ObjectInputStream.readObject(ObjectInputStream.java:348)
    at test.DeserialTest.main(DeserialTest.java:15)
    
情况二：假设两处serialVersionUID一致，如果A端增加一个字段，然后序列化，而B端不变，然后反序列化，会是什么情况呢?
【答案】新增 public int age; 执行SerialTest，生成序列化文件，代表A端。删除 public int age，反序列化，代表B端，最后的结果为：执行序列化，反序列化正常，但是A端增加的字段丢失(被B端忽略)。

情况三：假设两处serialVersionUID一致，如果B端减少一个字段，A端不变，会是什么情况呢?
【答案】序列化，反序列化正常，B端字段少于A端，A端多的字段值丢失(被B端忽略)。

情况四：假设两处serialVersionUID一致，如果B端增加一个字段，A端不变，会是什么情况呢?
验证过程如下：
先执行SerialTest，然后在实体类Person增加一个字段age，如下所示，再执行测试类DeserialTest.
	
import java.io.Serializable;
public class Person implements Serializable
{
    private static final long serialVersionUID = 123456789L;
    public int id;
    public String name;
    public int age;
 
    public Person(int id, String name)
    {
        this.id = id;
        this.name = name;
    }
 
    public String toString()
    {
        return "Person: " + id + " " + name;
    }
}

相应的修改测试类DeserialTest，打印出age的值。
	
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
 
public class SerialTest
{
 
    public static void main(String[] args) throws IOException
    {
        Person person = new Person(1234, "wang");
        System.out.println("Person Serial" + person + " age:" + person.age);
        FileOutputStream fos = new FileOutputStream("Person.txt");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(person);
        oos.flush();
        oos.close();
    }
}

结果为：
Person Deserial Person: 1234 wang age: 0
说明序列化，反序列化正常，B端新增加的int字段被赋予了默认值0。
最后通过下面的图片，总结一下上面的几种情况。
