>show me the code and take to me,做的出来更要说的明白   
>喜欢就点个赞呗!  你的支持是我分享的动力！

## 引入  

是否有遇到看不懂身边同事代码的情况，是否有被面试官问到 Java 新特性不懂的情况。我掐指一算你大概是遇到的了 Lambda 表达式 和 Stream 流。为了解决上述情况，我特地献上一份关于 Lambda 和 Stream 的指南，以解燃煤之急。

## Lambda

### 普通调用
首先我们先看第一个例子。

```java

@Test
public void test(){
        test1(1);
    }

private void test1(int a){
    System.out.println(a);
}
```

运行Test，输出结果

```java
1
```

相信这里大家都很清楚，我不需要多废话！

### 内部类调用
那我们在来看看第二个例子

```java
public interface IPerson {
    void personMethod();
}

private void test2(IPerson person){
        person.personMethod();
    }

@Test
public void test2(){
    test2(new IPerson(){
        @Override
        public void personMethod() {
            System.out.println(1);
        }
    });
}
```
运行Test，输出结果
```
1
```
这里的话我将参变成了一个接口，使用了内部类。大家是不是觉得这种形式的代码不容易看清楚，一个参数竟然放了这么多东西，真是让人头大啊。所以我们有了 Lambda 表达式。

### Lambda 重构
我们使用 Lambda 表达式重写上面代码。得到下面新代码：

```java
public interface IPerson {
    void personMethod();
}

private void test2(IPerson person){
        person.personMethod();
    }

@Test
public void test2(){
    test2(() -> System.out.println(1));
}
```

运行Test，输出结果
```
1
```

这样的代码是不是比使用内部类代码优雅了很多，看起来舒服极了，关键代码也少了。这就是 Lambda 表达式的魅力。


### Lambda 调用
好了，经过上面的层层递进的例子，我们引出了 Lambda 表达式。现在我们开始了解 Lambda 表达式的语法。

语法:

```java
-> 
```
是的，他的调用语法就是一个箭头。当然这样说的话我也不太信。其实还没说完。

当我们的方法没有参数的时候，没有返回，他需要保留括号，形式如下：

```java
() -> System.out.println(1);
```

当有一个参数的时候，没有返回，小括号可以去掉，形式如下

```java
a -> System.out.println(a)
```
当有多个参数的时候以及方法里面的语法多余一行的时候，没有返回，，形式如下：

```java
(a, b) -> {
            System.out.println(a);
            System.out.println(b);
        }
```

上面例子都是没有返回的，那么有返回又是怎样的呢？形式如下：

```java
a -> {
            System.out.println(a);
            return a;
        }
```
是的，那就加一下返回啊。


### 函数式接口
其实上面都是展示如果去调用，不知道你们是否发现后者感觉到，他没有方法名就去调用了，是不是接受不了。这是正常情况。因为我们调用的接口里面这有一个方法，所以我们只需要一个 ```->```就可以调用到接口里面方法。

所以我们如果想自己写一个 Lambda 的话。只需要在接口里面写一个抽象方法即可。举了例子：

```java
public interface IPerson {
    int personMethod(int a);
}
```
当然我建议加一个注解```@FunctionalInterface```,形式如下：

```java
@FunctionalInterface
public interface IPerson {
    int personMethod(int a);
}
```
这样就是限制接口里面只能有一个抽象方法了。这个就叫函数式接口。当我们再继续往里面加的话，就会报错了：

![](http://javahouse.xyz/20200103015910.png)

其实大多数情况下，我们不需要自己写函数式接口。因为 Java 已经内置了四种常见的函数式接口。
![](http://javahouse.xyz/20200103020156.png)

这四个接口需要一般与 Stream 一起使用。 

## Stream

### 普通迭代

我们直接看代码，一个普通的迭代例子

```java
public class StreamTest {
     @Test
    public void test2(){
        List list = Arrays.asList(1, 2, 3, 4, 5);
        int count = 0;
        for (Object o : list){
            count ++;
        }
        System.out.println(count);
    }
}
```
运行Test，输出结果

```java
5
```

在 Java8 之前，我们统计 list 的大小是上面的形式。


### Stream 重构

现在我们用 Stream 重构上面的代码

```java
public class StreamTest {
    @Test
    public void test3(){
        List list = Arrays.asList(1, 2, 3, 4, 5);
        long count = list.stream().count();
        System.out.println(count);
    }
}
```

运行Test，输出结果

```java
5
```
瞬间感觉代码清爽了很多，变得优雅了，关键代码也少了。这就是 Stream 的魅力。

经过上面的例子，我们可以感觉到 Stream 可以代替 for 循环，进行特定操作。注意这里是特定的操作，因为 Stream 接口里面只有只封了几个方法。

### collect()

collect() 方法可以将 Stream 变成 List等集合形式。

我们看一个例子：

```java
public class StreamTest {
    @Test
    public void test4(){
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5,1);
        List<Integer> collect = stream.collect(Collectors.toList());    
    }
}
```

### fiter()

fiter() 方法与 Predicate 函数接口一起使用。

![](http://javahouse.xyz/20200103100916.png)

不想看文字，还有图片

![](http://javahouse.xyz/20200103101405.png)

该方法可以过滤出特定元素并且返回原来的类型。举一个例子

```java
public class StreamTest {
    @Test
    public void test5(){
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5,1);
        List<Integer> collect = stream.filter(a -> a > 3).collect(Collectors.toList());
        System.out.println(Arrays.asList(collect));
    }
}
```

运行Test，输出结果

```java
[[4, 5]]
```
### map()

map() 方法与 Function 函数接口一起使用。

![](http://javahouse.xyz/20200103101750.png)

不想看文字，还有图片

![](http://javahouse.xyz/20200103101835.png)

该方法可以将原来的集合进行修改，包括返回类型，然后返回一个新的集合。举一个例子

```java
public class StreamTest {
   @Test
    public void test8(){
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5,1);
        List<Integer> collect = stream.map(a -> a = 1).collect(Collectors.toList());
//        List<Boolean> collect = stream.map(a -> a == 1).collect(Collectors.toList());
        System.out.println(Arrays.asList(collect));
    }
}
```
运行Test，输出结果

```java
[[1, 1, 1, 1, 1, 1]]
```

我们可以看到两种情况 Integer -> Integer 以及  Integer -> Boolean

注意我们不能操作同一个 stream 两次，不然会报错，有兴趣可以试试。


### distinct()

该方法意如其字，就是去重。代码如下

```java
public class StreamTest {
    @Test
    public void test7(){
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5,1);
        List<Integer> collect = stream.distinct().collect(Collectors.toList());
        System.out.println(Arrays.asList(collect));
    }
}
```

运行Test，输出结果

```java
[[1, 2, 3, 4, 5]]
```





### 关注微信公众号，随时移动端阅读

![公众号.jpg](http://javahouse.xyz/20191212204326.png)