---
title: "单机部署"
weight: 1
---

单机部署，主要用于本地开发调试，以及体验功能，生产环境建议使用[集群部署](/部署/集群部署/)。

单机部署，即把所有的组件都部署在一个机器上。需要Linux环境，并安装好kafka和mongodb。

# 1. 安装kafka

参照kafka的官方文档部署和运行一个使用默认配置的kafka，推荐使用最新版本。

# 2. 安装mongodb

参照mongodb的官方文档部署和运行一个使用默认配置的mongodb，推荐使用6.0版本及以上。

# 3. 解压安装包

[如何构建按转包?](/构建/安装包构建/)

解压安装包到安装目录，比如`/opt`目录

```sh
tar -zxvf reactor-gateway-0.0.1.tgz -C /opt
```

# 4. 启动dashboard

```sh
cd /opt/reactor-gateway-0.0.1
bin/rgw-dashboard.sh
```

dashboard默认监听`7000`端口。默认的管理员用户名和密码是，`admin` `admin@rgw`，自带的普通用户的用户名和密码是：`rgw1` `rgw1@rgw` 。使用浏览器访问 `http://127.0.0.1:7000/`登录。

# 5. 启动gateway

```sh
cd /opt/reactor-gateway-0.0.1
bin/rgw-gateway.sh --environment.id=dev
```

如果要体验多环境效果，可以使用不同的端口号分别启动test和prod环境的gateway实例：

```sh
cd /opt/reactor-gateway-0.0.1
bin/rgw-gateway.sh --environment.id=test --server.port=8001
```

```sh
cd /opt/reactor-gateway-0.0.1
bin/rgw-gateway.sh --environment.id=prod --server.port=8002
```

# 6. 启动access-log-consumer

```sh
cd /opt/reactor-gateway-0.0.1
bin/rgw-access-log-consumer.sh --environments=dev,test,prod
```
