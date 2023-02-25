+++
title = "Spring"
date = 2023-02-25
lastmod = 2023-02-25T19:46:46+08:00
tags = ["spring", "springboot", "springmvc", "springcloud", "面试", "java", "微服务"]
draft = false
katex = true
[menu]
  [menu.main]
    identifier = "spring"
    parent = "docs"
    weight = 3
+++

## Spring {#spring}


### 谈谈对Spring的理解 {#谈谈对spring的理解}

Spring是一个2004年发布的一个开源框架，现在已经到了6.0的版本，在这个时候还是纯XML配置的时代，到了现在的纯注解开发，提高了开发者的效率，对开发的帮助是十分大的。

Spring是一个轻量级的IoC和AOP容器框架。是为java应用程序提供基础性服务的一套框架，目的是用于简化企业引用程序的开发，它使得开发者只需要关心业务需求。常见的配置方式有三种：基于XML的配置、基于注解的配置、基于Java的配置

主要由以下及几个模块组成：

1.  Spring Core： 核心类库，提供IoC服务；
2.  Sprint Context：提供框架式的Bean访问方式，以及企业级功能（JNDI、定时任务等）；
3.  Spring AOP：提供AOP服务；
4.  Spring DAO：对JDBC的抽象，简化了数据访问异常的处理；
5.  Spring ORM：对现有ORM框架的支持；
6.  Spring Web：提供了基本的面向Web的综合特性，例如多方文件上传；
7.  Spring Mvc：提供面向Web引用的Model－View－Controller实现。

Spring的好处：

| 序号 | 好处        | 说明                                                                   |
|----|-----------|----------------------------------------------------------------------|
| 1  | 轻量        | Spring是轻量的，基本的版本大约2MB                                      |
| 2  | 控制反转    | Spring通过控制反转实现了松耦合，对象们给出他们的依赖，而不是创建和查找依赖的对象们 |
| 3  | 面向切面编程（AOP） | Spring支持面向切面的变成，并且把引用业务逻辑和系统服务分开。           |
| 4  | 容器        | Spring包含并管理引用中对象的生命周期和配置。                           |
| 5  | MVC容器     | Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。       |
| 6  | 事务管理    | Spring提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）。 |
| 7  | 异常处理    | Spring提供方便的API把具体结束相关的异常（比如由JDBC，Hibernate or JDO抛出的）转化为一致的unchecked异常 |


### Spring 用到了哪些设计模式 {#spring-用到了哪些设计模式}
