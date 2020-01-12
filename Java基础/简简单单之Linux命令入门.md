>show me the code and talk to me,做的出来更要说的明白  
>GitHub 项目[JavaHouse](https://github.com/buerbl/JavaHouse)同步收录   
>我是布尔bl，你的支持是我分享的动力！

## 引入


作为一名合格的后端开发人员，我们难免要在 Linux 环境下进入操作，查找问题，配置文件等等操作。因此，我觉得我们需要掌握简单的 Linux 命令。本文简简单单介绍一下 Linux 的常见操作，努力让读者在 Linux 环境下可以进行工作。

## 目录结构

![](http://javahouse.xyz/20200110163309.png)

Linux 大体目录结构如图所示。其中上面的 / 表示的是跟目录。


### 显示目录位置```pwd```


```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# pwd

/
```

### 进入目录 ```cd```
```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cd usr/
[root@iZm5eixsxpdmrg95cpxvmlZ usr]# pwd
/usr
```
可以看到我们进入了 usr 目录

### 显示内容 ```ll```
```java
[root@iZm5eixsxpdmrg95cpxvmlZ usr]# ll

total 136
dr-xr-xr-x.   2 root root 32768 Jan  6 21:00 bin
drwxr-xr-x.   2 root root  4096 Apr 11  2018 etc
drwxr-xr-x.   2 root root  4096 Apr 11  2018 games
drwxr-xr-x.  69 root root 12288 Dec  4 11:16 include
drwxr-xr-x    3 root root  4096 Dec  4 11:27 java
dr-xr-xr-x.  33 root root  4096 Jan  6 21:02 lib
dr-xr-xr-x.  49 root root 36864 Jan  6 21:00 lib64
drwxr-xr-x.  24 root root  4096 Dec  4 11:16 libexec
drwxr-xr-x.  21 root root  4096 Jan  6 21:02 local
dr-xr-xr-x.   2 root root 16384 Jan  6 21:02 sbin
drwxr-xr-x. 104 root root  4096 Jan  6 21:00 share
drwxr-xr-x.   4 root root  4096 Jul 11  2019 src
lrwxrwxrwx.   1 root root    10 Jul 11  2019 tmp -> ../var/tmp
```

该命令可以显示目录里面所有的文件名称、文件的权限、文件大小（kb）等等详细信息。

### 进入根目录 ```cd /```

```java
[root@iZm5eixsxpdmrg95cpxvmlZ usr]# cd /
[root@iZm5eixsxpdmrg95cpxvmlZ /]# ll

total 2097224
lrwxrwxrwx.  1 root root          7 Jul 11  2019 bin -> usr/bin
dr-xr-xr-x.  5 root root       4096 Jul 11  2019 boot
drwxr-xr-x   6 root root       4096 Dec 15 10:04 data
drwxr-xr-x  19 root root       2960 Dec 15 10:05 dev
drwxr-xr-x. 82 root root       4096 Jan  6 21:02 etc
drwxr-xr-x.  2 root root       4096 Apr 11  2018 home
drwxr-xr-x   3 root root       4096 Dec  4 11:04 java
lrwxrwxrwx.  1 root root          7 Jul 11  2019 lib -> usr/lib
lrwxrwxrwx.  1 root root          9 Jul 11  2019 lib64 -> usr/lib64
drwx------.  2 root root      16384 Jul 11  2019 lost+found
drwxr-xr-x.  2 root root       4096 Apr 11  2018 media
drwxr-xr-x.  2 root root       4096 Apr 11  2018 mnt
drwxr-xr-x.  2 root root       4096 Apr 11  2018 opt
dr-xr-xr-x  92 root root          0 Dec 15 10:05 proc
dr-xr-x---.  5 root root       4096 Jan 10 10:57 root
drwxr-xr-x  24 root root        720 Jan  6 21:00 run
lrwxrwxrwx.  1 root root          8 Jul 11  2019 sbin -> usr/sbin
drwxr-xr-x.  2 root root       4096 Apr 11  2018 srv
-rw-------   1 root root 2147483648 Dec  4 11:13 swapfile
dr-xr-xr-x  13 root root          0 Dec 15 15:28 sys
drwxrwxrwt.  9 root root       4096 Jan 10 03:15 tmp
drwxr-xr-x. 14 root root       4096 Dec  4 11:27 usr
drwxr-xr-x. 19 root root       4096 Jul 11  2019 var
```
### 进入上级目录 ```cd ..```
```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cd /usr/
[root@iZm5eixsxpdmrg95cpxvmlZ usr]# cd ..
[root@iZm5eixsxpdmrg95cpxvmlZ /]# pwd
/
```
### 文件权限```chmod```

一般来说一个文件有三种角色
- 拥有者
- 拥有组
- 其他人

权限有三种表示
- r 表示只可以读文件，可以用数字 4 代替
- w 表示只可以写文件，可以用数字 2 代替
- x 表示只可以执行文件，可以用数字 1 代替

其中数字可以累加
- 5 表示可读可执行
- 6 表示可读可写
- 7 表示可读可写可执行

举个例子
```java
chmod 754 a.txt
```
该命令表示
- 拥有者可读可写可执行
- 拥有组可读写可执行
- 其他者可读
 
可见三个数字是表示三种不同角色。

## 查找目录

```java
find 位置  -name 文件名
```

例子

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# find / -name tomcat

/usr/local/tomcat
/etc/logrotate.d/tomcat
/etc/selinux/targeted/active/modules/100/tomcat
/etc/rc.d/init.d/tomcat
```
显然我们目的是在从目录开始去寻找 tomcat 目录

## 字符串处理

### 管道```|```

这个命令大家应该很常见，他是 Linux 里面管道，用于连接两个或者多个命令，具体表现为上一个命令的输出内容作为下个命令的输入内容。

例子

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# ps aux| grep tomcat


root      2292  0.0  0.0 112712   956 pts/0    R+   11:29   0:00 grep --color tomcat
www       6514  0.1 32.8 2993956 618504 ?      Sl    2019  45:28 /usr/java/jdk1.8.0_231/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.security.egd=file:/dev/./urandom -server -Xms256m -Xmx919m -Dfile.encoding=UTF-8 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Djava.library.path=/usr/local/apr/lib -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat -Dcatalina.home=/usr/local/tomcat -Djava.io.tmpdir=/usr/local/tomcat/temp org.apache.catalina.startup.Bootstrap start
```

### 搜索文本```grep```

该命令用于匹配字符串，相当于我们常用的 Ctrl+f.

```java
grep  匹配内容 文件名
```

例子

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# grep asda test.txt

asda
```

### 排序```sort```

```java
sort [-ntkr] 文件名

其中：
n：数字排序
t：指定分隔符
k：指定一第几列为标准排序,常与 t 一起使用
r：逆序
```

例子：

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt 

a:2
s:6
d:1
a:0


[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt | sort 

a:0
a:2
d:1
s:6


[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt | sort -t ":" -k 2 

a:0
d:1
a:2
s:6


[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt | sort -t ":" -k 2 -r
s:6
a:2
d:1
a:0
```

### 计算重复```uniq```
```java
uniq [-ic]

# -i 忽略大小写
# -c 计算重复行数
```

常与```sort```使用

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt 

a:2 a a d
s:6 a V
d:1   a
a:0
a:0
a:0


[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt | uniq

a:2 a a d
s:6 a V
d:1   a
a:0


[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt | uniq -c

      1 a:2 a a d
      1 s:6 a V
      1 d:1   a
      3 a:0
      
      
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt| sort | uniq -c

      3 a:0
      1 a:2 a a d
      1 d:1   a
      1 s:6 a V

```



## 进程

### 瞬间状态```ps```
该命令用于查看某一时刻的进程状态
```java
ps 参数
#-A 列出所有的进程，和-e一样
#aux 显示所有的包含其他使用者的进程
```

查看tomcat进程是否存在

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# ps aux | grep tomcat


www       6514  0.1 32.8 2993956 618504 ?      Sl    2019  47:29 /usr/java/jdk1.8.0_231/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.security.egd=file:/dev/./urandom -server -Xms256m -Xmx919m -Dfile.encoding=UTF-8 -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Djava.library.path=/usr/local/apr/lib -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat -Dcatalina.home=/usr/local/tomcat -Djava.io.tmpdir=/usr/local/tomcat/temp org.apache.catalina.startup.Bootstrap start
root     32062  0.0  0.0 112712   952 pts/0    R+   17:46   0:00 grep --color tomcat

```


### 实时状态```top```

该命令用于查看cpu实时运行状态，内存空间

### 通过pid终止进程```kill```


### 通过名称终止进程```killall```


```killall``` 命令比较安全，可以输出进程名称终止，避免输错数字pid

### 端口占用 ```lsof```

改命令查看端口是否被占用

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# lsof -i:3306

COMMAND  PID  USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
mysqld  5437 mysql   30u  IPv4    94759      0t0  TCP *:mysql (LISTEN)

```

## vim 编辑器

vim编辑器是Linux的文本编辑器，相当window的记事本。

### 查找关键字 ```/```
在vim里面我们使用 ```/ 关键字```查找关键字

### 退出

使用 ```esc```和```:wq```组合保退出vim

## 文本处理工具```awk```
```awk```默认以空格划分域（列数）,因此我们可以通过 ```awk```打印特定的列

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt 

a:2 a a d
s:6 a V
d:1   a
a:0
a:0
a:0


[root@iZm5eixsxpdmrg95cpxvmlZ /]# awk '{print $1}' test.txt 

a:2
s:6
d:1
a:0
a:0
a:0

```

### 指定分割```-F 分隔符```

如果我们不需要空格分割，可以指定其他

```java
[root@iZm5eixsxpdmrg95cpxvmlZ /]# cat test.txt 

a:2 a a d
s:6 a V
d:1   a
a:0
a:0
a:0


[root@iZm5eixsxpdmrg95cpxvmlZ /]# awk -F : '{print $1}' test.txt 

a
s
d
a
a
a

```
