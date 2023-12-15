---
title: Java FAQ
url: java-faq
date: 2019-03-21 18:36:00
description: Java
categories: Computer Science
tags: [Language]
---

 1. Jar包本来在project structure中，按绿色按钮也可以执行，但从命令行就会报错：找不到对应的包。  
Idea为了从命令行编译程序，必须将jar包的路径添加到系统变量classpath中，再在idea中project structure中添加该jar，重启计算机。

## 多线程
Java有2种方式实现多线程：

继承`Thread`类

单线程程序即只有主方法的线程，该线程由JVM负责启动，其他线程由程序员负责启动。`Thread`类中实例化的对象代表一个线程，继承后重写`run()`方法，将该线程的功能实现放在`run()`方法中，调用`Thread`类中的`start()`方法启动线程，`start()`方法会调用覆盖后的`run()`方法。

```java
public class Test extends Thread {
    public void run() {
        System.out.println("Hello");
    }
    public static void main(String[] args) {
        new Test().start();
    }
}
```
实现`Runnable`接口

第一种方法的缺陷在于：如果程序需要继承其他类而非`Thread`类，但Java是单继承语言，此时就无法通过该方式实现多线程，此时就需要采用第二种方法。创建`Runnable`对象后，将其传递给`Thread`类的构造方法，调用`start()`方法即可。

```java
public class Test {
    public static void main(String[] args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello");
            }
        };

        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

这里比较疑惑的地方在于：Java中接口和抽象类是不能实例化的，即`Runnable`接口是不能实例化的，但是代码中却`new Runnable()`。这里实际上首先构造了一个`implements Runnable`的匿名内部类，然后构造了该类的一个实例，接着用`Runnable`表示该类的类型。

不使用匿名内部类：
```
public class Test implements Runnable {
    @Override
    public void run() {
        System.out.println("Hello");
    }

    public static void main(String[] args) {
        Test hello = new Test();
        Thread thread = new Thread(hello);
        thread.start();
    }
}
```
