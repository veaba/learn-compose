## Docker compose 概览
预计阅读时间：5分钟
正在查找Compose文件引用？[请在此处查找最新版本](https://docs.docker.com/compose/compose-file/)。

Compose是用于定义和运行多容器Docker应用程序的工具。使用compose，可以使用yaml文件配置应用程序的服务。然后，通过一个命令，您可以从配置中创建和启动所有服务。要进一步了解Compose的所有功能，[请参阅功能列表](https://docs.docker.com/compose/overview/#features)。


Compose工作在所有环境中：生产、分段、开发、测试以及CI工作流。[在常见用例](https://docs.docker.com/compose/overview/#common-use-cases)中，您可以了解更多关于每个用例的信息。


使用compose基本上是一个三步过程：

- 用`Dockerfile`定义应用程序的环境，以便在任何地方复制
- 在`docker-compose.yml`中定义组成应用程序的服务，以便它们可以在单独的环境中一起运行。
- 运行`docker compose up`和compose启动并运行整个应用程序。

一个 `docker-compose.yml`看起来如下：

```yml
version: '3'
services:
  web:
    build:.
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image:redis
  volumes:
    logvolume01:{}
```

有关Compose文件的详细信息，请参阅[Compose文件引用](https://docs.docker.com/compose/compose-file/)。

Compose具有管理应用程序整个生命周期的命令：

- 启动、停止和重建服务
- 查看正在运行的服务的状态
- 传输正在运行的服务的日志输出
- 对服务运行一次性命令

## Compose 文档
- [安装Compose](https://docs.docker.com/compose/install/)
- [入门](https://docs.docker.com/compose/gettingstarted/)
- [开始使用Django](https://docs.docker.com/compose/django/)
- [开始使用Rails](https://docs.docker.com/compose/rails/)
- [开始使用WordPress](https://docs.docker.com/compose/wordpress/)
- [常见问题](https://docs.docker.com/compose/faq/)
- [命令行参考](https://docs.docker.com/compose/reference/)
- [Compose文件引用](https://docs.docker.com/compose/compose-file/)

## 特性

使其有效的Compose特征是：
- [单个主机上的多个隔离环境](https://docs.docker.com/compose/overview/#Multiple-isolated-environments-on-a-single-host)
- [创建容器时保留卷数据](https://docs.docker.com/compose/overview/#preserve-volume-data-when-containers-are-created)
- [仅重新创建已更改的容器](https://docs.docker.com/compose/overview/#only-recreate-containers-that-have-changed)
- [变量和在环境之间移动组合](https://docs.docker.com/compose/overview/#variables-and-moving-a-composition-between-environments)


### 单个主机上的多个隔离环境
compose使用项目名称将环境彼此隔离。您可以在几个不同的上下文中使用此项目名称：
- 在dev主机上，创建单个环境的多个副本，例如当您希望为项目的每个功能分支运行一个稳定的副本时
- 在CI服务器上，为了防止生成相互干扰，可以将项目名称设置为唯一的内部版本号
- 在共享主机或dev主机上，防止可能使用相同服务名的不同项目相互干扰。

默认项目名称是项目目录的基名称。您可以使用`-p`[命令行选项](https://docs.docker.com/compose/reference/overview/) 或`COMPOSE_PROJECT_NAME`[环境变量](https://docs.docker.com/compose/reference/envvars/#compose-project-name)设置自定义项目名称。


### 创建容器时保留卷数据
Compose保留服务使用的所有卷。当docker compose up运行时，如果发现以前运行的任何容器，它会将卷从旧容器复制到新容器。此过程确保在卷中创建的任何数据都不会丢失。如果在Windows计算机上使用Docker Compose，请参阅环境变量，并根据您的特定需求调整必要的环境变量。

### 仅重新创建已更改的容器
Compose缓存用于创建容器的配置。重新启动未更改的服务时，Compose将重新使用现有容器。重新使用容器意味着您可以很快地对环境进行更改。


### 变量和在环境之间移动组合
Compose支持Compose文件中的变量。您可以使用这些变量为不同的环境或不同的用户自定义组合。有关详细信息，请[参阅变量](https://docs.docker.com/compose/compose-file/#variable-substitution)替换。

## 常见用例
Compose可以用很多不同的方式。下面概述了一些常见的用例。

### 开发环境
Compose文件提供了一种方法来记录和配置应用程序的所有服务依赖项（数据库、队列、缓存、Web服务API等）。使用组合命令行工具，您可以用一个命令（`docker compose up`）为每个依赖项创建和启动一个或多个容器。

这些特性一起为开发人员开始一个项目提供了一种方便的方法。Compose可以将多页的“开发人员入门指南”简化为单台机器可读的Compose文件和一些命令。

### 自动化测试环境
任何持续部署或持续集成过程的一个重要部分是自动化测试套件。自动化的端到端测试需要一个运行测试的环境。Compose为您的测试套件提供了一种创建和销毁独立测试环境的方便方法。通过在[Compose文件](https://docs.docker.com/compose/compose-file/)中定义完整的环境，您只需几个命令即可创建和销毁这些环境：

```shell
$ docker-compose uo -d
$ ./run_tests
$ docker-compose down
```

### 单主机部署
传统上，Compose一直专注于开发和测试工作流，但随着每个版本的发布，我们都在开发更多面向生产的特性。可以使用compose部署到远程Docker引擎。Docker引擎可以是提供[Docker Machine](https://docs.docker.com/machine/overview/)的单个实例，也可以是整个[Docker Swarm](https://docs.docker.com/engine/swarm/)集群。

有关使用面向生产的特性的详细信息，请参阅本文档中的在[生产中Compose](https://docs.docker.com/compose/production/)。

## 发行说明
要查看Docker Compose过去和当前版本的详细更改列表，请参阅[更改日志](https://github.com/docker/compose/blob/master/CHANGELOG.md)。

## 获得帮助