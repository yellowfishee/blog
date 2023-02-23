+++
title = "23种设计模式(Java)"
date = 2023-02-23
lastmod = 2023-02-23T19:36:44+08:00
tags = ["Java", "设计模式"]
draft = false
katex = true
[menu]
  [menu.main]
    identifier = "23种设计模式-java"
    parent = "docs"
    weight = 3
+++

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
