---
title: "快速开始"
weight: 2
---

在快速体验RGW的功能之前，需要先安装，部署，运行一个基本的RGW实例。可以选择[单机部署](/部署/单机部署/)，或者基于`docker compose`快速启动一个体验环境。

基于`docker compose`快速启动一个体验环境，需要先准备依赖：

1. Linux环境
2. 安装Docker
3. 安装Docker-Compose

## 1. 构建安装包

需要先构建安装包，参考[安装包构建](/构建/安装包构建/)。

## 2. 启动

执行代码仓库中release模块中名为`docker-compose-up`的Gradle任务：

```bash
./gradlew :release:docker-compose-up
```

> 如果是第一次启动，可能会耗时比较久，要下载的资源比较多，请等待，若因网络原因中断，重新执行任务即可。

待启动成功后，可以进入到`release/docker/rgw-compose`目录，使用`docker-compose`命令查看各个服务状态：

```sh
cd release/docker/rgw-compose
docker-compose ps
```
输出:
```txt
rgw-compose_access-log-consumer_1   /docker-entrypoint.sh acce ...   Up
rgw-compose_dashboard_1             /docker-entrypoint.sh dash ...   Up      0.0.0.0:7000->7000/tcp,:::7000->7000/tcp
rgw-compose_gateway-dev_1           /docker-entrypoint.sh gate ...   Up      0.0.0.0:8000->8000/tcp,:::8000->8000/tcp
rgw-compose_gateway-prod_1          /docker-entrypoint.sh gate ...   Up      0.0.0.0:8002->8002/tcp,:::8002->8002/tcp
rgw-compose_gateway-test_1          /docker-entrypoint.sh gate ...   Up      0.0.0.0:8001->8001/tcp,:::8001->8001/tcp
rgw-compose_kafka_1                 /docker-entrypoint.sh --ov ...   Up      0.0.0.0:9092->9092/tcp,:::9092->9092/tcp, 0.0.0.0:9093->9093/tcp,:::9093->9093/tcp
rgw-compose_mongodb_1               /docker-entrypoint.sh --bi ...   Up      0.0.0.0:27017->27017/tcp,:::27017->27017/tcp
```

各个服务监听的端口如上所示，各个服务的ip分别为：

| 服务名 | ip | 端口 |
| --- | --- | --- |
| rgw-compose_dashboard_1 | 172.16.105.100 | 7000 |
| rgw-compose_gateway_dev_1 | 172.16.105.110 | 8000 |
| rgw-compose_gateway_test_1 | 172.16.105.111 | 8001 |
| rgw-compose_gateway_prod_1 | 172.16.105.112 | 8002 |
| rgw-compose_access-log-consumer_1 | 172.16.105.120 | 无 |
| rgw-compose_mongodb_1 | 172.16.105.130 | 27017 |
| rgw-compose_kafka_1 | 172.16.105.140 | 9092 |

使用浏览器访问 `http://172.16.105.100:7000/` 登录dashboad。默认的管理员用户名和密码是，`admin` `admin@rgw`，自带的普通用户的用户名和密码是：`rgw1` `rgw1@rgw` 。

新建API需要使用普通用户`rgw1`登录。

## 3. 功能简单体验

1. 新建一个简单的Mock API
2. 使用curl请求新建的API
3. 查看访问日志

<video width="60%" controls>
  <source src="./create-api.webm">
</video>

