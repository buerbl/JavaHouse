

>欢迎点赞阅读，一同学习交流，有疑问请留言 。  
>GitHub上也有开源 [JavaHouse](https://github.com/buerbl/JavaHouse) 欢迎star 

### 引入

Object 应该是比较简单的源码了。现在我们来分析一下他。Object 是类层次结构的根。Java体系里面的每个类默认都有一个超类就是 Object。总之，所有对象,包含数组，都默认实现该类的方法。  

### native 关键字

因为 Object 类里面有很多地方都用到 native 关键字。我们先了解一下这家伙。native 关键字是 JNI（Java Native Interface）的重要体现。什么是 JNI ,JNI 是Java调用其他语言（c，c++） 的一种机制。native 关键字修饰的是方法，起声明作用，告诉 JVM 老哥自己去调用这个方法。 这个方法的实现在别的语言那里已经实现，我们是看不到源码的。  

### 初始化  

```java
private static native void registerNatives();
static {
    registerNatives();
}
```  
源码里面一个静态块，一个静态方法和一个没有显示的默认构造方法，没有成员变量。可以看出来 registerNatives() 方法只会被调用一次。

### getClass() 方法  

```java
public final native Class<?> getClass();
```

getClass()方法被native修饰，告诉 JVM 自己去调用。同时被 final 修饰，所以不能被子类重写。主要作用是返回正在运行的类别（Class）。

### hashCode() 方法

```java
public native int hashCode();
```

getClass()方法被native修饰，告诉 JVM 自己去调用,可以被重写。同时被 final 修饰，所以不能被子类重写。该方法主要是返回对象的hashcode，主要是为了一些哈希表的数据结构服务的，比如 HashMap 。  

在 Java 中hancode 与 对象是否相等密切相关。 如果两个对象相等，则 hashcode 一定相等，但是 hashcode 相等，两个对象不一定相等。如果 hashcode 不相等，那么这两个对象一定不相等。

### equals(Object obj)

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

该方法可以被重写，主要用来判断两个对象是否相等。
 
 该方法有一些约定。
 1. 对象任意一个非空对象x， x.equals(x) 返回 true。  
 2. 对象任意两个非空对象 x，y，如果 x.equals(y) 返回true，那么 y.equals(x) 也会返回true， 具有对称性。  
 3. 对象任意三个非空对象 x，y，z，如果 x.equals(y) 返回true， y.equals(z) 返回true， 那么x.equals(z) 返回true， 具有传递性。  
 4. 对象任意两个非空对象 x，y，在什么发生变化的情况写，x.equals(y) 返回总是 true 或者 flase，具有一直性。
 5. 对象任意一个非空对象x， x.equals(null) 返回 false。  
 
Object#equals(Object obj)方法，比较的是内存地址，通常实际应用中我们想比较的是两个对象里面的属性内容是否相等，所以会重写该方法。这里要注意重写 equals(Object obj) 的时候，也要重写 hashCode() 方法。 因为 Java 规定：如果两个对象相等，那么他们的 hashcode 也要相等。举个 Integer 的例子：

```java
@Override
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}

@Override
public int hashCode() {
    return Integer.hashCode(value);
}

public static int hashCode(int value) {
    return value;
}
```


### clone()

```JAVA
protected native Object clone() throws CloneNotSupportedException;
```

该方法被native修饰，告诉 JVM 自己去调用。当我们在自定义类中使用该方法的时候，需要继承一个 Cloneable 接口，否则会抛出无法克隆的异常。该方法是一个浅复制，不是深复制。

浅拷贝：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。  
深拷贝：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。

- 使用代码

```java
@Data
public class Person extends Man implements Cloneable {
    private String name;
    private Person person;

    public static void main(String[] args) throws CloneNotSupportedException {
        Person person = new Person();
        person.setName("chen");
        Person clone = (Person)person.clone();
        System.out.println(clone.toString());
        System.out.println("person.equals(clone):"+person.equals(clone));
        System.out.println("person == clone:"+(person == clone));
        System.out.println("person.person == clone.person:"+(person.person == clone.person));
    }
```

- 结果

```
Person(name=chen, person=null)
person.equals(clone):true
person == clone:false
person.person == clone.person:true
```


### notify()、notifyAll() 和 wait()

```java
public final native void notify();
public final native void notifyAll();
public final void wait() throws InterruptedException {
        wait(0);
    }
```
notify() 随机唤醒一个等待线程，notifyAll() 唤醒全部的等待线程。wait() 方法让当前线程进入等待状态。

![流程图](http://q1rr6rhl9.bkt.clouddn.com/%E6%9C%AA%E5%91%BD%E5%90%8D%E6%96%87%E4%BB%B6_%E7%88%B1%E5%A5%87%E8%89%BA.jpg)



无论当前线程调用哪个方法，都有一个前提：当前线程拥有对象的监视器。实现方法也很简单，配合 synchronized 关键字使用。

- 实现方法 


```java
synchronized (obj) {
    while (true)
        obj.wait();
        // obj.notify();          
}
```

举一个实际的例子

- 多线程打印奇偶数  

```java
public static void main(String[] args) {
        Thread thread = new Thread(() -> {
        for ( int i = 0; i < 100; i += 2) {
            synchronized (a) {
                System.out.println(Thread.currentThread().getName()+":"+i);
                try {
                    a.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        });
        Thread thread1 = new Thread(() -> {
            for (int i = 1; i < 100; i += 2) {
                synchronized (a) {
                    System.out.println(Thread.currentThread().getName()+":"+i);
                    a.notify();
                }
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        thread.start();thread1.start();
    }
```


### finalize()

```java
protected void finalize() throws Throwable { }
```

当垃圾回收器确定不再有对该对象的引用时，由垃圾回收器在对象上调用该方法。该方法只会被调用一次。

### 参考  



如果我的文章帮助到您，可以关注我的微信公众号，第一时间分享文章给您
![公众号.jpg](http://q1rr6rhl9.bkt.clouddn.com/公众号.jpg)