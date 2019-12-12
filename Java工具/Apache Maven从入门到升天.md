### 1 引入  

在日常 Java 开发中，Maven 应该是必不可少的一个工具了，当然也有人使用 Gradle 的。那么 Maven 究竟是个啥东西呢？不就是一个依赖引入工具吗？我相信很多人和我是一样的感觉。但是一般而言，感觉的东西往往是不可靠的。我在网上收集一些资料，翻翻官网，重新整理了一下 Maven 的资料。

### 2 Maven 简介

维基百科上面说 Maven 是一个软件项目管理和自动构建工具。单从字面意思来看，我是不知道这是想要表达什么意思的。按照我的理解，Maven 其实就是一个开发工具，跟 IDEA 相辅相成。我们日常使用 IDEA 的编译项目的时候，其实就是在使用 Maven，只不过这些操作都被强大的 IDEA 隐藏了，造成我们在使用 IDEA，和 Maven 没有联系的错觉。

### 3 Maven 的命令

说命令行之前，容我说手几个专业的名词。  

名称 | 解释  
---|---
groupId | 包名
artifactId | 项目名称

groupId 的 artifactId 被人们称为坐标，确实也是，一旦他们的值确定了，我们就可以根据他们的坐标找到他们。

#### 创建

```text
mvn -B archetype:generate 
-DarchetypeGroupId=org.apache.maven.archetypes -DgroupId=com.mycompany.app    
-Dartifac t Id=my-app
```

![](http://javahouse.xyz/GY7MAIMWP3RV@ZKU7W%7D6%5DLG.png)

这样maven就自动创建了一个带有 hello world 的 Maven 项目。包名是 com.mycompany.app； 项目名是 my-app。看到这里，我们就知道为什么 Maven 是项目构建工具了。我个人觉得 Maven 跟大前端的 npm 工具有相似的地方。

#### 编译

```text
mvn compile
```

该命令从字面意思流知道是编译命令，他是不是编译 test 文件夹里面的源码的。执行该命令就会生成一个 target 文件。文件夹里面就是编译后的内容。

#### 清除

```text
mvn clean
```


该命令主要是清除编译后产生的 target 文件夹。

#### 打包

```text
mvn package
```

该命令会将项目默认打成 jar 包，当然我们就也可以打成 war 包。

#### 安装


```text
mvn install
```

该命令可以把项目打成 jar，放到本地仓库。

### 4 仓库

要理解 Maven，仓库概念是必不可少的。让我（灵魂画手）画个图。

![](http://javahouse.xyz/20191212201309.png)

项目需要的依赖都是从本地仓库里面拿到的，本地仓库就是自己的电脑，我们引入的各种依赖都放在本地仓库里面，一般依赖有问题的话，就是本地仓库的依赖没有。我们可以先把本地的对应的依赖删除，在试试，或许就可以了。  
从中，我们可以看到自建仓库，也就是我们说的私服，可以存放一些我们开发的基础包，从自建仓库引入依赖就比较快的，当然如果自建仓库里面没有我们需要的依赖，自建仓库就会发出请求，请求远程的中央仓库，中央仓库没有那就真的没有了。 

### 5 POM

现在是我们的重要任务出场了--POM. Maven 项目都会有一个 pom.xml 文件。

![](http://javahouse.xyz/20191212202738.png)

这是一个最简单的 pom 文件。这个 pom 文件应该我们很熟悉了，引入依赖就是从这里引的。没什么好说的，所以说些其他的。  



#### 包类型

```text
// war
<packaging>jar</packaging>  
```

该标签决定项目是 jar 包或者 war 包。一般来说，SpringBoot 项目会打成 war 包，然后放进 tomcat 运行。当然也可以 jar 包运行，毕竟 SpringBoot 内置了tomcat ，强的一批。

#### 加进来
```text
<build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
</build>
```
如果我们把一下文件，例如 xml 后缀的 mapper 文件放在 resources 里面，编译的时候会发现他们没有编译进去，是因为 <filtering> 标签默认是  false，我们需要 true。

#### 去掉他

```text
<exclusions>
    <exclusion>
      <groupId>sample.ProjectE</groupId> <!-- Exclude Project-E from Project-B -->
      <artifactId>Project-E</artifactId>
    </exclusion>
</exclusions>
```

#### 部署到远程

```text
<distributionManagement>
    <repository>
      <id>mycompany-repository</id>
      <name>MyCompany Repository</name>
      <url>scp://repository.mycompany.com/repository/maven2</url>
    </repository>
</distributionManagement>

```

当我们造好轮子之后，想部署到远程中央仓库，就添加上面标签。同时在 setting.xml(Maven 安装的目录下)添加以下

```text
<servers>
    <server>
      <id>mycompany-repository</id>
      <username>jvanzyl</username>
      <!-- Default value is ~/.ssh/id_dsa -->
      <privateKey>/path/to/identity</privateKey> (default is ~/.ssh/id_dsa)
      <passphrase>my_key_passphrase</passphrase>
    </server>
 </servers>
```

#### 多个模块

如果我们想一次建造几个maven项目，也是没有问题的。比如有两个模块（my-app、my-webapp），目录如下

```text
+- pom.xml
+- my-app
| +- pom.xml
| +- src
|   +- main
|     +- java
+- my-webapp
| +- pom.xml
| +- src
|   +- main
|     +- webapp
```
在顶层 pom.xml 需要添加 <modules> 标签，如下

```text
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-app</module>
    <module>my-webapp</module>
  </modules>
</project>
```

然后选一个做老大（webapp），在他的 pom 文件里加入 my-app 的依赖

```text
<dependencies>
    <dependency>
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>
    </dependency>
    ...
</dependencies>
```

然后再这两个项目（webapp、my-app）的 pom 文件里面加入顶层的 <parent> 标签

```text
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>app</artifactId>
    <version>1.0-SNAPSHOT</version>
 </parent>
 ...
```
最后执行 mvn verify 命令，就会在老大项目（webapp）的 target 文件夹里生成 war 包。



#### 参考  

- https://zh.wikipedia.org/wiki/Apache_Maven  
- https://maven.apache.org/  
- https://maven.apache.org/guides/getting-started/index.html  
- https://maven.apache.org/guides/mini/guide-configuring-maven.html  
- https://www.cnblogs.com/luotaoyeah/p/3791966.html
