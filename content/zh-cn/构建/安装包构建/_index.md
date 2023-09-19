---
title: "安装包构建"
weight: 1
---

RGW使用Java语言基于`Open-Jdk-17`开发，使用`Gradle`作为构建工具，前端使用`Vue3`开发。

## 1. 依赖准备

推荐使用较新版本的Linux环境进行编译构建，先准备好依赖：

1. Linux环境
2. Open-Jdk-17或以上版本
3. Gradle 8.3 或以上版本
4. 较新版本的git
5. 确认网络畅通，构建过程中除了会到Maven仓库自动下载Java依赖，也需要自动安装临时的node和npm并下载前端依赖用于编译前端代码。


## 2. 克隆RGW后端代码

```sh
git clone https://github.com/tmzk1005/reactor-gateway.git
```

## 3. 克隆RGW前端代码

RGW的前端代码放在单独的git仓库，并且没有用git的sub module功能和后端代码关联，需要单独clone后放在后端代码仓库的根目录，以便作为一个整体的Gradle项目进行编译构建。

```sh
cd reactor-gateway
git clone https://github.com/tmzk1005/reactor-gateway-ui.git
```

## 4. 生成代码格式规则文件

在编译代码之前，需要执行一个自动生成代码格式规则文件的Gradle任务：

```sh
./gradlew fmtCreateRulesFile
```

## 5. 编译

项目编译非常简单，执行release模块中名为`tgz`的Gradle任务即可：

```sh
./gradlew :release:tgz
```

编译成功后的安装包位于`release/build`目录，文件名为`reactor-gateway-x.y-z.tgz`，`x.y.z`为版本号。

## 6. 安装包结构

将构建成功的RGW的安装包`reactor-gateway-x.y-z.tgz`解压，目录结构大致如下：

```txt
.
├── bin
├── conf
├── libs
├── LICENSE
└── static
```

- bin : 启动脚本
- conf : 配置文件
- libs : jar包
- LICENSE : 开源协议
- static : 前端静态资源

bin目录下有3个启动脚本：

- rgw-dashboard.sh 启动dashboard组件

- rgw-gateway.sh 启动gatewway组件

- rgw-access-log-consumer.sh 启动访问日志消费组件

各个脚本均可以使用 `--help` 参数查看简单的命令行帮助。
