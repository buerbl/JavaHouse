### 引用  

当开发过程中,我们遇到并发问题。怎么解决？

一种解决方式，简单粗暴：上锁。将千军万马都给拦下来，只允许一个人过独木桥。书面意思就是将并行的程序变成串行的程序。现实的锁有门锁、挂锁和抽屉锁等等。在Java中，我们的锁就是synchronized关键字和Lock接口。

### synchronized关键字

synchronized也叫同步锁，是Java里面的关键字。我们可以猜测到synchronized原理也JVM虚拟机有关联。

synchronized锁的是对象。对象里面有一个叫做监视锁(monitor)的东西，监视锁依赖操作系统的互斥锁（Mutex Lock）。操作系统切换线程其实就是从用户态变成核心态（cpu的两种状态）。这个代价有点高，所以synchronized这个重量级锁后面也引进了偏向锁和轻量级锁。

加锁(监视锁monitor)过程分析()：
1. 当monitor的进入数为0，线程A进入
2. monitor的进入数为1
3. 线程B想进入该monitor就会被阻塞。

线程A可以重复进入该monitor，所以synchronized是可重入锁，和Lock实现的锁一样。

- 程序验证

```java
public class SynchronizedTest {
    private static  int i = 0;
    public static void main(String[] args) {
        test();
    }

    public static void test(){
        synchronized (SynchronizedTest.class){
            synchronized (SynchronizedTest.class){
                i++;
            }
        }
    }
}
```

- 运行结果  
程序正常运行，没有报错

synchronized可以修饰方法以及代码块，代码块就是上面重入锁的例子。

- 修饰方法

```java
public class SynchronizedTest {
    static int n = 100;
    final static CountDownLatch start = new CountDownLatch(n);
    private static  int i = 0;
    public static void main(String[] args) throws InterruptedException {
        for (int j = 0; j < n; j++) {
            Thread thread = new Thread(new addNoSynchronized());
            thread.start();
        }
        start.await();
        System.out.println(i);
    }

    public static class addSynchronized implements Runnable{
        @Override
        public void run() {
            addSynchronized();
        }
        public static synchronized void addSynchronized(){
            for (int j = 0; j < 1000; j++) {
                i++;
            }
            start.countDown();
        }
    }
}

```

- 运行结果

```java
100000
```

如果去掉 synchronized 关键字的话，运行结果大概率不是 100000，因为线程不安全问题。



### Lock接口

一般我们使用 ReentrantLock 类作为重入锁，实现Lock接口。

- 使用方法

```java
public class ReentranLockTest {
    private static int j;
    private static int n = 100;
    private static CountDownLatch latch = new CountDownLatch(n);
    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < n; i++) {
            new Thread(new LockTest()).start();
        }
        latch.await();
        System.out.println("结果为："+j);
    }

    public static class LockTest implements Runnable{
        static Lock lock = new ReentrantLock();
        @Override
        public void run() {
            lockTest();
            latch.countDown();
        }
        private void lockTest() {
            lock.lock();
            try {
                for (int i = 0; i < 1000; i++) {
                        j++;
                }
            }finally {
                lock.unlock();
            }
        }
    }
}
```

- 运行结果

```java
结果为：100000
```

这里我们锁住的 j++ 这块资源区（公共资源），lock 是 static 关键字修饰的，是类对象，思考一下如果不是类对象会怎么样？那就是连环锁了（看图）。

![连环锁.png](http://q1rr6rhl9.bkt.clouddn.com/连环锁.png)

每一个线程都对可以用钥匙解开这把锁，对于程序而言，加锁操作就没有意义了。因为我们需要的是一个锁。



