+++
title = "23种设计模式(Java)"
date = 2023-02-23
lastmod = 2023-03-05T20:41:15+08:00
tags = ["Java", "设计模式"]
draft = false
katex = true
[menu]
  [menu.main]
    identifier = "23种设计模式-java"
    parent = "docs"
    weight = 3
+++

## UML {#uml}

统一建模语言（Unified Modeling Language，UML）是用来设计软件的可视化建模语言。它的特点是简单、统一、图形化、能表达软件设计中的动态与静态信息。

UML从目标系统的不同角度出发，定义了用例图、类图、对象图、状态图、活动图、时序图、协作图、构件图、部署图等等9种。


### 类图概述 {#类图概述}

类类图（Class diagram）是象时了模型的静态结构，特别是模型中存在的类、类的内部结构以及它们与其他类的关心等。类图不显示暂时性的信息。类图是面向对象建模的主要组成部分。


### 类图的作用 {#类图的作用}

-   在软件工程中，类图是一种静态的结构图，描述了系统的类的集合，类的属性和类之间的关心，可以简化了人们对系统的理解；
-   类图是系统分析的设计阶段的中要产物，是系统编码和测试的中要模型。


### 类图表示法 {#类图表示法}


#### 类的表示方式 {#类的表示方式}

在UML类图中，类使用包含类名、属性（field）和方法（method）且带有分割线的矩形来表示，比如下图表示一个Employee类，它包含name，age和address这三个属性，以及work()方法。

```nil
digraph {
      node [shape=record, fontname="Inconsolata, Consolas", fontsize=10, penwidth=0.5]

      Account [label="{
  Employee
  |
  - name : String\l
  - age : int\l
  - address : String\l
  |
  + work() : void\l
  }"]

  }
```

属性/方法名称前面家的加号和减号表示了这个属性/方法的可见性，UML类图中表示可见性的符号有三种：

-   ＋ ：表示public
-   － ： 表示private
-   ＃ ：表示protected

属性的完整表示方式是： **可见性** **名称** ： **类型** 【= 缺省值】

方法的完整表示方式是： **可见性** **名称 : 类型** **【： 返回类型】**

> 注意点：
>
> 1.  中括号中的内容表示是可选的。
> 2.  也有将类型放在变量名前面，返回值放在方法名前面的做法

**举个例子：**

```:file
  digraph {
    node [shape=record, fontname="Inconsolata, Consolas", fontsize=10, penwidth=0.5]

    Account [label="{
Demo
|
|
+ method() : void \l
- method1() : String \l
# method2(int par1, String par2) : int \l
}"]

}
```

这个图就不多解释了，有了上面的例子应该很简单就可以看出来这个是什么意思。


#### 类与类之间关系的表示方式 {#类与类之间关系的表示方式}

<!--list-separator-->

-  关联关系

    关联关系是对象之间的一种引用关系，用于表示一类对象与另一类对象之间的联系，如老师和学生、师傅和徒弟、丈夫和妻子等。关联关系是类与类之间最常用的一种关系，分为一般关联关系、聚合关系和组合关系。我们先介绍一般关联。

    **1. 单向关联**

    ```:file
    digraph UML {
        node [shape=record, fontname="Inconsolata, Consolas", fontsize=10, penwidth=0.5]

        Customer [label="{
    Customer
    |
    - address : Address
    |
    }"]

        Address [label="{
    Address
    |
    |
    }"]

    Customer -> Address
    }
    ```

    在UML类图中单向关联用一个带箭头的实线表示，上图表示每个顾客都有一个地址，这通过让Customer类持有一个类型为Address的成员变量类实现。

    **2. 双向关联**


## 面向对象的设计原则 {#面向对象的设计原则}

我们在进行软件开发时，不仅仅需要将最基本的业务给完成，还要考虑整个项目的可维护性和可复用性，我们开发的项目不单单需要我们自己来维护，同时也需要其他的开发者一起来进行共同维护，因此我们在编写代码时，应该尽可能的规范。如果我们在编写代码时不注重这些问题，整个团队项目就像一座屎山，随着项目的不断扩大，整体结构只会越来越遭。

甚至到最后你会发现，我们的程序居然是稳定运行在BUG之上的...


### 单一职责原则 {#单一职责原则}

单一职责原则（Simple Responsibility Pinciple，SRP）是最简单的面向对象设计原则，它用于控制类的粒度大小。

> 一个对象应该只包含单一的职责，并且该职责被完整地封装在一个类中。

其实简单来说就是一个类的任务应该是属于自己所被定义的功能的，下面我们看一个例子

```java
package org.example;

public class People {
    public void Coding() {
	System.out.println("i'm coding");
    }

    public void Riding() {
	System.out.println("i'm riding");
    }

    public void Working() {
	System.out.println("i'm working");
    }
}
```

在上面的例子中我们把三种不同类型的人的工作放到了People这个类中，这明显是不符合SRP这个原则的，如果我们后续的对象需要进行修改，那么我们都需要修改People这个类，所以我们要避免这个错误的行为，我们可以通过extends来指定相同的属性，然后各自定义自己的方法，也可以各自做一个新类。下面我们使用前者来进行修改。

```java
package org.example;

public class People {
    public void doingNow() {
    }
}

class Rider extends People {
    @Override
    public void doingNow() {
	System.out.println("i'm riding");
    }
}

class Worker extends People {
    @Override
    public void doingNow() {
	System.out.println("i'm working");
    }
}

class Coder extends  People {
    @Override
    public void doingNow() {
	System.out.println("i'm coding");
    }
}
```

这样每一个类的行为就得到了细致的划分，并且他们并没有脱离People这个范畴，这是一个相对来说合理的优化。并且我们将类的粒度进行更近一步的划分，这样就很清晰了，包括我们以后在设计Mapper、Service、Controller等等，根据不同的业务进行划分，都可以采用单一职责原则，以它作为我们实现高内聚低耦合的指导方针。实际上我们的微服务也是参考了单一职责原则，每个微服务只应担负一个职责。


### 开闭原则 {#开闭原则}

开闭原则（Open Close Principle）也是重要的面向对象设计原则。

> 软件实体应当对扩展开放，对修改关闭。

一个软件实体，比如类、模块和函数应该对扩展开放，对修改关闭。其中，对扩展开放是针对提供方来说的，对修改关闭是针对调用方来说的。

比如我们的程序员分为Java程序员、C#程序员、c++程序员、PHP程序员、前端程序员等，而他们要做的都是去打代码，而具体如何打代码是根据不同语言的程序员来决定的，我们可以将程序员打代码这一个行为抽象成一个统一的接口或是抽象类，这样我们就满足了开闭原则的第一个要求：对扩展开放，不同的程序员可以自由地决定他们该如何进行编程。而具体哪个程序员使用什么语言怎么编程，是自己在负责，不需要其他程序员干涉，所以满足第二个要求：对修改关闭，比如：

其实上面完善后的例子就是一个开闭原则的一个实现。这里给了一个更合理的修改，我们将People类修改成了abstract抽象方法。

```java
package org.example;

public abstract class People {
    public abstract void doingNow();
}

class Rider extends People {
    @Override
    public void doingNow() {
	System.out.println("i'm riding");
    }
}

class Worker extends People {
    @Override
    public void doingNow() {
	System.out.println("i'm working");
    }
}

class Coder extends  People {
    @Override
    public void doingNow() {
	System.out.println("i'm coding");
    }
}
```

通过对People暴露出来的doingNow()方法进行实现，从而让他们自己去实现自己要干什么，而自己不去干涉，这样就可以根据项目的需求进行灵活的扩展了。


### 里氏替换原则 {#里氏替换原则}

里氏替换原则（Liskov Substitution Principle）是对子类型的特别定义。它由芭芭拉·利斯科夫（Barbara Liskov）在1987年在一次会议上名为 "数据的抽象与层次" 的演说中首先提出。

> 所有引用基类的地方必须能透明地使用其子类的对象。

简单的说就是，子类可以扩展父类的功能，但不能改变父类原有的功能：

1.  子类可以实现父类的抽象方法，但不能覆盖父类的非抽象方法。
2.  子类可以增加自己特有的方法。
3.  当子类的方法重载父类的方法时，方法的前置条件（即方法的输入/入参）要比父类方法的输入参数更宽松。
4.  当子类的方法实现父类的方法时（重写/重载或实现抽象方法），方法的后置条件（即方法的输出/返回值）要比父类更严格或与父类一样。

我们举一个例子

```java
package org.example;

public abstract class Coder {
    public void coding() {
	System.out.println("i can code");
    }

    class JavaCoder extends Coder {
	public void PlayGame() {
	    System.out.println("i'm play gta");
	}
    }
}
```

可以看到，我们的JavaCode继承自抽象类Coder，但是我们没有重写Coder中coding这个方法，并且还自己扩展了属于自己的方法，所以是符合里氏替换原则的。但是我们再来看看下面这个例子。

```java
package org.example;

public abstract class Coder {
    public void coding() {
	System.out.println("i can code");
    }

    class JavaCoder extends Coder {
	public void PlayGame() {
	    System.out.println("i'm play gta");
	}

	@Override
	public void coding() {
	    System.out.println("我不想敲代码了");
	}
    }
}
```

我们让JavaCoder这个子类对父类的coding方法进行了重写，我们的子类已经不具备父类最初的功能了，所以我们这里不符合里氏替换原则。

所以对于这种情况我们可以不再继承自Coder，而是去继承People类。

里氏替换原则也是避开原则实现开闭原则重要方式之一。


### 依赖倒转原则 {#依赖倒转原则}

依赖倒转原则（Dependence Inversion Principle）也是我们一直在使用的，最明显的就是我们的Spring框架了。

> 高层模块不应依赖于底层模块，它们都应该依赖抽象。抽象不应依赖于细节，细节应该依赖于抽象。

下面我们来看一段代码演示：

```java
package org.example;

public class Main {
    public static void main(String[] args) {

    }

    static class UserMapper {
    }

    static class UserService{
	UserMapper mapper = new UserMapper();
    }

    static class UserController {
	UserService service = new UserService();
    }
}
```

但是如果有一天我们的需求发生了改变，需要对代码进行优化。导致代码变成了下面这个样子：

```java
package org.example;

public class Main {
    public static void main(String[] args) {

    }

    static class UserMapper {
    }

    // 直接修改这里
    static class UserNewService{
	UserMapper mapper = new UserMapper();
    }

    static class UserController {
	// 这里会受到影响
	UserService service = new UserService();
	UserNewService serv =new UserNewService();
    }
}
```

我们发现，我们的各个模块之间实际上是具有强关联的，一个模块是直接指定依赖于另一个模块，虽然这样结构清晰，但是底层模块的变动，会直接影响到其他依赖于它的高层模块，如果我们的项目变得很庞大，那么这样的修改将是一场灾难。

而有了Spring框架之后，我们的开发模式就发生了变化：

```java
package org.example;

import javax.annotation.Resource;

public class Main {
    public static void main(String[] args) {

	UserController userController = new UserController();
    }

    interface UserMapper {
	// 我们只在这里面定义方法
    }

    static class UserMapperImpl implements UserMapper {

    }

    interface UserService {

    }

    static class UserServiceImpl implements UserService {
	@Resource
	UserMapper userMapper;
    }

    static class UserController {
	@Resource
	UserService service;
    }

}
```

可以看到，通过使用接口，我们就可以将原有的强关联给弱化，我们只需要知道接口中定义了什么方法然后去使用即可，而具体的操作由接口的实现类来完成，并由Spring来为我们注入，而不是我们通过硬编码的方式去指定。


### 接口隔离原则 {#接口隔离原则}

接口隔离原则（Interface Segregation Principle, ISP）实际上是对接口的细化。

> 客户端不应依赖那些它不需要的接口。

我们在定义接口的时候，一定要注意控制接口的粒度，比如下面的例子：

```java

interface Device {
    String getCpu();
    String getType();
    String getMemory();
}

//电脑就是一种电子设备，那么我们就实现此接口
class Computer implements Device {

    @Override
    public String getCpu() {
	return "i9-12900K";
    }

    @Override
    public String getType() {
	return "电脑";
    }

    @Override
    public String getMemory() {
	return "32G DDR5";
    }
}

//电风扇也算是一种电子设备
class Fan implements Device {

    @Override
    public String getCpu() {
	return null;   //就一个破风扇，还需要CPU？
    }

    @Override
    public String getType() {
	return "风扇";
    }

    @Override
    public String getMemory() {
	return null;   //风扇也不需要内存吧
    }
}
```

虽然我们定义了一个Device接口，但是由于此接口的粒度不够细，虽然比较契合电脑这种设备，但是不适合风扇这种设备，因为风扇压根就不需要CPU和内存，所以风扇完全不需要这些方法。这时我们就必须要对其进行更细粒度的划分：

```java
interface SmartDevice {   //智能设备才有getCpu和getMemory
    String getCpu();
    String getType();
    String getMemory();
}

interface NormalDevice {   //普通设备只有getType
    String getType();
}

//电脑就是一种电子设备，那么我们就继承此接口
class Computer implements SmartDevice {

    @Override
    public String getCpu() {
	return "i9-12900K";
    }

    @Override
    public String getType() {
	return "电脑";
    }

    @Override
    public String getMemory() {
	return "32G DDR5";
    }
}

//电风扇也算是一种电子设备
class Fan implements NormalDevice {
    @Override
    public String getType() {
	return "风扇";
    }
}
```


### 合成复用原则 {#合成复用原则}

合成复用原则（Composite Reuse Principle）的核心就是委派。

> 优先使用对象组合，而不是通过继承来达到复用的目的。

在一个新的对象里面使用一些已有的对象，使之成为新对象的一部分，新的对象通过向这些对象的委派达到复用已有功能的目的。实际上我们在考虑将某个类通过继承关系在子类得到父类已经实现的方法之外（比如A类实现了连接数据库的功能，恰巧B类中也需要，我们就可以通过继承来获得A已经写好的连接数据库的功能，这样就能直接复用A中已经写好的逻辑）我们应该应该优先地去考虑使用合成的方式来实现复用。

比如我们下面的例子：

```java
class A {
    static void connectDB() {
	System.out.println("connect database");
    }

}

class B extends A {
    void test() {
	System.out.println("in B, connect database yes");
	// 直接调用父类的方法
	connectDB();
    }
}
```

虽然这样看起来没啥毛病，但是还是存在我们之前说的那个问题，耦合度太高了。

可以看到通过继承的方式实现复用，我们是将类B直接指定继承自类A的，那么如果有一天，由于业务的更改，我们的数据库连接操作，不再由A来负责，而是由新来的C去负责，那么这个时候，我们就不得不将需要复用A中方法的子类全部进行修改，很显然这样是费时费力的。

并且还有一个问题就是，通过继承子类会得到一些父类中的实现细节，比如某些字段或是方法，这样直接暴露给子类，并不安全。

所以，当我们需要实现复用时，可以优先考虑以下操作：

```java

class A {
    public void connectDB() {
	System.out.println("connect database");
    }
}

class B  {
    void test(A a) {
	System.out.println("in B, connect database yes");
	a.connectDB();
    }
}
```

或者是下面这样的操作：

```java
class A {
    public void connectDatabase(){
	System.out.println("我是连接数据库操作！");
    }
}

class B {

    A a;
    public B(A a){   //在构造时就指定好
	this.a = a;
    }

    public void test(){
	System.out.println("我是B的方法，我也需要连接数据库！");
	a.connectDatabase();   //也是通过对象A去执行
    }
}
```

当然你也可以抽象一个接口出来，那么它会变得更加的灵活；


### 迪米特法则 {#迪米特法则}

迪米特法则（Law of Demeter）又称最少知识原则，是对程序内部数据交互的限制。

> 每一个软件单位对其他单位都只有最少的知识，而且局限于那些与本单位密切相关的软件单位。

简单来说就是，一个类/模块对其他的类/模块有越少的交互越好。当一个类发生改动，那么，与其相关的类（比如用到此类啥方法的类）需要尽可能少的受影响（比如修改了方法名、字段名等，可能其他用到这些方法或是字段的类也需要跟着修改）这样我们在维护项目的时候会更加轻松一些。

其实说白了，还是降低耦合度，我们还是来看一个例子：

```java
public class Main {
    public static void main(String[] args) throws IOException {
	Socket socket = new Socket("localhost", 8080);   //假设我们当前的程序需要进行网络通信
	Test test = new Test();
	test.test(socket);   //现在需要执行test方法来做一些事情
    }

    static class Test {
	/**
	 * 比如test方法需要得到我们当前Socket连接的本地地址
	 */
	public void test(Socket socket){
	    System.out.println("IP地址："+socket.getLocalAddress());
	}
    }
}
```

可以看到，虽然上面这种写法没有问题，我们提供直接提供一个Socket对象，然后再由test方法来取出IP地址，但是这样显然违背了迪米特法则，实际上这里的test方法只需要一个IP地址即可，我们完全可以直接传入一个字符串，而不是整个Socket对象，我们需要保证与其他类的交互尽可能的少。

就像我们在餐厅吃完了饭，应该是我们自己扫码付款，而不是直接把手机交给老板来帮你操作付款。

要是某一天，Socket类中的这些方法发生修改了，那我们就得连带着去修改这些类，很麻烦。

所以，我们来改进改进：

```java
public class Main {
    public static void main(String[] args) throws IOException {
	Socket socket = new Socket("localhost", 8080);
	Test test = new Test();
	test.test(socket.getLocalAddress().getHostAddress());  //在外面解析好就行了
    }

    static class Test {
	public void test(String str){   //一个字符串就能搞定，就没必要丢整个对象进来
	    System.out.println("IP地址："+str);
	}
    }
}
```


## 创建型设计模式 {#创建型设计模式}

软件设计模式（Design pattern），又称设计模式，是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。使用设计模式是为了可重用代码、让代码更容易被他人理解、保证代码可靠性、程序的重用性。


### 单例模式 {#单例模式}

单例模式（Singleton Pattern）是Java中最简单的设计模式之一。这种类型的设计模式属于创建型设计模式，它提供了一种创建对象的最佳方式。

这种模式涉及对一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。


#### 单例模式的结构 {#单例模式的结构}

单例模式主要有影响角色：

-   单例类。只能创建一个实例的类。
-   访问类。使用单例类。


#### 单例模式的实现 {#单例模式的实现}

> 单例模式分为两种：饿汉式：类加载就会导致该单实例对象被创建。懒汉式：类加载不会导致该单实例对象被创建，而是首次使用时才会被创建。

1.  饿汉式－方式1(静态变量方式)

**Singleton.java**

```java
package org.example.singleton;

public class Singleton {
    private static Singleton instance = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
	return instance;
    }
}

```

**Client.java**

```java
package org.example.singleton;

public class Client {
    public static void main(String[] args) {
	Singleton singleton = Singleton.getInstance();
	Singleton singleton1 = Singleton.getInstance();
	System.out.println(singleton == singleton1); // true
    }
}

```

该方式在成员位置声明Singleton类型的静态变量，并创建Singleton类的对象instance。instance对象是随着类的加载而创建的，如果该对象没有被使用，那么就会造成内存的浪费。

1.  饿汉式－方式2(静态代码块方式)

<!--listend-->

```java
package org.example.singleton.demo02;

public class Singleton {
    private Singleton() {
    }

    /**
     * 声明Singleton类型的变量instance
     */
    private static Singleton instance;
    /**
     * 在静态代码块中创建Singleton的实例
     */
    static {
	instance = new Singleton();
    }

    public static Singleton getInstance() {
	return instance;
    }
}
```

测试类同上，所以这里就不放测试类了。

1.  懒汉式－方式1,2（线程安全和不安全放在一起说）

<!--listend-->

```java
package org.example.singleton.demo03;

public class Singleton {
    private Singleton() {

    }

    private static Singleton instance;
    // 这里如果没有synchronized锁的话就是线程不安全的
    public synchronized static Singleton getInstance() {
	if (instance == null) {
	    instance = new Singleton();
	}
	return instance;
    }
}
```

1.  懒汉式－方式3(双重检查锁)

对于 `instance()` 方法来说，绝大部分的操作都是读操作，读操作是线程安全的，所以我们没必要让每一个线程都必须持有锁才能调用该方法，我们需要调整加锁的时机。由此也产生了一种新的实现模式：双重检查锁模式。

```java
package org.example.singleton.demo04;

public class Singleton {
    private Singleton() {}

    /**
     * 保证有序性和可见性
     */
    private volatile static Singleton instance;

    private static Singleton getInstance() {
	if (instance == null) {
	    synchronized (Singleton.class) {
		if (instance == null) {
		    instance = new Singleton();
		}
	    }
	}
	return instance;
    }
}
```

双重检查模式是一种非常好的单例实现模式，解决了单例、性能、线程安全问题，上面的双重检查模式看上去完美无缺，其实是存在一些问题的，在多线程的情况下，可能会出现空指针问题，出现问题的原因是JVM在实例化对象的时候会进行优化和指令重排序操作，要解决双重检查锁模式带来的空指针异常的问题，只需要使用 `volatile` 关键字， `valatile` 关键字可以保证可见性和有序型。

**小结**
添加 `valatile` 关键字之后的双重检查锁模式是一种比较好的单例实现模式，能够保证在多线程的情况下线程安全也不会有性能问题。

1.  懒汉式－方式4(静态内部类的方式)

静态内部类单例模式中实例由内部类创建，由于JVM在加载外部类的过程中，是不会加载静态内部类的，只有内部类的属性/方法被调用时才会被加载，并初始化其静态属性。静态属性由于被 `static` 修饰，保证植被实例化一次，并且严格保证实例化顺序。

```java
package org.example.singleton.demo05;

public class Singleton {
    private Singleton() {}

    private static class SingletonHolder {
	private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
	return SingletonHolder.INSTANCE;
    }

}
```

**说明**
第一次加载Singleton类时不会去初始化INSTANCE，只有第一次调用getInstance，虚拟机加载SingletonHolder并初始化INSTANCE，这样不仅能确保线程安全，也能保证Singleton类的唯一性

**小结**
静态内部类单例模式是一种优秀的单例模式，是开源项目中比较常用的一种单例模式。在没有加任何锁的情况下，保证了多线程下的安全，并且没有任何性能影响和空间浪费

1.  枚举方式

枚举方式来实现单例模式是极力推荐的，因为枚举类时线程安全的，并且之后装载一次，设计者充分利用了枚举的这个特性来实现单例模式，枚举的写法非常简单，而且枚举类型时所有单例实现中唯一一种不会被破坏的单例实现模式。

```java
package org.example.singleton.demo06;

public enum Singleton {
    INSTANCE;
}

```

当然，这个是不考虑内存空间浪费的情况下


#### 存在的问题 {#存在的问题}

<!--list-separator-->

-  问题的演示

    破坏单例模式：

    使上面定义的单例类()可以创建多个对象，枚举方式除外。有两种方式，分别是序列化和反射。

    -   序列化和反序列化

    这里的Singleton随便采取之前的任何一种即可。（除了enum方式的）

    ```java
    package org.example.singleton.demo07;

    import java.io.FileInputStream;
    import java.io.FileOutputStream;
    import java.io.ObjectInputStream;
    import java.io.ObjectOutputStream;

    public class Client {
        public static void main(String[] args) throws Exception {
    	// writeObject2File();
    	readObjectFromFile();
    	readObjectFromFile();
        }

        public static void readObjectFromFile() throws Exception{
    	// read object from file
    	ObjectInputStream ois = new ObjectInputStream(new FileInputStream("/home/yellowfish/3.txt"));
    	Singleton instance = (Singleton) ois.readObject();
    	System.out.println(instance);
    	ois.close();
        }

        public static void writeObject2File() throws Exception {
    	Singleton instance = Singleton.getInstance();
    	// write object to file
    	ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("/home/yellowfish/3.txt"));
    	oos.writeObject(instance);
    	oos.close();
        }
    }
    ```

    根据上面的例子我们可以发现，我们写进文件中的object读出来两次之后，输出的地址是不一样的地址，由此可以证明，我们的序列化操作破坏了单例模式。

    -   反射操作
        ```java
        package org.example.singleton.demo08;

        import java.lang.reflect.Constructor;

        public class Client {
            public static void main(String[] args) throws Exception {
        	// 获取字节码对象
        	Class clazz = Singleton.class;

        	// 获取无参构造方法
        	Constructor cons = clazz.getDeclaredConstructor();

        	// 取消访问检查
        	cons.setAccessible(true);

        	// 创建对象
        	Singleton s1 = (Singleton) cons.newInstance();
        	Singleton s2 = (Singleton) cons.newInstance();

        	/**
        ​	 * false
        	 */
        	System.out.println(s1 == s2);
            }
        }

        ```

<!--list-separator-->

-  问题的解决

    -   序列化、反序列化方式破坏单例模式的解决方法在Singleton类中添加 `readResolve()` 方法，在反序列化时被反射调用，如果定义了这个方法，就返回这个方法的值，如果没有定义，则返回新new出来的对象。

        **Singleton类**
        ```java
        package org.example.singleton.demo07;

        import java.io.Serializable;

        public class Singleton implements Serializable {
            private static Singleton instance;

            private Singleton() {}

            private static class SingletonHolder {
        	private static final Singleton INSTANCE = new Singleton();
            }

            public static Singleton getInstance() {
        	return SingletonHolder.INSTANCE;
            }

            // 重写readResolve方法，防止反序列化破坏单例
            private Object readResolve() {
        	return SingletonHolder.INSTANCE;
            }
        }
        ```

    -   反射破坏单例模式的解决办法：
        ```java
        package org.example.singleton.demo08;

        import java.io.Serializable;

        public class Singleton implements Serializable {
            private static Singleton instance;

            /**
        ​     * 这里的构造方法是为了防止反射破坏单例
        ​     * 不同的实现方法，防止反射破坏单例的方式不一样
        ​     * 比如说demo07中的SingletonHolder，就是通过静态内部类的方式
        ​     * 在静态内部内中，如果要防止反射破坏单例，就需要在类中再定义一个flag用来标记是否已经创建过实例
        ​     * 但是这里的方式，就不需要了
             */
            private Singleton() {
        	synchronized (Singleton.class) {
        	    if (instance != null) {
        		throw new RuntimeException("单例模式被侵犯");
        	    }
        	}
            }

            public static Singleton getInstance() {
        	if (instance == null) {
        	    synchronized (Singleton.class) {
        		if (instance == null) {
        		    instance = new Singleton();
        		}
        	    }
        	}
        	return instance;
            }
        }

        ```


#### JDK源码中的单例模式－Runtime类 {#jdk源码中的单例模式-runtime类}

Runtime类就是使用的单例设计模式。

```java
private static Runtime currentRuntime = new Runtime();

public static Runtime getRuntime() {
    return currentRuntime;
}

private Runtime() {
}

```

我们可以通过这一段源代码看出Runtime是基于饿汉式的单例模式的。


### 工厂模式 {#工厂模式}


#### 概述 {#概述}

设计一个咖啡店点餐系统。

设计一个咖啡类（coffee），并定义其两个子类（美式咖啡（AmericanCoffee）和拿铁咖啡（LatteCoffee））；再设计一个咖啡店类，咖啡店具有点咖啡的功能。


#### 简单工厂模式 {#简单工厂模式}
