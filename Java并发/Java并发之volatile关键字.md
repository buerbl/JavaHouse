引言

说到多线程,我觉得我们最重要的是要理解一个临界区概念。

黑人问号.png

举个例子，一个班上1个女孩子（临界区），49个男孩子（线程），男孩子的目标就是这一个女孩子，就是会有竞争关系（线程安全问题）。推广到实际场景，例如对一个数相加或者相减等等情形，因为操作对象就只有一个，在多线程环境下，就会产生线程安全问题。理解临界区概念，我们对多线程问题可以有一个好意识。

Jav内存模型（JMM)

谈到多线程就应该了解一下Java内存模型（JMM）的抽象示意图.下图：
JMM.png
线程A和线程B执行的是时候，会去读取共享变量（临界区），然后各自拷贝一份回到自己的本地内存，执行后续操作。
JMM模型是一种规范，就像Java的接口一样。JMM会涉及到三个问题：原子性，可见性，有序性。 所谓原子性。就是说一个线程的执行会不会被其他线程影响的。他是不可中断的。举个例子:

int i=1
这个语句在Jmm中就是原子性的。无论是一个线程执行还是多个线程执行这个语句，读出来的i就是等于1。那什么是非原子性呢，按道理如果Java的代码都是原子性，应该就不会有线程问题了啊。其实JMM这是规定某些语句是原子性罢了。举个非原子性例子：

i ++;
这个操作就不是原子性的了。因为他就是包含了三个操作:第一读取i的值，第二将i加上1，第三将结果赋值回来给i，更新i的值。

所谓可见性。可见性表示如果一个值在线程A修改了，线程B就会马上知道这个结果。

所谓有序性。所谓有序性值的是语意的有序性。就是说代码顺序可能会发生变化。因为有一个指令重排机制。所谓指令重排，他会改变代码执行顺序，为了让cpu执行效率更高。为了防止重排序出错，JMM有个happen-before规则，这个规则限制了那些语句执行在前，那些语句执行在后。

Happen-before：
程序顺序原则：一个线程内保证语义的串行性
volatile原则：volatile变量的写发生在读之前
锁规则：先加锁再解锁 传递性：a先于b，b先于c，则a必定先于c
线程的start方法先于他的每一个操作
线程所有的操作先于线程的终结
对象的构造函数执行、结束先于finalize()方法。

volatile

进入正题，volatile可以保证变量（临界区）的可见性以及有序性，但是不能保证原子性。举个例子：

public class VolatileTest implements Runnable{
    private static VolatileTest volatileTest = new VolatileTest();
    private  static volatile int i= 0;
    public static void main(String[] args) throws InterruptedException {
        for (int j = 0; j < 20; j++) {
            Thread a = new Thread(new VolatileTest());
            Thread b = new Thread(new VolatileTest());
            a.start();b.start();
            a.join();b.join();
            System.out.print(i+"&&");
        }

    }
    
    @Override
    public void run() {
        for (int j = 0; j < 1000; j++) {
            i++;
        }
    }

}

// 输出结果
// 2000&&4000&&5852&&7852&&9852&&11852&&13655&&15655&&17655&&19655&&21306     
//&&22566&&24566&&26189&&28189&&30189&&32189&&34189&&36189&&38089&&
有结果看到有问题，虽然i已经添加了volatile关键字，说明volatile关键字不能保证i++的原子性。

那什么场景适合使用volatile关键字

轻量级的“读-写锁”策略
private volatile int value;
public int getValue(){ return value;}
public synchronized void doubleValue(){ value = value*value; }
2.单例模式（双检查锁机制

private volatile static Singleton instace;   
public static Singleton getInstance(){  // 没有使用同步方法，而是同步方法块
    //第一次null检查 ，利用volatile的线程间可见性，不需要加锁，性能提高    
    if(instance == null){            
        synchronized(Singleton.class) {    //锁住类对象，阻塞其他线程
            //第二次null检查，以保证不会创建重复的实例       
            if(instance == null){       
                instance = new Singleton(); // 禁止重排序
            }  
        }           
    }  
    return instance;
参考

《现代操作系统（第三版）中文版》
《实战Java高并发程序设计》
《Java并发编程的艺术》

如果我的文章帮助到您，可以关注我的微信公众号，分享文章给您

MOMlcV.gif