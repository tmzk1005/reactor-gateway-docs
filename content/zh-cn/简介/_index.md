---
title: "简介"
weight: 1

resources:
  - name: img1
    src: "img1.svg"
    title: "Reactor-Gateway部署架构"
---

{{< toc format=html >}}

# 1. 项目简介

`Reactor-Gateway`主要是作为一个业务型的API网关来设计，而不是一个纯流量型的网关，功能层面类似于
[Spring-Cloud-Gateway](https://github.com/spring-cloud/spring-cloud-gateway)，
但是功能要比`Spring-Cloud-Gateway`更为强大，具有一些`Spring-Cloud-Gateway`所不具有的功能，比如可视化的API路由配置、
访问日志审计、多环境发布、用户权限管理、自定义扩展等。

# 2. 技术架构简介

`Reactor-Gateway`主要包含2个组件：

1. Gateway
2. Dashboard

`Reactor-Gateway`的部署架构如下:

{{< img name="img1" size="large" lazy=false >}}

- `Gateway`负责HTTP流量转发，无状态组件，可以视流量大小任务水平扩展，一般在一组完全相同的`Gateway`节点之前需要部署一层负载均衡（Load Balance），并由一个域名来提供服务。

- `Dashboard`是API信息管理服务，一般部署2到3个节点保证高可用即可。此组件有UI，系统管理可以登陆进系统进行系统管理，
    API发布者登陆进系统进行API的配置和发布，API的使用者可以登陆进系统搜索查找自己需要使用的API。

- `Gateway`组件会注册到`Dashboard`，并持续发送心跳，以便在`Dashboard`界面上可以可视化观测`Gateway`的在线状态以及健康情况。
    同时`Gateway`和`Dashboard`之间会互相通信来同步API信息，以便在配置信息有变更时及时生效。

# 3. 业务场景简介

`Gateway`组件本身类似于`Spring-Cloud-Gateway`，可以并不依赖`Dashboard`而独立作为一个单独组件来使用，它只是单纯的按照配置信息来完成HTTP请求的路由转发；
但是`Dashboard`设计有一套业务规则，下面用一个场景来描述。

在制造业企业中，肯定有不同的部门负责着一些不同的系统，这些系统需要相关交互，在没有一个统一的网关情况下，系统之间直接调用，各个系统需要记住需要调用的其他系统的地址，
而且，一些通用的功能，比如认证、熔断、限流，访问日志审计等，各个系统需要自己造轮子，存在资源浪费，效率底下，因此需要一个用于系统间融合的API网关，`Reactor-Gateway`正是为此场景而打造。

在`Reactor-Gateway`中有一下几个概念：

- API (API)
- 用户 (User)
- 组织（Organization）
- 环境 (Environment)
- API市场 (API market)

## 3.1 API

一个`API`就是一个具体的HTTP请求路由规则，定义了（包括但不仅限于）如下信息：

- http请求method

- http请求url

- 后端请求信息

    后端可能是http服务，grpc服务，mock服务，或者其他被支持的服务后端。

一个API应该具有一个唯一表示`id`

## 3.2 组织

组织（Organization），简单来理解就是一个部门，一组用户（User）的集合。由系统管理员来新增。

## 3.3 用户

`User`是可以通过用户名密码登陆到`Dashboard`的实体，但是注意，`User`不是`API`的所有者，`API`的所有者是一个`组织`，
而属于某个`组织`的用户有权限操作此`组织`的API，此用户新发布的API属于其所属组织，其他组织成员可以修改。

一个用户**需要且只能**属于一个组织。一个API也**需要且只能**属于一个组织。

除了管理员的普通用户，有2种业务角色：API的发布者，API的使用者，或者的者兼有。

## 3.4 环境

就像平时我们开发一般会有”开发环境“，”测试环境“，”预生产环境“，”生产环境“等几套不同的环境。在`Reactor-Gateway`中，
可以由一组`Gateway`节点组成一个环境，在`Dashboard`中发布API时，可以选择把API发布到指定的环境。一般，我们新建一个API，应该先发布到“开发环境”，然后和API的使用方联调，联调通过没有问题后再逐步发布到”测试环境“，”生产环境“。

一个组织的某个环境可以配置一些string到string的键值对，用于在API配置信息中引用。

比如某个组织有一个API，在定义其要路由的后端上游的地址时，其值是`http://${address}/foo/bar`

然后DEV环境，绑定的环境变量配置为：

```json
{
    "address": "1.1.1.1"
}
```

TEST环境绑定的环境变量配置为：

```json
{
    "address": "2.2.2.2"
}
```

同一个API定义，发布到DEV环境和TEST环境，那么请求对用的环境的`Gateway`时，就会被路由到不同的上游后端。

因此，一个组织，应该在定义API时，把不同环境动态变化的部分设置为环境变量，然后在API配置信息中引用。

## 3.5 API市场

所有发布的API的集合构成了`API市场`，作为API的使用者登陆到`Dashboard`，可以搜索查询自己想要使用的API，阅读其使用

# 4. Gateway架构

TODO
