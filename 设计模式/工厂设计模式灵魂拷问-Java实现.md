>show me the code and take to me,做的出来更要说的明白  
>GitHub项目[JavaHouse](https://github.com/buerbl/JavaHouse)同步收录  
>喜欢就点个赞呗!  你的支持是我分享的动力！

### 引入

我们经常听到工厂模式，比如说 Spring 用了工厂模式啦，面试也会被问到工厂模式等等，这见工厂模式是日常开发中比较重要的一种设计模式。其实工厂模式也是比较简单的一种设计模式，这年头似乎不了解工厂模式，你都不好意思说你学过 编程一样（手动狗头）。那么，允许我灵魂拷问一下，你真的了解工厂模式吗。

![](http://javahouse.xyz/20191229095619.png)

### UML类图

不急，我们先看看 UML 类图。学习一下怎么用 UML 类图 表示代码。先看如何表示一个实体类。代码如下:  

```java
public class B {
    
    private String name;
    private String password;
    
    public void test(){
        
    }
}
```

这是一个简单的实体类。他的 看 UML 类图如下：

![](http://javahouse.xyz/20191229100609.png)

看完实体类，我们在来看看接口，代码如下： 

```java
public interface B {
    void test();
}
```
这是一个简单的接类。一般接口就放几个方法，不放属性。他的 看 UML 类图如下：

![](http://javahouse.xyz/20191229100852.png)


看完实体类和接口，我们来看看他们有什么关系吧。先看看继承关系如何表示:

![](http://javahouse.xyz/20191229101248.png)

可以一眼看到他的箭头特点---空心的。箭头指向父类。这就是继承关系表示。

看完继承关系，我们在看看实现接口如何表示。

![](http://javahouse.xyz/20191229101634.png)

这里我们可以看到与继承关系类似，不过实现变成虚线，箭头指向接口类。

完了没有，没有，我们在看看依赖关系，这种关系比较弱，一般是一个类作为另一个类的参数表示， uml 图如下：

![](http://javahouse.xyz/20191229103657.png)

依赖关系是虚线，半个箭头

到这里，以上就是常见的 uml 类图表示。


### 简单工厂

进入正题，什么是工厂模式，我们先看看简单工厂模式，其实简单工厂模式并不是工厂模式，但是他可以帮助我们去了解工厂模式，所以我们先看看简单工厂模式。

需求：我们需要做一个可以加减乘除的计算机，运用到简单工厂模式。

UML 类图：

![](http://javahouse.xyz/20191229144354.png)

运算类：

```java
import lombok.Data;

@Data
public abstract class Operation {
    private Double numA;
    private Double numB;
    /**
     * 运算方法
     */
    abstract Double getResult();
}
```

加法类：

```java
public class OperationAdd extends Operation{

    @Override
    Double getResult() {
        return getNumA() + getNumB();
    }
}
```

乘法类：

```java
public class OperationMul extends Operation{
    @Override
    Double getResult() {
        return getNumA() * getNumB();
    }
}
```

除法类：

```java
public class OperationDiv extends Operation{
    @Override
    Double getResult() {
        if (getNumB() == 0){
            throw new RuntimeException("除数不能为零！");
        }
        return getNumA() / getNumB();
    }
}


```

简单工厂类：

```java
public class CreateOperationFactory {
    public static Operation createOperation(String operate){
        Operation operation = null;
        switch (operate){
            case "+":
                operation = new OperationAdd();
                System.out.println("这是+法");
                break;
            case "/":
                operation = new OperationDiv();
                System.out.println("这是/法");
                break;
            case "*":
                operation = new OperationMul();
                System.out.println("这是*法");
                break;
        }
        return operation;
    }
}

```

测试类：

```java
public class Test {
    public static void main(String[] args) {
        Operation operation = CreateOperationFactory.createOperation("+");
        operation.setNumA(1D);
        operation.setNumB(2D);
        System.out.println(operation.getResult());

        operation = CreateOperationFactory.createOperation("*");
        operation.setNumA(1D);
        operation.setNumB(2D);
        System.out.println(operation.getResult());

        operation = CreateOperationFactory.createOperation("/");
        operation.setNumA(1D);
        operation.setNumB(2D);
        System.out.println(operation.getResult());
    }
}
```

输出接口：

```
这是+法
3.0
这是*法
2.0
这是/法
0.5
```

### 工厂模式

需求：要再添加一个减法运算，嗯？上面没有减法运算吗？我猜你们肯定没有发现上面没有减法运算。看到的也会以为我在偷懒了，蒙混过关呢。事情并没有这么简单。  

一般来说，加个减法嘛，简单，我直接就下 工厂类里面加一个分支不就完事了。哈哈，殊不知，这时候，我们已经违反一个重要的原则--开放关闭原则。所谓开放关闭原则，我们不应该修源码，祖传代码你敢动，试试看，而是通过继承父类或者实现接口去拓展代码，达到我们的目的。

uml 类图：

![](http://javahouse.xyz/20191229144723.png)  

代码如下：

```java
public interface IFactory {
    Operation createOperation(String operate);
}


public class OperationAddFactory implements IFactory{
    @Override
    public Operation createOperation(String operate) {
        Operation operation = new OperationAdd();
        System.out.println("这是+法");
        return operation;
    }
}
```

### 抬杠

没错，这里是自己杠自己的环节。

![](http://javahouse.xyz/20191229135041.png)  

什么工厂模式啊！我自己 ```new``` 一个对应的对象不就解决问题了，为啥要写一个工厂类出来。（杠上杠啊，这是要搞事情）

嗯？好像我无法反驳·····是不存在的（用我第三根手指推一推我的眼镜）。试想一下，一个地方用到就去 ```new``` 一下。我们一下```new```了十几个地方，然后发现要来类名，这时候，怎么办。一处一处改咯，自己```new```的对象，哭着也要改完。但是如果用工厂类的话，我们只需要改一个地方就可以，就是这么简单。

就这一个优点吗？我还是喜欢一个一个```new```啊。

肯定不是（这是一个杠精啊），当然简单一个```new```对象,确实不需要写一个类出来。但是```new```一个对象并不是那么简单，我们需要对象进行一个操作呢。Spring IOC机制默认创建对象是单例的，那么我们现在也需要对象是唯一的怎么办。这时候，我们就是可以在工厂类里面写一个单例模式，达到我们的目的。

以上就是我的抬杠环节。


### 总结

不管什么设计模式，其实就是为了代码复用，少写代码，我们做明白什么模式是什么模式，更要理解这些的作用。正所谓，武术的目的是为了生发，战争的出发点是为了和平。总之，就是知其然知其所以然。

看到这里的都是人才。

### 参考  

https://juejin.im/post/5d318b485188255957377ac3#heading-4  
《大话设计模式》  
https://www.zhihu.com/question/24843188  -- 工厂设计模式有什么用？



### 关注微信公众号，随时移动端阅读

![公众号.jpg](http://javahouse.xyz/20191212204326.png)