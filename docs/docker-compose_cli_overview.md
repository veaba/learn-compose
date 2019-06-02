# Docker Compose CLI概述
*预计阅读时间：5分钟*
此页提供`docker-compose`命令的用法信息。

## 命令选项概述和帮助

 您还可以通过运行`docker-compose --help`（命令行中的帮助）来查看这些信息。


 ```shell
 Define and run multi-container applications with Docker.

Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help

Options:
  -f, --file FILE             Specify an alternate compose file
                              (default: docker-compose.yml)
  -p, --project-name NAME     Specify an alternate project name
                              (default: directory name)
  --verbose                   Show more output
  --log-level LEVEL           Set log level (DEBUG, INFO, WARNING, ERROR, CRITICAL)
  --no-ansi                   Do not print ANSI control characters
  -v, --version               Print version and exit
  -H, --host HOST             Daemon socket to connect to

  --tls                       Use TLS; implied by --tlsverify
  --tlscacert CA_PATH         Trust certs signed only by this CA
  --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
  --tlskey TLS_KEY_PATH       Path to TLS key file
  --tlsverify                 Use TLS and verify the remote
  --skip-hostname-check       Don't check the daemon's hostname against the
                              name specified in the client certificate
  --project-directory PATH    Specify an alternate working directory
                              (default: the path of the Compose file)
  --compatibility             If set, Compose will attempt to convert deploy
                              keys in v3 files to their non-Swarm equivalent

Commands:
  build              Build or rebuild services
  bundle             Generate a Docker bundle from the Compose file
  config             Validate and view the Compose file
  create             Create services
  down               Stop and remove containers, networks, images, and volumes
  events             Receive real time events from containers
  exec               Execute a command in a running container
  help               Get help on a command
  images             List images
  kill               Kill containers
  logs               View output from containers
  pause              Pause services
  port               Print the public port for a port binding
  ps                 List containers
  pull               Pull service images
  push               Push service images
  restart            Restart services
  rm                 Remove stopped containers
  run                Run a one-off command
  scale              Set number of containers for a service
  start              Start services
  stop               Stop services
  top                Display the running processes
  unpause            Unpause services
  up                 Create and start containers
  version            Show the Docker-Compose version information
 ```

 您可以使用docker compose binary，`docker-compose[-f<arg>…][options][command][args…]`，在docker容器中构建和管理多个服务。


## 使用`-f`指定一个或多个Compose文件的名称和路径

使用`-f`标志指定Compose配置文件的位置。

## 指定多个Compose文件


您可以提供多个`-f`配置文件。当您提供多个文件时，compose将它们组合成一个配置。compose按照您提供文件的顺序构建配置。后续文件将覆盖并添加到其前置任务。例如，考虑以下命令行：

```shell
$ docker-compose -f docker-compose.yml -f docker-compose.admin.yml run backup_db
```

`docker-compose.yml`文件可能指定`webapp`服务。

```yml
webapp:
  image: examples/web
  ports:
    - "8000:8000"
  volumes:
    - "/data"
```

如果`docker-compose.admin.yml`也指定了相同的服务，那么任何匹配的字段都会覆盖上一个文件。新值，添加到`webapp`服务配置。

```yml
webapp:
  build: .
  environment:
    - DEBUG=1
```

使用带有`-`（dash）的`-f`作为文件名从`stdin`读取配置。当使用`stdin`时，配置中的所有路径都是相对于当前工作目录的。

`-f`标志是可选的。如果不在命令行上提供此标志，则compose将遍历工作目录及其父目录，查找`docker-compose.yml`和`docker-compose.override.yml`文件。必须至少提供`docker-compose.yml`文件。如果两个文件都在同一目录级别上，那么compose将两个文件合并为一个配置。

`docker-compose.override.yml`文件中的配置除了`docker-compose.yml`文件中的值外，还应用于其他值。

### 指定单个Compose文件的路径

您可以使用`-f`标志从命令行或通过在shell或环境文件中设置[COMPOSE_FILE环境变量](https://docs.docker.com/compose/reference/envvars/#compose_file)来指定Compose当前目录以外文件的路径。

对于在命令行使用`-f`选项的示例，假设您正在运行[compose rails示例](https://docs.docker.com/compose/rails/)，并在名为`sandbox/rails`的目录中有`docker-compose.yml`文件。您可以使用`docker-compose pull`这样的命令从任何地方通过使用`-f`标志来获取`db`服务的postgres映像，如下所示：

```shell
docker-compose -f ~/sandbox/rails/docker-compose.yml pull db
```

以下是完整的示例：

```shell
$ docker-compose -f ~/sandbox/rails/docker-compose.yml pull db
Pulling db (postgres:latest)...
latest: Pulling from library/postgres
ef0380f84d05: Pull complete
50cf91dc1db8: Pull complete
d3add4cd115c: Pull complete
467830d8a616: Pull complete
089b9db7dc57: Pull complete
6fba0a36935c: Pull complete
81ef0e73c953: Pull complete
338a6c4894dc: Pull complete
15853f32f67c: Pull complete
044c83d92898: Pull complete
17301519f133: Pull complete
dcca70822752: Pull complete
cecf11b8ccf3: Pull complete
Digest: sha256:1364924c753d5ff7e2260cd34dc4ba05ebd40ee8193391220be0f9901d4e1651
Status: Downloaded newer image for postgres:latest
```

## 使用`-p`指定项目名称

每个配置都有一个项目名称。如果提供`-p`标志，则可以指定项目名称。如果不指定标志，则compose使用当前目录名。另请参见[COMPOSE_PROJECT_NAME环境变量](https://docs.docker.com/compose/reference/envvars/#compose_project_name)。

## 设置环境变量
您可以为各种Docker组合选项设置[环境变量](https://docs.docker.com/compose/reference/envvars/)，包括`-f`和`-p`标志。

例如，[COMPOSE_FILE 环境变量](https://docs.docker.com/compose/reference/envvars/#compose_file)与`-f`标志相关，[COMPOSE_PROJECT_NAME环境变量](https://docs.docker.com/compose/reference/envvars/#compose_project_name)。与`-p`标志相关。

此外，还可以在[环境文件](https://docs.docker.com/compose/env-file/)中设置其中一些变量。

## 下一步
- [CLI环境变量](https://docs.docker.com/compose/reference/envvars/)
- [在文件中声明默认环境变量](https://docs.docker.com/compose/env-file/)


