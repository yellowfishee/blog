+++
title = "23种设计模式(Java)"
date = 2023-02-23
lastmod = 2023-02-26T15:55:00+08:00
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

{{< figure src="/ox-hugo/temp-1.png" >}}

属性/方法名称前面家的加号和减号表示了这个属性/方法的可见性，UML类图中表示可见性的符号有三种：

-   ＋ ：表示public
-   － ： 表示private
-   ＃ ：表示protected

属性的完整表示方式是： **可见性** **名称** ： **类型** 【= 缺省值】

方法的完整表示方式是： **可见性** **名称 : 类型** **【： 返回类型】**


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


### 工厂方法模式 {#工厂方法模式}

首当其冲的是最简单的一种设计模式——工厂方法模式，我们知道，如果需要创建一个对象，那么最简单的方式就是直接new一个即可。而工厂方法模式代替了传统的直接new的形式，那么为什么要替代传统的new形式呢？

可以想象一下，如果所有的对象我们都通过new的方式去创建，那么当我们的程序中大量使用此对象时，突然有一天这个对象的构造方法或是类名发生了修改，那我们岂不是得挨个去进行修改？根据迪米特法则，我们应该尽可能地少与其他类进行交互，所以我们可以将那些需要频繁出现的对象创建，封装到一个工厂类中，当我们需要对象时，直接调用工厂类中的工厂方法来为我们生成对象，这样，就算类出现了变动，我们也只需要修改工厂中的代码即可，而不是大面积地进行修改。

同时，可能某些对象的创建并不只是一个new就可以搞定，可能还需要更多的步骤来准备构造方法需要的参数，所以我们来看看如何使用简单工厂模式来创建对象，既然是工厂，那么我们就来创建点工厂需要生产的东西：

```java
public abstract class Fruit {   //水果抽象类
    private final String name;

    public Fruit(String name){
	this.name = name;
    }

    @Override
    public String toString() {
	return name+"@"+hashCode();   //打印一下当前水果名称，还有对象的hashCode
    }
}
```

```java
public class Apple extends Fruit{   //苹果，继承自水果

    public Apple() {
	super("苹果");
    }
}
```

```java
public class Orange extends Fruit{  //橘子，也是继承自水果
    public Orange() {
	super("橘子");
    }
}
```

正常情况下，我们直接new就可以得到对象了：

```java
public class Main {
    public static void main(String[] args) {
	Apple apple = new Apple();
	System.out.println(apple);
    }
}
```

现在我们将对象的创建封装到工厂中：

```java
public class FruitFactory {
    /**
     * 这里就直接来一个静态方法根据指定类型进行创建
     * @param type 水果类型
     * @return 对应的水果对象
     */
    public static Fruit getFruit(String type) {
	switch (type) {
	    case "苹果":
		return new Apple();
		case "橘子":
		return new Orange();
	    default:
		return null;
	}
    }
}
```

现在我们就可以使用此工厂来创建对象了：

```java
public class Main {
    public static void main(String[] args) {
	Fruit fruit = FruitFactory.getFruit("橘子");   //直接问工厂要，而不是我们自己去创建
	System.out.println(fruit);
    }
}
```

不过这样还是有一些问题，我们前面提到了开闭原则，一个软件实体，比如类、模块和函数应该对扩展开放，对修改关闭，但是如果我们现在需要新增一种水果，比如桃子，那么这时我们就得去修改工厂提供的工厂方法了，但是这样是不太符合开闭原则的，因为工厂实际上是针对于调用方提供的，所以我们应该尽可能对修改关闭。

所以，我们就利用对扩展开放，对修改关闭的性质，将简单工厂模式改进为工厂方法模式，那现在既然不让改，那么我们就看看如何去使用扩展的形式：

```java
public abstract class FruitFactory<T extends Fruit> {   //将水果工厂抽象为抽象类，添加泛型T由子类指定水果类型
    public abstract T getFruit();  //不同的水果工厂，通过此方法生产不同的水果
}
```

```java
public class AppleFactory extends FruitFactory<Apple> {  //苹果工厂，直接返回Apple，一步到位
    @Override
    public Apple getFruit() {
	return new Apple();
    }
}
```

这样，我们就可以使用不同类型的工厂来生产不同类型的水果了，并且如果新增了水果类型，直接创建一个新的工厂类就行，不需要修改之前已经编写好的内容。

```java
public class Main {
    public static void main(String[] args) {
	test(new AppleFactory()::getFruit);   //比如我们现在要吃一个苹果，那么就直接通过苹果工厂来获取苹果
    }

    //此方法模拟吃掉一个水果
    private static void test(Supplier<Fruit> supplier){
	System.out.println(supplier.get()+" 被吃掉了，真好吃。");
    }
}
```

这样，我们就简单实现了工厂方法模式，通过工厂来屏蔽对象的创建细节，使用者只需要关心如何去使用对象即可。
