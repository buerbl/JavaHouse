>show me the code and talk to me,做的出来更要说的明白   
>我是布尔bl，你的支持是我分享的动力！

## 一 、引入

设计接口是我们开发人员的日常操作。当我们把接口交给前端人员时，是否有种拔剑出鞘的错觉。毕竟交付接口，我们的开发工作就阶段性完成了。不过，如果我们没有一个接口设计规范的时候，结果会怎样呢？我们来张图感受一下。

![](http://javahouse.xyz/20200118222541.png)

## 二、REST

2000年，一个年轻小伙子（Roy Thomas Fielding）在他的博士论文提出了 REST。REST 是一种万维网软件架构风格。为什么说是风格不是标准呢？个人理解可能说标准就有点过分了。小伙子做不到。随后这种风格被推广开来，漂洋过海，被大众熟知。在 REST 的基础上，产生了 RESTFUL 。什么是 RESTFUL？简单的说，符合 REST 风格的接口就是 RESTFUL。

## 三、RESTFUL

接口各种各样。正如一千个人眼里有一千个鲁迅。RESTFUL 接口究竟长什么样子呢？

### 3.1 HTTP 的方法

HTTP 里面有几个基本的方法。我们利用这些约定一些规范。


方法 | 作用
---|---
GET |获取数据
POST | 插入数据
PUT | 更新数据
DELECT | 删除数据

从表中，如果我们可以清楚看到当我们的接口是关于获取数据，那么我们使用 GET 方法。  
如果我们可以清楚看到当我们的接口是关于插入数据，那么我们使用 POST 方法。  
如果我们可以清楚看到当我们的接口是关于更新数据，那么我们使用 PUT 方法。  
如果我们可以清楚看到当我们的接口是关于删除数据，那么我们使用 DELECT 方法。  

### 3.2 名词

在上面我们已经知道接口在什么时候需要什么方法，那么我们现在来进入到设计接口的第二步。

我们看看线上网站的接口是怎么样的。

![](http://javahouse.xyz/20200118232332.png)

图中我们可以看到有个 v1 ，他代表的是版本号，所以我们在设计接口的时候可以在将版本号写上，用 v1、v2、v3 等表示。

我们发现他的接口都是名词。所以我们知道 RESTFUL 接口使用的是名词。比如我们设计一个获取数据的接口，我们可这样设计

```java
/v1/list 
```

上面接口是获取所有数据。

当我们需要列表中的一条数据，我们可以这样设计

```java
/v1/list/1 
```
上面接口表示获取是列表的1号数据，我们可以获取2号、3号数据等等，只要改变数字即可。




### 3.3 组合 

结合上面两步，我们就可以设计出增删改查的 restful 接口了。


接口 | 方法|作用
---|---|---
/v1/list | GET| 获取列表
/v1/list | POST| 增加列表
/v1/list | PUT| 更新列表
/v1/list | DELECT| 删除列表




### 3.4 应用
以下是源码的demo

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
@Slf4j
public class LsbRestfulApplication {

    public static void main(String[] args) {
        SpringApplication.run(LsbRestfulApplication.class, args);
    }

    /**
     * 得到所有列表
     * @return
     */
    @RequestMapping(value = "/v1/list",method = RequestMethod.GET)
    public String getList(){
        log.info("得到列表");
        return "得到列表";
    }

    /**
     * 得到列表中的一条
     * @param name
     * @return
     */
    @RequestMapping(value = "/v1/list/{name}",method = RequestMethod.GET)
    public String getListone(@PathVariable("name") String name){
        log.info("得到列表"+name);
        return "得到列表"+name;
    }

    /**
     * 往列表中的增加一条数据
     * @return
     */
    @RequestMapping(value = "/v1/list",method = RequestMethod.POST)
    public String addList(){
        log.info("增加一个列表1");
        return "增加一个列表1";
    }

    /**
     * 更新列表中的一条数据
     * @return
     */
    @RequestMapping(value = "/v1/list/{name}",method = RequestMethod.PUT)
    public String updateListOne(@PathVariable("name") String name){
        log.info("更新列表"+name);
        return "更新列表"+name;
    }
    
    /**
     * 删除所有列表
     * @return
     */
    @RequestMapping(value = "/v1/list",method = RequestMethod.DELETE)
    public String delList(){
        log.info("删除一个列表");
        return "删除一个列表";
    }
}
```






