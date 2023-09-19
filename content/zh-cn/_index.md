---
title: "Reactor-Gateway"
geekdocNav: false
geekdocAlign: center
geekdocAnchor: false
---

`Reactor-Gateway`是一个基于[Reactor-Netty](https://github.com/reactor/reactor-netty)实现的**响应式API**网关。参考借鉴了[Spring-Cloud-Gateway](https://github.com/spring-cloud/spring-cloud-gateway)和[Gravitee](https://www.gravitee.io)等其它开源API网关的设计思想。有可视化的API路由配置界面，部署简单，易于配置和使用，并且有不俗的性能。适合于在企业在数字化转型业务中用于不同系统中的融合。`Reactor-Gateway`主要是作为一个**业务型**的API网关来设计的，和流量型的API网关有一定的区别。常用的微服务网关[Spring-Cloud-Gateway](https://github.com/spring-cloud/spring-cloud-gateway)是基于`Spring-Boot`和`Spring-WebFlux`的，而`Spring-WebFlux`的底层也是基于`Reactor-Netty`。此网关借鉴了一些`Spring-Cloud-Gateway`中比较好的设计思想，但是不使用任何Spring相关的框架，而是直接基于`Reactor-Netty`来实现，安装包的大小，资源消耗，性能等方面要优于`Spring-Cloud-Gateway`。

{{< button size="large" relref="简介" >}}简介{{< /button >}}
{{< button size="large" relref="快速开始" >}}快速开始{{< /button >}}

## 功能特点

{{< columns >}}

### 完全开源

`Reactor-Gateway`的代码完全开源[Apache License V2]。

<--->

### 简单易用

部署简单，易于使用，下载 -> 解压 -> 执行启动脚本，即可基于默认配置快速部署启动，可视化界面新建和发布API，操作简单。

<--->

### 插件化

完全插件化的设计，网关的核心只是实现了一套插件运行时，可以开发自定义插件满足特殊需求。

{{< /columns >}}
