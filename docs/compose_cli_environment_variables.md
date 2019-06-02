# 编写CLI环境变量
预计阅读时间：4分钟

有几个环境变量可供您配置docker compose命令行行为。

以`DOCKER_`开头的变量与用于配置docker命令行客户机的变量相同。如果您使用的是`docker-machine`，那么`eval“$（docker-machine env my-docker-vm）”`命令应该将它们设置为正确的值。（在本例中，`my-docker-vm`是您创建的机器的名称。）

> 注意：其中一些变量也可以使用[环境文件](https://docs.docker.com/compose/env-file/)提供。


## COMPOSE_PROJECT_NAME

设置项目名称。启动时，此值与容器的服务名称一起预先设置。例如，如果您的项目名称是`myapp`，并且包含两个services `db`和`web`，那么compose将分别启动名为`myapp_db_1`和`myapp_web_1`的容器。

设置这是可选的。如果不设置此项，则`COMPOSE_PROJECT_NAME`默认为项目目录的`basename`。另请参见`-p`[命令行选项](https://docs.docker.com/compose/reference/overview/)。


## COMPOSE_FILE

指定Compose文件的路径。如果未提供，则compose将在当前目录中查找名为`docker-compose.yml`的文件，然后依次查找每个父目录，直到找到该名称的文件。

此变量支持由路径分隔符分隔的多个Compose文件（在Linux和MacOS上，路径分隔符为`：`，在Windows上为`;`）。例如：c`COMPOSE_FILE=docker-compose.yml:docker-compose.prod.yml`。路径分隔符也可以使用`COMPOSE_PATH_SEPARATOR`自定义。

另请参见`-f`[命令行选项](https://docs.docker.com/compose/reference/overview/)。


## COMPOSE_API_VERSION

Docker API仅支持来自报告特定版本的客户端的请求。如果使用`docker-compose`收到的`客户端和服务器没有相同的版本错误`，则可以通过设置此环境变量来解决此错误。设置版本值以匹配服务器版本。

设置此变量是为了解决在客户端和服务器版本不匹配的情况下需要临时运行的情况。例如，如果您可以升级客户机，但需要等待升级服务器。

使用这个变量集和已知的不匹配运行会阻止某些Docker功能正常工作。失败的确切特性将取决于Docker客户机和服务器版本。出于这个原因，使用这个变量集运行只是为了解决问题，它没有得到正式的支持。

如果使用此集运行时遇到问题，请通过升级解决不匹配问题，并删除此设置，以在通知支持人员之前查看问题是否已解决。

## DOCKER_HOST

设置Docker守护进程的URL。与`docker`客户机一样，默认为unix:///var/run/docker.sock。

## DOCKER_TLS_VERIRY

当设置为空字符串以外的任何值时，启用与`docker`守护进程的TLS通信。

## DOCKER_CERT_PATH
配置用于TLS验证的`ca.pem`、`cert.pem`和`key.pem`文件的路径。默认为`~/.docker`。

## COMPOSE_HTTP_TIMEOUT
配置对docker守护进程的请求在compose认为失败之前被允许挂起的时间（秒）。默认为60秒。

## COMPOSE_TLS_VERSION
配置用于与`docker`守护进程进行TLS通信的TLS版本。默认为`tlsv1`。支持的值有：`tlsv1`、`tlsv1_1`、`tlsv1_2`。

## COMPOSE_CONVERT_WINDOWS_PATHS
在卷定义中启用从Windows样式到Unix样式的路径转换。Windows上Docker Machine和Docker工具箱的用户应始终设置此设置。默认值为`0`。支持的值：`true`或`1`表示启用，`false`或`0`表示禁用。

## COMPOSE_PATH_SEPARATOR

如果设置，则使用此字符作为路径分隔符来分隔`COMPOSE_FILE`环境变量的值。

## COMPOSE_FORCE_WINDOWS_HOST

如果设置了，则使用[短语法](https://docs.docker.com/compose/compose-file/#short-syntax-3)解析卷声明，假设主机路径是Windows路径，即使compose在基于UNIX的系统上运行。支持的值：`true`或`1`表示启用，`false`或`0`表示禁用。

## COMPOSE_IGNORE_ORPHANS
如果设置了，则compose不会尝试检测项目的孤立容器。支持的值：`true`或`1`表示启用，`false`或`0`表示禁用。

## COMPOSE_PARALLEL_LIMIT
为compose可以并行执行的操作数设置限制。默认值为`64`，不能设置为小于`2`。

## COMPOSE_INTERACTIVE_NO_CLI
如果设置了，则compose不会尝试将docker cli用于交互式运行和执行操作。在上述操作需要使用CLI的Windows上，此选项不可用。支持：`true`或`1`启用，`false`或`0`禁用。



## 相关信息
- [用户指南](https://docs.docker.com/compose/)
- [正在安装Compose](https://docs.docker.com/compose/install/)
- [Compose文件引用](https://docs.docker.com/compose/compose-file/)
- [环境文件](https://docs.docker.com/compose/env-file/)