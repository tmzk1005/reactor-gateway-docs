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

`Reactor-Gateway`主要包含3个组件：

1. Gateway
2. Dashboard
3. Access-log-consumer

`Reactor-Gateway`的部署架构如下:

{{< img name="img1" size="large" lazy=false >}}

- `Gateway`负责HTTP流量转发，无状态组件，可以视流量的大小来任意水平扩展，一般在一组完全相同的`Gateway`节点之前需要部署一层负载均衡（Load Balance，比如Nginx或者HA），并由一个域名来提供服务。`Gateway`组件会注册到`Dashboard`，并持续发送心跳，以便在`Dashboard`界面上可以可视化观测`Gateway`的在线状态以及健康情况。同时`Gateway`和`Dashboard`之间会互相通信来同步API信息，以便在配置信息有变更时及时生效。`Gateway`组件会将审计到的访问日志信息写入到kafka。

- `Dashboard`是API管理服务，一般部署2到3个节点保证高可用即可。此组件有UI，系统管理员可以登陆进系统进行系统管理，API发布者登陆进系统进行API的配置和发布，API的使用者可以登陆进系统搜索查找自己需要使用的API。

- `Access-log-consumer`用于异步处理`Gateway`写入到kafka的访问日志，然后写入到mongodb,供`Dashboard`来过滤查询，以及统计分析，监控告警等。

# 3. 业务场景简介

`Gateway`组件本身类似于`Spring-Cloud-Gateway`，可以并不依赖`Dashboard`而独立作为一个单独组件来使用，它只是单纯的按照配置信息来完成HTTP请求的路由转发；但是`Dashboard`设计有一套业务规则，下面用一个场景来描述。

在制造业企业中，肯定有不同的部门负责着一些不同的系统，这些系统需要相关交互，在没有一个统一的API网关的情况下，各系统之间若是直接互相调用，各个系统需要记住需要调用的其他系统的地址，
而且，一些通用的功能，比如认证、熔断、限流，访问日志审计等，各个系统需要自己造轮子，存在资源浪费，效率底下的问题，因此需要一个用于系统间融合的API网关，`Reactor-Gateway`正是为此场景而打造。

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

`User`是可以通过用户名密码登录到`Dashboard`的实体，但是注意，`User`并不是`API`的所有者，`API`的所有者是一个`组织`，而属于某个`组织`的用户有权限操作此`组织`的API，此用户新发布的API属于其所属的组织，同组织的其他成员可以修改。

一个用户**需要且只能**属于一个组织。一个API也**需要且只能**属于一个组织。

此系统中的用户角色设计比较简单，总的来说只有2种角色：管理员和普通用户。

- 管理员

    此系统中的管理员，不是那种具有类似于"root"的权限，啥都能干的管理员，反而有部分操作是只有普通用户才能操作的。其权限原则是：管理员可以执行所有的"只读"操作，比如查看任意用户的API，查看任意用户的访问日志；可以执行"管理类"操作，比如新建环境，新增用户，修改系统配置等；但是不能“替”普通用户执行业务变更，比如新建API,修改API等。

- 普通用户

    普通用户可以操作自己所属的组织所拥有的资源，比如新建API,发布API,查看自己的API被访问的访问日志等，普通用户可以设置为组织管理员，可以管理所属组织的成员。

## 3.4 环境

就像平时我们开发一般会有”开发环境“，”测试环境“，”预生产环境“，”生产环境“等几套不同的环境。在`Reactor-Gateway`中，可以由一组`Gateway`节点组成一个环境，在`Dashboard`中发布API时，可以选择把API发布到指定的环境。一般，我们新建一个API，应该先发布到“开发环境”，然后和API的使用方联调，联调通过没有问题后再逐步发布到”测试环境“，”生产环境“。

一个组织的某个环境可以配置一些string到string的键值对，用于在API配置信息中引用。

比如某个组织有一个API，在定义其要路由的后端上游的地址时，其值是`http://${address}/foo/bar`

DEV环境，绑定的环境变量配置为：

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

同一个API定义，发布到DEV环境和TEST环境，那么发送请求到对应的环境的`Gateway`时，就会被路由到不同的上游后端。

因此，一个组织，应该在定义API时，把不同环境动态变化的部分设置为环境变量，然后在API配置信息中引用。

## 3.5 API市场

所有发布的API的集合构成了`API市场`，作为API的使用者登陆到`Dashboard`，可以搜索查询自己想要使用的API，阅读其使用文档，然后调用API。
