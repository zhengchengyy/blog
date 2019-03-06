---
layout: post
title: Use Lombok to Simplify Java POJO Creating
categories: [java]
tags: [POJO,lombok]
---

* content
{:toc}


# Overview

Lombok通过注解的方式让开发过程中减少下面这些代码的使用：

- Getter
- Setter
- toString
- hashCode
- equals

为了使用Lombok，需要在Maven项目中添加该依赖：

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.4</version>
    <scope>provided</scope>
</dependency>
```

或者在[download lombok](https://projectlombok.org/download)下载jar包，然后自行配置。

**注意：在IDE中使用Lombok需要先配置一下，方法为直接运行在官网下载到（或者Maven等工具）的Lombok的jar包。**

下面是Lombok中常用注解的使用方法。

# @Getter and @Setter

带有@Getter的变量会自动生成一个getter函数，而带有@Setter的变量会自动生成一个setter函数。如果该变量的类型为布尔类型，则getter函数的命名方式会有所不同，不再以get作为前缀，而使用is作为前缀。

使用Lombok的代码：

```java
@Getter @Setter private boolean employed = true;
@Setter(AccessLevel.PROTECTED) private String name;
```

等同于：

```java
private boolean employed = true;
private String name;

public boolean isEmployed() {
    return employed;
}

public void setEmployed(final boolean employed) {
    this.employed = employed;
}

protected void setName(final String name) {
    this.name = name;
}
```

# @ToString

@ToString注解对类使用，表示自动重写该类的toString函数。需要注意的是：

- 不包含静态变量；
- 使用键值对的方式生成字符串，如`MyClass(field1=value1,field2=value2)`;
- 在注解中设置`includeFieldNames=false`可以使结果不包含变量名；

使用Lombok的代码：

```java
@ToString(callSuper=true,exclude="someExcludedField")//1
public class Foo extends Bar {
    private boolean someBoolean = true;
    private String someStringField;
    private float someExcludedField;
}
```

- 1
  - callSuper表示在生成的字符串中包括父类的toString函数的返回结果；
  - exclude表示不包含的内容，可以写为`exclude={"field1","field2"}`。

等同于：

```java
public class Foo extends Bar {
    private boolean someBoolean = true;
    private String someStringField;
    private float someExcludedField;
    
    @java.lang.Override
    public java.lang.String toString() {
        return "Foo(super=" + super.toString() +
            ", someBoolean=" + someBoolean +
            ", someStringField=" + someStringField + ")";
    }
}
```

# @EqualsAndHashCode

在类上使用该注解会同时自动生成equals函数和hashCode函数。

该注解有两个常用的参数，callSuper和exclude，和@ToString类似，不过callSuper在这里需要谨慎对待，如果设置为true的话需要确保在父类当中已经妥善处理好了这两个函数。如果这个类直接继承自Object，一定要将callSuper设置为false。

# @Data

最常用的注解，对类使用，如果该类是一个标准的POJO类的话。

使用该注解会

- 生成所有成员变量的setter和getter方法
- 生成toString方法
- 生成equals方法和hashCode方法
- 生成该类的构造方法，使用的参数为**声明为final的变量和添加了@NonNull注解的变量**。

该注解有一个可设置的参数，staticConstructor，表示生成一个静态构造方法并且命名为该参数的值（参数同样为声明为final的变量和添加了@NonNull注解的变量）。

使用Lombok的代码：

```java
@Data(staticConstructor="of")
public class Company {
    private final Person founder;
    private String name;
    private List<Person> employees;
}
```

等同于：

```java
public class Company {
    private final Person founder;
    private String name;
    private List<Person> employees;
    
    private Company(final Person founder) {
        this.founder = founder;
    }
    
    public static Company of(final Person founder) {
        return new Company(founder);
    }
    
    public Person getFounder() {
        return founder;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(final String name) {
        this.name = name;
    }
    
    public List<Person> getEmployees() {
        return employees;
    }
    
    public void setEmployees(final List<Person> employees) {
        this.employees = employees;
    }
    
    @java.lang.Override
    public boolean equals(final java.lang.Object o) {
        if (o == this) return true;
        if (o == null) return false;
        if (o.getClass() != this.getClass()) return false;
        final Company other = (Company)o;
        if (this.founder == null ? other.founder != null : !this.founder.equals(other.founder)) return false;
        if (this.name == null ? other.name != null : !this.name.equals(other.name)) return false;
        if (this.employees == null ? other.employees != null : !this.employees.equals(other.employees)) return false;
        return true;
    }
    
    @java.lang.Override
    public int hashCode() {
        final int PRIME = 31;
        int result = 1;
        result = result * PRIME + (this.founder == null ? 0 : this.founder.hashCode());
        result = result * PRIME + (this.name == null ? 0 : this.name.hashCode());
        result = result * PRIME + (this.employees == null ? 0 : this.employees.hashCode());
        return result;
    }
    
    @java.lang.Override
    public java.lang.String toString() {
        return "Company(founder=" + founder + ", name=" + name + ", employees=" + employees + ")";
    }
}
```

