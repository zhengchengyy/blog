---
layout: post
title: Spring自动装配和组件扫描
categories: [spring]
tags: [spring]
---

* content
{:toc}




## 环境配置

先创建一个Maven项目，Eclipse和IntelliJ等IDE都支持Maven。然后添加Spring相关的依赖，包括core、beans、context，context-support。

Maven导入依赖的具体坐标可以去 [mvnrepository.com](mvnrepository.com) 查询，一个简单的pom.xml配置如下。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.fifteenfive</groupId>
  <artifactId>mvntutorial</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>mvntutorial Maven Webapp</name>
  <url>http://maven.apache.org</url>
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.7</version>
      <scope>test</scope>
    </dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-core</artifactId>
        <version>5.1.3.RELEASE</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-web</artifactId>
	    <version>5.1.3.RELEASE</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-beans</artifactId>
	    <version>5.1.3.RELEASE</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context-support</artifactId>
	    <version>5.1.3.RELEASE</version>
	</dependency>
  </dependencies>
  <build>
    <plugins>
    	<plugin>
    		<groupId>org.apache.maven.plugins</groupId>
    		<artifactId>maven-compiler-plugin</artifactId>
    		<configuration>
    			<source>1.8</source>
    			<target>1.8</target>
    		</configuration>
    	</plugin>
    </plugins>
  </build>
</project>

```



## 依赖注入（Dependency Injection）

依赖注入**一般**有两种方式，一种通过构造方法，另一种是通过setter方法。依赖注入概念的提出主要是解决对象之间的耦合问题。看这段代码：

```Java
class Computer{
    private Monitor monitor;
    public Computer(){
        this.monitor = new DellMonitor();
    }
    public work(){
        monitor.show();
    }
}
```



这段代码中`Computer`依赖于`Monitor`和`DellMonitor`，执行构造方法的时候会先生成一个`DellMonitor`对象，然后才能调用这个对象的`show()`方法工作。这段代码很糟糕，因为当我们想用另一个Monitor（HP etc.）的时候还需要修改构造函数里面的内容为`this.monitor = new HPMonitor();`，不利于维护。因此，我们改成下面这样：



```Java
class Computer{
    private Monitor monitor;
    public Computer(Monitor monitor){
        this.monitor = monitor;
    }
    public work(){
        monitor.show();
    }
}

```

或者

```Java
class Computer{
    private Monitor monitor;
    public Computer(){}
    public void setMonitor(Monitor monitor){
        this.monitor = monitor;
    }
    public work(){
        monitor.show();
    }
}
```

这样的话就把Monitor对象实例化的细节分离出去了，Computer类只负责接受一个Monitor，而不关心这个Monitor的具体型号。这两种处理依赖的方式就是依赖注入，实际上任何类似形式的方法都可以被称为依赖注入。



## 组件扫描和自动化装配

组件扫描（Component Scanning）是指让Spring自动发现应用上下文中所创建的bean；自动装配（autowiring）是指Spring自动处理bean之间的依赖关系。要实现该功能需要先显式配置Spring。

### 配置Config类

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration//1
@ComponentScan//2
public class ComputerConfig {

}
```

1. Configuration声明这是一个配置类
2. ComponentScan
   - 不设置参数的话，就表明自动扫描这个类所在的包；
   - 如果设置字符串参数的话，则自动扫描参数表示的包，如`@Component("com.pack.main")`；
   - 或者显式设置basePackages参数，如：`@ComponentScan(basePackages="pack2")`；
   - 设置多个包：`@ComponentScan(basePackages={"pack2","pack3"})`;
   - 设置扫描的类：`@ComponentScan(basePackageClasses={Keyboard.class,Mouse.class})`。

### 为bean添加Component注解

```java
import org.springframework.stereotype.Component;

@Component//1
public class DellMonitor implements Monitor{
	@Override
	public void show() {
		System.out.println("This is Dell Monitor!");
	}
}
```

1. 如果不为Component设置ID，则Spring会自动为该bean设置一个ID，方法是将类名第一个字母变为小写。手动设置ID的方式是传入一个表示ID的字符串，比如`@Component("dell")`;

### 为依赖添加Autowired注解

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Computer {
	private Monitor monitor;
	@Autowired//1
	public Computer(Monitor monitor) {
		this.monitor = monitor;
	}
	public void work() {
		monitor.show();
	}
}
```

1. 设置Autowired以后，Spring会自动去查找符合参数的依赖。
   - 如果只有一个的话会直接装配；
   - 如果没有匹配的bean会抛出异常。为了避免这个异常出现可以设置required属性为false，如`@Autowired(required=false)`，谨慎使用此方法。
   - 如果有多个bean满足依赖关系也会抛出异常

### 设置上下文测试

```java
import org.apache.catalina.core.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ComputerConfig.class);//1
		Computer computer = context.getBean(Computer.class);//2
		computer.work();
	}
}
```

1. `AnnotationConfigApplicationContext`是使用注解（Java）配置的上下文，如果是XML配置的上下文则需调用`ClassPathXmlApplicationContext`，传入代表配置文件地址的字符串。
2. `getBean()`会生成该bean的对象，因为Computer里面autowire了一个Monitor对象，所以会自动生成一个Monitor对象。我们的程序中只有一个DellMonitor组件实现了Monitor接口，所以实际生成的是DellMonitor的对象。这个阶段我们没有显示实例化任何一个bean，Spring帮我们把那些事情都做好了。

### 代码清单

- pom.xml
- Computer.java
- ComputerConfig.java
- DellMonitor.java
- Monitor.java (Interface, contains a `show()` method)
- Test.java