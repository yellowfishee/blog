+++
title = "Java 多线程技术"
date = 2023-02-20
lastmod = 2023-03-05T20:35:38+08:00
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

    run方法的方法体就代表了线程要完成的任务，因此把run方法称为执行体

    启动线程时调用start方法，这样会创建一个新的线程，并执行线程的任务。
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
>
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

关于这个题目的说法众说纷纭，常见的说法就是5、6、7这三种其实都可以，那么我们依次来说一下这三种说法到底是为什么吧。

首先就是五种线程状态，那么这种说法的根据呢就是操作系统层面的了。
![](/ox-hugo/2023-02-24_16-27-02_screenshot.png)
下面来解释一下这五个状态

-   new status：这个状态的线程处于刚刚被创建的状态，线程还没有启动，当你启动之后就到了下一个状态，ready状态
-   ready status：这个状态的线程已经在准备状态了。当CPU调度的时候，就进行到了下一个阶段，running阶段。
-   running status：这个阶段的线程已经在执行自己携带的字节码等操作。如果在这个时候我们对线程进行了如wait()、sleep()、jonin()等阻塞操作，那么我们的线程会进入到一个waiting的status中去，在这个状态中的线程CPU是不会去调度的。然后执行结束，倒了TERMINATED status

Java层面的6种状态
![](/ox-hugo/2023-02-24_16-54-00_screenshot.png)

-   new：Thread对象被创建出来了，但是还没有执行start方法。
-   runnable：Thread对象调用了start方法，就成为了runnable方法，（CPU调度/没有调度），它说的是可以被执行，而不是一定在执行当中。是一个就绪和运行中同时存在的状态。
-   blocked、waiting、time_waiting：都可以理解为是阻塞、等待状态，因为处于这三种状态下，CPU 不会调度当前线程。
-   blocked：synchronized没有拿到同步锁，被阻塞的情况
-   waiting：调用wait方法就会处于WAITING状态，需要被手动唤醒
-   time_waiting：调用sleep方法或者join方法，会被自动唤醒，无需手动唤醒
-   terminated：run方法执行完毕，线程生命周期到头了。

在代码中验证一下这个说法是不是对的

NEW：

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {

    });
    System.out.println(t1.getState()); // NEW
}
```

RUNNABLE:

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {
	while (true) {

	}
    });
    t1.start();
    System.out.println(t1.getState()); // RUNNABLE
}
```

BLOCKED:

```java
public static void main(String[] args) throws InterruptedException {
    Object obj = new Object();
    Thread t1 = new Thread(() -> {
	synchronized (obj) {

	}
    });

    synchronized (obj) {
	t1.start();
	Thread.sleep(496);
	System.out.println(t1.getState()); // BLOCKED
    }
}
```

WAITING:

```java
public static void main(String[] args) throws InterruptedException {
    Object obj = new Object();
    Thread t1 = new Thread(() -> {
	synchronized (obj) {
	    try {
		obj.wait(); // 等待notify
	    } catch (InterruptedException e) {
		e.printStackTrace();
	    }
	}
    });
    t1.start();
    Thread.sleep(500);
    System.out.println(t1.getState()); //WAITING
}
```

TIMED_WAITING:

```java
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
	try {
	    Thread.sleep(1000);
	} catch (InterruptedException e) {
	    e.printStackTrace();
	}
    });
    t1.start();
    Thread.sleep(500);
    System.out.println(t1.getState()); // TIMED_WAITING
}
```

TERMINATED:

```java
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
	try {
	    Thread.sleep(500);
	} catch (InterruptedException e) {
	    e.printStackTrace();
	}
    });
    t1.start();
    Thread.sleep(1000);
    System.out.println(t1.getState()); // TERMINATED
}
```

主要还是答在Java中的几种状态。


#### 线程的结束方式 {#线程的结束方式}

线程结束的方式很多，最常用的就是让线程的run方法结束，无论是return结束，还是抛出异常结束，都可以。可以看源码，被＝@Deprecated＝修饰

<!--list-separator-->

-  stop方法（不用）

    强制让线程结束，无论你在干吗，不推荐使用当然当然方式，但是，他确实可以把线程干掉。

    举个例子：

    ```java
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
    	try {
    	    Thread.sleep(5000);
    	} catch (InterruptedException e) {
    	    e.printStackTrace();
    	}
        });
        t1.start();
        Thread.sleep(1000);
        t1.stop();
        System.out.println(t1.getState()); // TIMED_WAITING
    }
    ```

<!--list-separator-->

-  使用共享变量（很少会用）

    这种方式用的也不多，有的线程可能会通过死循环来保证一直运行。

    咱们可以通过修改共享变量在破坏死循环，让线程退出循环，结束run方法。

    ```java
    static volatile boolean flag = true;
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
    	while (flag) {
    	    // statement
    	}
        });
        t1.start();
        Thread.sleep(4999);
        flag = false;
        System.out.println(t1.getState());
    }
    ```

<!--list-separator-->

-  interrupt 方式

    共享变量方式，它的方法跟上面的共享变量的方法类似。其主要的实现方法是，线程内部维护了一个线程标记位置，当你执行了Thread.currentThread().isInterrupted()就会输出返回你的interrupt标记位。

    **总结：**
    主要有三个方法：

    1.  interrupt：这个方法用来中断当前执行的线程。它是设置该线程的中断位为true，然后可以在线程里面通过interrupted或者isInterruped来判断中断标志位从而退出程序执行过程。
    2.  interrputed：判断当前线程是否设置了中断标志位，是一个静态方法，仅仅作用于本线程，同时它被调用后，线程的中断标志会被清除。
    3.  isInterrupted：判断线程是否设置了中断标志位，它的使用者可能运行本线程，也有可能运行在其他线程，同时它不会清楚线程的中断标志。

    下面可以看代码：

    ```java
    public static void main(String[] args) throws InterruptedException {
        // 线程默认情况下， interrupt标记位为：false
        System.out.println(Thread.currentThread().isInterrupted());
        // 执行interrupt之后，再次查看打断信息
        Thread.currentThread().interrupt();
        // interrupt标记位：true
        System.out.println(Thread.currentThread().isInterrupted());
        // 返回当前线程，并归位位false interrupt标记位为：true
        System.out.println(Thread.currentThread().interrupted());
        // 已经归位了
        System.out.println(Thread.interrupted());

        Thread t1 = new Thread(() -> {
    	while ( !Thread.currentThread().isInterrupted()) {
    	    // 业务代码
    	}
    	System.out.println("t1结束");
        });
        t1.start();
        Thread.sleep(499);
        t1.interrupt();
    }
    ```


#### Java中sleep和wait方法的区别 {#java中sleep和wait方法的区别}

1.  单词不一样
2.  sleep属于Thread类中的static方法、wait属于Object类的方法
3.  sleep属于TIMED_WAITING，自动被唤醒、wait属于WAIGING，需要被手动唤醒
4.  sleep方法在持有锁时，执行，不会释放锁资源，wait在执行后，会释放锁资源。
5.  sleep可以再持有锁或者不持有锁时执行。而wait方法必须在持有锁的时候才可以执行。

wait方法会将持有锁的线程丛owner扔到WaitSet集合中，这个操作是在修改ObjectMonitor对象，如果没有持有synchronized锁的话，是无法操作ObjectMonitor对象的，这里牵扯到了synchronized锁的底层实现原理，会在后面的内容中进行详细的说明。

实际上需要锁并不是为了避免死锁，而是为了因为wait需要有锁的情况下才能运行。


#### Java并发编程的三大特性 {#java并发编程的三大特性}

<!--list-separator-->

-  原子性

    <!--list-separator-->

    -  什么是并发编程的原子性

        JMM（Java Memory Model）。不同的硬件和不同的操作系统在内存上的操作有一定的差异。Java为了解决相同代码在不同操作系统上出现的各种问题，用JMM屏蔽掉了各种硬件和操作系统带来的差异。让Java的并发编程可以做到跨平台。

        JMM 规定所有的变量都会存储在主内丛中，在操作的时候，需要从主内存中复制一份到线程内存（CPU内存），让线程内部做计算。=然后再写回主内存中(也不一定完全是这样)=

        **原子性的定义：原子性是指一个操作是不可分割的，不可中断的，一个线程在执行时，另外一个线程不会影响到他。**

        并发编程的原子性用代码阐述：

        ```java
        package org.example;

        public class Main {

            private static int count;

            public static void increment() {
        	try {
        	    Thread.sleep(10);
        	} catch (InterruptedException e) {
        	    e.printStackTrace();
        	}
        	count++;
            }

            public static void main(String[] args) throws InterruptedException{
        	Thread t1 = new Thread(() -> {
        	    for (int i = 0; i < 100; i++) {
        		increment();
        	    }
        	});
        	Thread t2 = new Thread(() ->{
        	    for (int i = 0; i < 100; i++) {
        		increment();
        	    }
        	});
        	t1.start();
        	t2.start();
        	t1.join();
        	t2.join();
        	System.out.println(count);
            }
        }
        ```

        我们可以根据结果看出这玩意确实存在了不小的问题，那么我们该如何解决呢？

    <!--list-separator-->

    -  保证并发操作的原子性

        <!--list-separator-->

        -  synchronized

            那么最常规的方式肯定还是我们的synchronized锁的方式，当你在执行线程任务的时候，你必须持有我的synchronized锁才能继续执行，不然的话你就只能等在那里，这也保证了并发操作的原子性。

              ![](/ox-hugo/2023-02-25_13-29-05_screenshot.png)
            我们在方法上追加synchronized关键字或者采用同步代码块的方式来保证原子性。

            synchronized可以避免多线程同时操作同一临界资源，同一时间点，只会有一个线程在操作临界资源
            ![](/ox-hugo/2023-02-25_13-35-57_screenshot.png)

        <!--list-separator-->

        -  CAS

            CAS(compare and swap)比较和交换，他是一条CPU的并发原语。

            它在替换内存的某个位置的值时，首先查看内存中的值与预期值是否一致，如果一致，执行替换操作。这个操作是一个原子性操作。

            Java中基于Unsafe的类提供了对CAS的操作的方法，JVM会帮助我们将方法实现CAS汇编指令。

            但是要清楚CAS只是比较和交换，在获取原值的这个操作上，需要你自己实现。

            ```java
            package org.example;

            import java.util.concurrent.atomic.AtomicInteger;

            public class Main {


                private static AtomicInteger count = new AtomicInteger(-1);

                public static void main(String[] args) throws InterruptedException{
            	Thread t0 = new Thread(() -> {
            	    for (int i = -1; i < 100; i++) {
            		count.incrementAndGet();
            	    }
            	});
            	Thread t1 = new Thread(() ->{
            	    for (int i = -1; i < 100; i++) {
            		count.incrementAndGet();
            	    }
            	});
            	t0.start();
            	t1.start();
            	t0.join();
            	t1.join();
            	System.out.println(count);
                }
            }
            ```

            结果是我们期望的200，正常输出200。

            **CAS的缺点** ：CAS只能保证对一个变量的操作是原子性的，无法实现对多行代码实现原子性。

            **CAS的问题** ：

            -   **ABA问题** ：问题如下，可以引入版本号的方式，来解决ABA的问题。Java中提供了一个类在CAS时，针对各个版本追加版本号的操作。AtomicStampeReference

        <!--list-separator-->

        -  Lock锁

            Lock锁是JDK1.5研发出的一个机制，它的性能相比synchronized在JDK1.5时期，性能好了很多，但是在JDK1.6对synchronized做了优化之后，性能相差其实不大，但是如果涉及并发比较多的时候，推荐使用ReentrantLock锁，性能会更好一些

        <!--list-separator-->

        -  ThreadLocal

            这只是一种方式，其实本质上ThreadLocal和原子性没有太大的关系，但是我们为了保证原子性其实就是让线程去操作临界资源，那么我们就可以让ThreadLocal去控制每个线程操作属于自己的代码

<!--list-separator-->

-  可见性

    // TODO 这里可以开一个，后续会单独写一篇文章说这个是怎么回事可见性问题是基于CPU位置出现的，CPU处理速度非常快，相对CPU来说，去主内丛获取数据这个事情太慢了，CPU就提供了L1, L2, L3的三级缓存，每次去主内存拿完数据后，就会存储到CPU的三件缓存中，没丛去三级缓存拿数据，效率肯定会提升。

    这样就会带来一个问题，现在的CPU都是多核的，每个线程的工作内存（CPU的三级缓存）都是独立的，会告知每个线程中做修改时，只改自己的工作内存，没有及时的同步到主内存，导致数据不一致的问题。

    <!--list-separator-->

    -  volatile

        直接给变量一个volatile即可

    <!--list-separator-->

    -  synchronized

        加锁

    <!--list-separator-->

    -  LocK

        和上面的synchronized同理，只不过实现的方式不一样

    <!--list-separator-->

    -  Final

        直接让它改不了，就没有可见性的问题了

<!--list-separator-->

-  有序性

    在Java中，.java文件中的内容会被编译，在执行前需要再次转为CPU可以识别的指令，CPU在执行这些指令时，为了提升执行效率，在不影响最终结果的前提下（满足一些要求），会对指令进行重排

    <!--list-separator-->

    -  volatile

        如果需要让程序对某一个属性的操作不出现指令重排，除了满足happens－before原则之外，还可以基于volatile修饰属性，从而对这个属性的操作，就不会出现指令重排了。

        那么它是如何实现的呢；

        原因在于他会在两个操作之间添加一道内存屏障，你可以简单的看成一道指令，有了这个指令就可以避免上下执行得到其他指令进行重排了。
