+++
title = "Java 多线程技术"
date = 2023-02-20
lastmod = 2023-02-24T16:20:14+08:00
tags = ["JUC", "Java", "面试"]
draft = false
katex = true
+++

## Java 核心知识 {#java-核心知识}


### Java 多线程技术 {#java-多线程技术}


#### 线程的实现方式 {#线程的实现方式}

> 一个有一点重要的题目

这个题目的难度在于底层，答案有1,3,4,5

1.  继承Thread类，重写run方法
    run方法的方法体就代表了线程要完成的任务，因此把run方法称为执行体启动线程时调用start方法，这样会创建一个新的线程，并执行线程的任务。
    ```java
    package org.example;

    public class Main {
        public static void main(String[] args) {
    	Myjob t1 = new Myjob();
    	t1.start();
    	for (int i = 0; i < 100; i++) {
    	    System.out.println("main: " + i);
    	}
        }
    }

    class Myjob extends Thread {
        @Override
        public void run() {
    	for (int i = 0; i < 100; i++) {
    	    System.out.println("Myjob: " + i);
    	}
        }
    }
    ```

2.  实现Rrunnable方法

    -   定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
    -   创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。
    -   调用线程对象的start()方法来启动该线程。

    线程的执行流程很简单，当执行代码start()时，就会执行对象中重写的void run()方法，该方法执行完成后，线程就消亡了。

3.  实现Callable重写call方法，配合FutureTask可以实现结果值的返回，Callable一般用于有返回结果的非阻塞的执行方法。步骤一
    ```java
    package org.example;

    import java.util.Objects;
    import java.util.concurrent.Callable;
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.FutureTask;
    public class Main {
        public static void main(String[] args) throws ExecutionException, InterruptedException {
    	MyCallable callable = new MyCallable();
    	FutureTask task = new FutureTask(callable);
    	Thread t1 = new Thread(task);
    	t1.start();
    	Object o = task.get();
    	System.out.println(o);
        }
    }

    class MyCallable implements Callable<String>{

        @Override
        public String call() throws Exception {

    	return "string";
        }
    }
    ```

上述三种方法的优点缺点对比：

-   Thread类
    -   优点: 编写简单, 如果需要访问当前的线程,无需使用Thread.currentThread()方法,直接使用this即可获得当前的线程
    -   缺点: 因为已经继承了Thread类,所以没办法继承别的类了.
-   Runnable接口:
    -   优点: 线程类只是实现了Runable接口，还可以继承其他的类。在这种方式下，可以多个线程共享同一个目标对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
    -   缺点: 编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法。
-   Callable接口:
    1.  Callable规定的方法是call(),Runnable规定的方法是run().
    2.  Callable的任务执行后可返回值，而Runnable的任务是不能返回值得
    3.  call方法可以抛出异常，run方法不可以，因为run方法本身没有抛出异常，所以自定义的线程类在重写run的时候也无法抛出异常
    4.  运行Callable任务可以拿到一个Future对象，表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并检索计算的结果。通过Future对象可以了解任务执行情况，可取消任务的执行，还可获取执行结果。

拓展的问题: start和run的区别在哪里?

> 这里只是简单的说明一下: start()方法用来，开启线程，但是线程开启后并没有立即执行，他需要获取cpu的执行权才可以执行
> run()方法是由jvm创建完本地操作系统级线程后回调的方法，不可以手动调用（否则就是普通方法）

着重记录一下自己的了解:

-   start方法
    start方法是Thread类中的一个自己的方法.
    1.  他会在调用的时候,检查线程的状态,如果线程的status是0,那么会直接抛出一个IllegalThreadStateException的异常.
    2.  如果说调用的对象的线程的状态是不为0的,那么它就会把当前的线程对象塞进一个Group中, `=private ThreadGroup group`;=
        1.  这里对Group进行一个简短的说明:
        2.  首先这个Group是实现了=UncaughtExceptionHandler=这个接口的.这里不多说,因为这个接口是Thread类里面的接口.
            ```java
            // 这个线程是父亲线程, 会在初始化的一些情况下进行赋值,这里不多说
            private final ThreadGroup parent;
            String name;
            int maxPriority;
            boolean destroyed;
            boolean daemon;
            boolean vmAllowSuspension;
            int nUnstartedThreads;
            int nthreads;
            Thread[] threads;
            int ngroups;
            ThreadGroup[] groups;
            ```
    3.  然后添加进去之后会留一个boolean在外面判断是否执行成功.
    4.  然后通过native start0() 方法去系统调度这个线程
-   run()方法 :
    1.  他与start方法最大的区别就在于start方法调用之后,除去异常情况下, start方法就会通过start0()来开启一个线程. 但是run()只是一个普通的由自己实现的一个方法.
    2.  所以直接调用run方法的时候, 你的this指向的是当前的main线程, 而不是Thread-1线程, 只有当你在新建一个Thread对象的时候将Runnabel作为构造函数传进去, 才会新建一个线程.
        ```java
        // 调用上文的init
        public Thread(Runnable var1) {
            this.init((ThreadGroup)null, var1, "Thread-" + nextThreadNum(), 0L);
        }
        ```

回到最开始的问题，有几种创建线程的方法，上面说了三种之后，其实还有两种说法，一个是使用Lambda表达式的创建，但是这种方法本质上还是使用上面的三种方法，所以这里不多赘述，另外一种就是实现线程池创建线程，那我们来着重说一下这个东西。

现在我们来说使用线程池的一些优点，也是我们为什么要使用线程池的原因：
1.减少资源的消耗。重复利用已经创建的线程,避免频繁的创造和销毁线程，减少消耗。
2.提高响应速度。当执行任务时，不需要去创建线程再来执行，只要调动现有的线程来执行即可。
3.提高了线程的管理性。线程是稀缺资源，使用线程池可以进行统一的分配、调优和监控。

这里不过多的描述线程池的创建，等到后面有这个题目的时候我们再来进行深入的研究，不过这里只需要知道我们还有一种方式可以创建线程就好了。

那么还有一种说法是我们创建线程的办法究其底层只有一种，那就是通过Thread类来创建，而你运行的代码块的内容则可以实现Runnable接口或者是继承Thread类。


#### Java中线程的状态 {#java中线程的状态}
