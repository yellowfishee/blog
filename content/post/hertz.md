+++
title = "hertz 快速介绍"
date = 2023-02-27
lastmod = 2023-02-27T02:55:34+08:00
tags = ["go", "goweb", "hertz"]
draft = false
katex = true
[menu]
  [menu.main]
    identifier = "hertz-快速介绍"
    parent = "docs"
    weight = 3
+++

## Hertz {#hertz}


### 概述 {#概述}


#### CloudWeGo-Hertz {#cloudwego-hertz}

Hertz[həːts] 是一个 Golang 微服务 HTTP 框架，在设计之初参考了其他开源框架 fasthttp、gin、echo 的优势， 并结合字节跳动内部的需求，使其具有高易用性、高性能、高扩展性等特点，目前在字节跳动内部已广泛使用。 如今越来越多的微服务选择使用 Golang，如果对微服务性能有要求，又希望框架能够充分满足内部的可定制化需求，Hertz 会是一个不错的选择。


#### 架构设计 {#架构设计}

{{< figure src="/ox-hugo/2023-02-27_02-54-20_screenshot.png" >}}


#### 框架特点 {#框架特点}

-   高易用性

    在开发过程中，快速写出来正确的代码往往是更重要的。因此，在 Hertz 在迭代过程中，积极听取用户意见，持续打磨框架，希望为用户提供一个更好的使用体验，帮助用户更快的写出正确的代码。

-   高性能

    Hertz 默认使用自研的高性能网络库 Netpoll，在一些特殊场景相较于 go net，Hertz 在 QPS、时延上均具有一定优势。关于性能数据，可参考下图 Echo 数据。

四个框架的对比:


### 快速开始 {#快速开始}
