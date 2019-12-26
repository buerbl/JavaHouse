>show me the code and take to me,做的出来更要说的明白  
>GitHub项目[JavaHouse](https://github.com/buerbl/JavaHouse)同步收录  
>喜欢就点个赞呗!  你的支持是我分享的动力！

### 引入  

反射是一种不安套路处出牌的机制。通常我们接触的方式都是 "正射"，暂且起这个名字。虽然我们用的少，但是反射其实就在我们日常开发中。比如我们常见的空指针异常，或者是 Spring 的各种注解配置创建对象等等。

### 正常操作
```java
ReflectTest reflectTest = new ReflectTest();
reflectTest.setName("boolenbl公众号");
System.out.println(reflectTest.getName());
```

一般我们使用上面方法创建对象以及初始化属性。并且有严格的访问规则。但是反射就反其道为之。他利用 Class、 Method、 Field等类，随意创建对象，获取公有、私有的方法，获取私有的、共有的方法。

### Class 类

Class 类只有私有的构造方法，因此我们不能初始化该对对象，不过 Class 类也不是给我们使用的。 Class 类只能被 JVM 调用。有图有真相：

![](http://javahouse.xyz/20191226174620.png)

### 获取Class 类
有三种方法可以获取到 Class 类。代码如下：
```java
// 方法1
System.out.println(ReflectTest.class);
// 方法2
System.out.println(new ReflectTest().getClass());
// 方法3  包名+类名 （JDBC那种操作） 有异常
System.out.println(Class.forName("基础.ReflectTest"));
// 获取类名
System.out.println(ReflectTest.class.getName());
```

### 获取方法

利用该方法可以无视访问规则,获取所有的方法。代码如下：

```java
System.out.println(Arrays.toString(ReflectTest.class.getDeclaredMethods()));
```

### 获取属性

利用该方法可以无视访问规则,获取所有的属性。代码如下：

```java
System.out.println(Arrays.toString(ReflectTest.class.getDeclaredFields()));
```

### 获取构造器

```java
reflectTestClass.getConstructor();
```
### 从正常到异常（反射操作对象）

```java
Class<ReflectTest> reflectTestClass = ReflectTest.class;
Method method = reflectTestClass.getMethod("setName", String.class);
Constructor<ReflectTest> constructor = reflectTestClass.getConstructor();
reflectTest = constructor.newInstance();
method.invoke(reflectTest, "公众号");
System.out.println(reflectTest.getName());
```

### 完整代码


```java
package 基础;

import lombok.Data;
import org.junit.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.Arrays;

/**
 * @Description: 反射测试
 * @Author: boolean
 * @Date: 2019/12/26 17:01
 */
@Data
public class ReflectTest {
    private String name;
    private String pass;

    @Test
    public void test() throws NoSuchMethodException, ClassNotFoundException, IllegalAccessException, InvocationTargetException, InstantiationException {
        /**
         * 前提获取 Class 类
         */
        // 方法1
        System.out.println(ReflectTest.class);
        // 方法2
        System.out.println(new ReflectTest().getClass());
        // 方法3  包名+类名 （JDBC那种操作） 有异常
        System.out.println(Class.forName("基础.ReflectTest"));
        // 获取类名
        System.out.println(ReflectTest.class.getName());
        // 获取方法
        System.out.println(Arrays.toString(ReflectTest.class.getDeclaredMethods()));
        // 获取属性
        System.out.println(Arrays.toString(ReflectTest.class.getDeclaredFields()));

        // 正常创建对象并初始化
        ReflectTest reflectTest = new ReflectTest();
        reflectTest.setName("boolenbl公众号");
        System.out.println(reflectTest.getName());

        // 反射创建对象并初始化
        Class<ReflectTest> reflectTestClass = ReflectTest.class;
        Constructor<ReflectTest> constructor = reflectTestClass.getConstructor();
        reflectTest = constructor.newInstance();
        Method method = reflectTestClass.getMethod("setName", String.class);
        method.invoke(reflectTest, "公众号");
        System.out.println(reflectTest.getName());
    }

    public void test1(){

    }

    public void test2(){

    }

    public void test3(){

    }
}

```

### 运行结果

```java
class 基础.ReflectTest
class 基础.ReflectTest
class 基础.ReflectTest
基础.ReflectTest
[public boolean 基础.ReflectTest.equals(java.lang.Object), public java.lang.String 基础.ReflectTest.toString(), public int 基础.ReflectTest.hashCode(), public java.lang.String 基础.ReflectTest.getName(), public void 基础.ReflectTest.setName(java.lang.String), public void 基础.ReflectTest.test() throws java.lang.NoSuchMethodException,java.lang.ClassNotFoundException,java.lang.IllegalAccessException,java.lang.reflect.InvocationTargetException,java.lang.InstantiationException, protected boolean 基础.ReflectTest.canEqual(java.lang.Object), public void 基础.ReflectTest.test3(), public void 基础.ReflectTest.test2(), public void 基础.ReflectTest.test1(), public java.lang.String 基础.ReflectTest.getPass(), public void 基础.ReflectTest.setPass(java.lang.String)]
[private java.lang.String 基础.ReflectTest.name, private java.lang.String 基础.ReflectTest.pass]
boolenbl公众号
公众号
```






### 参考
https://www.cnblogs.com/chanshuyi/p/head_first_of_reflection.html  
sczyh30.com/posts/Java/java-reflection-1/  
https://juejin.im/post/598ea9116fb9a03c335a99a4



### 关注微信公众号，随时移动端阅读

![公众号.jpg](http://javahouse.xyz/20191212204326.png)