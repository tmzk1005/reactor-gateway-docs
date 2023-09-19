---
title: "Docker镜像构建"
weight: 2
---

构建Docker镜像适用于计划要将RGW部署在kubernates的场景，在构建Docker镜像之前，先[构建安装包](/构建/安装包构建/)。

构建Docker镜像非常简单，执行release模块中名为`build-docker-image-rgw`的任务即可：

```sh
./gradlew :release:build-docker-image-rgw
```

构建成功后，Docker image的名称为`reactor-gateway`,tag同项目的版本。

例如：

```txt
REPOSITORY                                                   TAG                   IMAGE ID       CREATED             SIZE
reactor-gateway                                              0.0.1                 ceaf68edf4d0   2 hours ago         803MB
```