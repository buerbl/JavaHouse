### 1 引入  

在Java8里面，ThreadLocal 是一个泛型类。这个类可以提供线程变量。每个线程都有自己的变量。这意味着什么？每一个线程都有自己的资源，就像在现实生活中，每一个程序员都有自己的一个对象，不用去竞争，绝对的线程安全啊。那么 ThreadLocal 究竟怎么用呢？

![黑人问号.png](https://user-gold-cdn.xitu.io/2019/11/26/16ea3730ab0e0fbc?w=500&h=290&f=png&s=213385)

### 2 类的说明

```java
* This class provides thread-local variables.  These variables differ from
* their normal counterparts in that each thread that accesses one (via its
* {@code get} or {@code set} method) has its own, independently initialized
* copy of the variable.  {@code ThreadLocal} instances are typically private
* static fields in classes that wish to associate state with a thread (e.g.,
* a user ID or Transaction ID).
```

这是 ThreadLocal 类上面的说明。大概意思是提供线程变量，通常是被 static fields 修饰。

### 3 创建他

创建 ThreadLocal 有两种方法，一种通过原始的无参构造器， 另一种是使用Java8的 lamaba 表达式。  

#### 3.1  无参构造器

源码  
```java
/**
 * Creates a thread local variable.
 * @see #withInitial(java.util.function.Supplier)
 */
public ThreadLocal() {
}
```

使用并初始化
```java
private static final ThreadLocal<Integer> threadId = new ThreadLocal<Integer>() {
        @Override
        protected Integer initialValue(){
            return 1;
        }
    };
```


#### 3.2 lamaba 表达式

```java

    /**
     * Creates a thread local variable. The initial value of the variable is
     * determined by invoking the {@code get} method on the {@code Supplier}.
     *
     * @param <S> the type of the thread local's value
     * @param supplier the supplier to be used to determine the initial value
     * @return a new thread local variable
     * @throws NullPointerException if the specified supplier is null
     * @since 1.8
     */
    public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {
        return new SuppliedThreadLocal<>(supplier);
    }
```

使用并初始化

```java
private static final ThreadLocal<Integer> threadId = ThreadLocal.withInitial(() -> 1);
```

其实使用如果使用 IDEA 的话， 编译器也会提示可以有 lamaba。 但是看看里面的源码还是挺有意思的。

### 4 getter() 方法

```java
/**
 * Returns the value in the current thread's copy of this
 * thread-local variable.  If the variable has no value for the
 * current thread, it is first initialized to the value returned
 * by an invocation of the {@link #initialValue} method.
 *
 * @return the current thread's value of this thread-local
 */
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```

这里可以看到 Thread.currentThread，获取了当前线程，还有 ThreadLocalMap 这个类,他是一个哈希结构（key-value）。getMap() 方法通过当前线程去获取他。 然后从再通过 this 关键字作为key，得到相应的值value。当然如果为空，就会返回初始化的值。

### 5 setter() 方法

什么情况下不会返回初始化的默认值呢，答案就是调用了setter() 方法。先看源码 
```java
/**
 * Sets the current thread's copy of this thread-local variable
 * to the specified value.  Most subclasses will have no need to
 * override this method, relying solely on the {@link #initialValue}
 * method to set the values of thread-locals.
 *
 * @param value the value to be stored in the current thread's copy of
 *        this thread-local.
 */
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```

大概意思就是将当前线程作为key， 要设的值作为 value 放在 ThreadLocalMap 这个哈希结构中。看到这里，就知道为什么说 ThreadLocal 可以提供线程变量了。他讲每个线程都分开存储，每个线程都有自己的独立资源，不存在资源共享的情况，所以线程安全。

### 6  内存泄漏

每个线程变量都放进一个 ThreadLocalMap 里面，不会有内存问题吗。我截取一部分源码

```java
static class ThreadLocalMap {
    static class Entry extends WeakReference<ThreadLocal<?>> {
        Object value;
        
        Entry(ThreadLocal<?> k, Object v) {
            super(k);
            value = v;
        }
    }
    
}
````

这里可以看到了 WeakReference 这个类，可以知道  ThreadLocalMap 类是一个弱引用，按道理来说，一般执行完线程，就会被虚拟机的垃圾回收机制回收了。但是，真的是这样的吗。如果是线程池环境里面，线程一直存在，那么 ThreadLocal 就会变成起强引用了，不能被回收了。所以有内存泄漏问题。  

那现在就是 remove() 方法出场的时候了。按照字面意思，可以知道，该方法可以清掉线程变量资源，事实也是这样。所以，在程序的最后，最好调用一下 emove() 方法,防止内存泄漏。

### 参考  

《实战Java高并发程序设计》  
ThreadLocal 源码代

