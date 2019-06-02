#docker-compose command 

## docker-compose build
```shell
Usage: build [options] [--build-arg key=val...] [SERVICE...]

Options:
    --compress              Compress the build context using gzip.
    --force-rm              Always remove intermediate containers.
    --no-cache              Do not use cache when building the image.
    --pull                  Always attempt to pull a newer version of the image.
    -m, --memory MEM        Sets memory limit for the build container.
    --build-arg key=val     Set build-time variables for services.
    --parallel              Build images in parallel.
```

服务被构建一次，然后被标记，默认为`project_service`。例如，`composetest_db`。如果compose文件指定了一个镜像名称，则会用该名称标记镜像，并预先替换任何变量。参见[变量替换](https://docs.docker.com/compose/compose-file/#variable-substitution)。

如果您更改了服务的dockerfile或其build目录的内容，请运行`docker-compose build`来重建它。

## docker-compose bundle
```shell
Usage: bundle [options]

Options:
    --push-images              Automatically push images for any services
                               which have a `build` option specified.

    -o, --output PATH          Path to write the bundle file to.
                               Defaults to "<project name>.dab".
```
从Compose文件生成分布式应用程序包（DAB）。

镜像必须存储摘要，这需要与Docker注册表交互。如果没有为所有镜像存储摘要，可以使用`docker-compose pull`或`docker-compose push`获取它们。要在捆绑时自动推送镜像，请通过`--push-images`。只有指定了`build`选项的服务才会推送其镜像。


## docker-compose config
```shell
Usage: config [options]

Options:
    --resolve-image-digests  Pin image tags to digests.
    -q, --quiet              Only validate the configuration, don't print anything.
    --services               Print the service names, one per line.
    --volumes                Print the volume names, one per line.
    --hash="*"               Print the service config hash, one per line.
                             Set "service1,service2" for a list of specified services
                             or use the wildcard symbol to display all services.
```

验证并查看Compose文件。
## docker-compose create


```shell
Creates containers for a service.
This command is deprecated. Use the `up` command with `--no-start` instead.

Usage: create [options] [SERVICE...]

Options:
    --force-recreate       Recreate containers even if their configuration and
                           image haven't changed. Incompatible with --no-recreate.
    --no-recreate          If containers already exist, don't recreate them.
                           Incompatible with --force-recreate.
    --no-build             Don't build an image, even if it's missing.
    --build                Build images before creating containers.
```
## docker-compose down
```shell
Usage: down [options]

Options:
    --rmi type              Remove images. Type must be one of:
                              'all': Remove all images used by any service.
                              'local': Remove only images that don't have a
                              custom tag set by the `image` field.
    -v, --volumes           Remove named volumes declared in the `volumes`
                            section of the Compose file and anonymous volumes
                            attached to containers.
    --remove-orphans        Remove containers for services not defined in the
                            Compose file
    -t, --timeout TIMEOUT   Specify a shutdown timeout in seconds.
                            (default: 10)
```
停止容器并删除`up`创建的容器、网络、卷和镜像。

默认情况下，只删除以下内容：
- Compose文件中定义的服务容器
- 在Compose文件的“网络”部分中定义的网络
- 默认网络，如果使用

## docker-compose events
```shell
Usage: events [options] [SERVICE...]

Options:
    --json      Output events as a stream of json objects
```
为项目中的每个容器流式处理容器事件。
使用`--json`标志，每行打印一个json对象，格式为：
```json
{
    "time": "2015-11-20T18:01:03.615550",
    "type": "container",
    "action": "create",
    "id": "213cf7...5fc39a",
    "service": "web",
    "attributes": {
        "name": "application_web_1",
        "image": "alpine:edge"
    }
}
```
可以在[这里](https://docs.docker.com/engine/reference/commandline/events/#object-types)看到使用它接收到的事件。

## docker-compose exec
```shell
Usage: exec [options] [-e KEY=VAL...] SERVICE COMMAND [ARGS...]

Options:
    -d, --detach      Detached mode: Run command in the background.
    --privileged      Give extended privileges to the process.
    -u, --user USER   Run the command as this user.
    -T                Disable pseudo-tty allocation. By default `docker-compose exec`
                      allocates a TTY.
    --index=index     index of the container if there are multiple
                      instances of a service [default: 1]
    -e, --env KEY=VAL Set environment variables (can be used multiple times,
                      not supported in API < 1.25)
    -w, --workdir DIR Path to workdir directory for this command.
```
这相当于`docker exec`。使用此子命令，您可以在服务中运行任意命令。默认情况下，命令是分配tty的，因此可以使用`docker-compose exec web sh`等命令来获取交互式提示。

## docker-compose help
```shell
Usage: help COMMAND
```
显示命令的帮助和用法说明。

## docker-compose kill
```shell
Usage: kill [options] [SERVICE...]

Options:
    -s SIGNAL         SIGNAL to send to the container.
                      Default signal is SIGKILL.
```
通过发送`SIGKILL`信号强制运行容器停止。可以选择传递信号，例如：
```shell
docker-compose kill -s SIGINT
```
## docker-compose logs
```shell
Usage: logs [options] [SERVICE...]

Options:
    --no-color          Produce monochrome output.
    -f, --follow        Follow log output.
    -t, --timestamps    Show timestamps.
    --tail="all"        Number of lines to show from the end of the logs
                        for each container.
```
## docker-compose pause
```shell
Usage: pause [SERVICE...]
```
暂停正在运行的服务容器。他们可以与`docker-compose unpause`。

## docker-compose port

```shell
Usage: port [options] SERVICE PRIVATE_PORT

Options:
    --protocol=proto  tcp or udp [default: tcp]
    --index=index     index of the container if there are multiple
                      instances of a service [default: 1]
```
打印端口绑定的公共端口。
## docker-compose ps
```shell
Usage: ps [options] [SERVICE...]

Options:
    -q, --quiet          Only display IDs
    --services           Display services
    --filter KEY=VAL     Filter services by a property
    -a, --all            Show all stopped containers (including those created by the run command)
```
列出容器。
```shell
$ docker-compose ps
         Name                        Command                 State             Ports
---------------------------------------------------------------------------------------------
mywordpress_db_1          docker-entrypoint.sh mysqld      Up (healthy)  3306/tcp
mywordpress_wordpress_1   /entrypoint.sh apache2-for ...   Restarting    0.0.0.0:8000->80/tcp
```

## docker-compose pull
```shell
Usage: pull [options] [SERVICE...]

Options:
    --ignore-pull-failures  Pull what it can and ignores images with pull failures.
    --parallel              Deprecated, pull multiple images in parallel (enabled by default).
    --no-parallel           Disable parallel pulling.
    -q, --quiet             Pull without printing progress information
    --include-deps          Also pull services declared as dependencies
```

拉取与`docker-compose.yml`或`docker-stack.yml`文件中定义的服务相关联的镜像，但不基于这些镜像启动容器。

例如，假设您有来自[quickstart:compose和rails示例](https://docs.docker.com/compose/rails/)的`docker-compose.yml`文件。

```yml
version: '2'
services:
  db:
    image: postgres
  web:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

如果在定义服务的`docker-compose.yml`文件所在的目录中运行`docker-compose pull servicename`，docker将提取相关联的映像。例如，要调用在我们的示例中配置为`db`服务的`postgres`映像，您需要运行`docker-compose pull db`。

```shell
$ docker-compose pull db
Pulling db (postgres:latest)...
latest: Pulling from library/postgres
cd0a524342ef: Pull complete
9c784d04dcb0: Pull complete
d99dddf7e662: Pull complete
e5bff71e3ce6: Pull complete
cb3e0a865488: Pull complete
31295d654cd5: Pull complete
fc930a4e09f5: Pull complete
8650cce8ef01: Pull complete
61949acd8e52: Pull complete
527a203588c0: Pull complete
26dec14ac775: Pull complete
0efc0ed5a9e5: Pull complete
40cd26695b38: Pull complete
Digest: sha256:fd6c0e2a9d053bebb294bb13765b3e01be7817bf77b01d58c2377ff27a4a46dc
Status: Downloaded newer image for postgres:latest
```

## docker-compose push
```shell
Usage: push [options] [SERVICE...]

Options:
    --ignore-push-failures  Push what it can and ignores images with push failures.
```
将镜像推送到各自的服务 `registry/repository`
假设如下：
- 你在推一个你在本地建立的镜像
- 您可以访问生成密钥
### 实例
```yml
version: '3'
services:
  service1:
    build: .
    image: localhost:5000/yourimage  # goes to local registry

  service2:
    build: .
    image: youruser/yourimage  # goes to youruser DockerHub registry
```
## docker-compose restart
```shell
Usage: restart [options] [SERVICE...]

Options:
  -t, --timeout TIMEOUT      Specify a shutdown timeout in seconds.
                             (default: 10)
```
重新启动所有已停止并正在运行的服务。

如果您对`docker-compose.yml`配置进行了更改，这些更改在运行此命令后不会反映出来。

例如，对环境变量的更改（在构建容器之后，但在执行容器的命令之前添加）在重新启动后不会更新。

如果要配置服务的重新启动策略，请参阅Compose文件v3中的[重新启动](https://docs.docker.com/compose/compose-file/#restart)和Composev2中的[重新启动](https://docs.docker.com/compose/compose-file/compose-file-v2/#restart)。请注意，如果您在[swarm模式下部署一个堆栈](https://docs.docker.com/engine/reference/commandline/stack_deploy/)，那么应该使用[重启策略](https://docs.docker.com/compose/compose-file/#restart)。

## docker-compose rm
```shell
Usage: rm [options] [SERVICE...]

Options:
    -f, --force   Don't ask to confirm removal
    -s, --stop    Stop the containers, if required, before removing
    -v            Remove any anonymous volumes attached to containers
```

删除停止的服务容器。

默认情况下，不会删除附加到容器的匿名卷。您可以用-v覆盖这个。要列出所有卷，请使用`docker volume ls`。

任何不在卷中的数据都将丢失。

在没有选项的情况下运行命令也会删除`docker-compose up`或`docker-compose run`创建的一次性容器：

```shell
$ docker-compose rm
Going to remove djangoquickstart_web_run_1
Are you sure? [yN] y
Removing djangoquickstart_web_run_1 ... done
```

## docker-compose run
```shell
Usage:
    run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] [-l KEY=VALUE...]
        SERVICE [COMMAND] [ARGS...]

Options:
    -d, --detach          Detached mode: Run container in the background, print
                          new container name.
    --name NAME           Assign a name to the container
    --entrypoint CMD      Override the entrypoint of the image.
    -e KEY=VAL            Set an environment variable (can be used multiple times)
    -l, --label KEY=VAL   Add or override a label (can be used multiple times)
    -u, --user=""         Run as specified username or uid
    --no-deps             Don't start linked services.
    --rm                  Remove container after run. Ignored in detached mode.
    -p, --publish=[]      Publish a container's port(s) to the host
    --service-ports       Run command with the service's ports enabled and mapped
                          to the host.
    --use-aliases         Use the service's network aliases in the network(s) the
                          container connects to.
    -v, --volume=[]       Bind mount a volume (default [])
    -T                    Disable pseudo-tty allocation. By default `docker-compose run`
                          allocates a TTY.
    -w, --workdir=""      Working directory inside the container
```

对服务运行一次性命令。例如，下面的命令启动`Web`服务并运行`bash`作为其命令。

```shel
docker-compose run web bash
```

在新容器中与`run` start一起使用的命令，其配置由服务的配置定义，包括卷、链接和其他详细信息。然而，有两个重要的区别。

首先，由`run`传递的命令覆盖服务配置中定义的命令。例如，如果Web服务配置是用`bash`启动的，那么`docker-compose run web python app.py`将用`python app.py`覆盖它。

第二个区别是`docker-compose run`命令不创建服务配置中指定的任何端口。这可以防止端口与已打开的端口发生冲突。如果要创建服务的端口并将其映射到主机，请指定`--service ports`标志：

```shell
docker-compose run --service-ports web python manage.py shell
```

或者，可以使用`--publish`或`-p`选项指定手动端口映射，就像使用`docker run`时一样：

```shell
docker-compose run --publish 8080:80 -p 2022:22 -p 127.0.0.1:2021:21 web python manage.py shell
```

如果启动配置了链接的服务，`run`命令首先检查链接的服务是否正在运行，如果停止，则启动该服务。一旦所有链接的服务都在运行，`run`就会执行您传递给它的命令。例如，您可以运行：

```shell
docker-compose run db psql -h db -U docker
```
这将打开链接`db`容器的交互式PostgreSQL shell。


如果不希望运行命令启动链接的容器，请使用`--no deps`标志：

```shell
docker-compose run --no-deps web python manage.py shell
```

如果要在覆盖容器的重新启动策略时运行后删除容器，请使用`--rm`标志：

```shell
docker-compose run --rm web python manage.py db upgrade
```
这将运行数据库升级脚本，并在完成运行后删除容器，即使在服务配置中指定了重新启动策略。

## docker-compose scale
>注意：此命令已弃用。相反，使用带有--scale标志的up命令。注意，使用--scale标志与scale命令有一些细微的区别，因为它包含了up命令的行为。
```shell
Usage: scale [options] [SERVICE=NUM...]

Options:
  -t, --timeout TIMEOUT      Specify a shutdown timeout in seconds.
                             (default: 10)
```
设置要为服务运行的容器数。

数字以service=num的形式指定为参数。例如：

```shell
docker-compose scale web=2 worker=3
```

> 提示：或者，在[Compose文件3.x版](https://docs.docker.com/compose/compose-file/)中，您可以在部署密钥下指定副本，作为Swarm模式服务配置的一部分。deploy键及其子选项（包括`replicas`）只与`docker stack deploy`命令一起使用，而不是`docker0-compose up`或`docker-compose run`。
## docker-compose start
```shell
Usage: start [SERVICE...]
```
启动服务的现有容器。
## docker-compose stop
```shell
Usage: stop [options] [SERVICE...]

Options:
  -t, --timeout TIMEOUT      Specify a shutdown timeout in seconds.
                             (default: 10)
```

停止运行容器而不移除它们。它们可以用`docker-compose start`重新启动。

## docker-compose top
```shell
Usage: top [SERVICE...]

```
显示正在运行的进程。
```shell
$ docker-compose top
compose_service_a_1
PID    USER   TIME   COMMAND
----------------------------
4060   root   0:00   top

compose_service_b_1
PID    USER   TIME   COMMAND
----------------------------
4115   root   0:00   top
```
## docker-compose unpause
```shell
Usage: unpause [SERVICE...]
```

取消暂停服务的暂停容器。

## docker-compose up
```shell
Usage: up [options] [--scale SERVICE=NUM...] [SERVICE...]

Options:
    -d, --detach               Detached mode: Run containers in the background,
                               print new container names. Incompatible with
                               --abort-on-container-exit.
    --no-color                 Produce monochrome output.
    --quiet-pull               Pull without printing progress information
    --no-deps                  Don't start linked services.
    --force-recreate           Recreate containers even if their configuration
                               and image haven't changed.
    --always-recreate-deps     Recreate dependent containers.
                               Incompatible with --no-recreate.
    --no-recreate              If containers already exist, don't recreate
                               them. Incompatible with --force-recreate and -V.
    --no-build                 Don't build an image, even if it's missing.
    --no-start                 Don't start the services after creating them.
    --build                    Build images before starting containers.
    --abort-on-container-exit  Stops all containers if any container was
                               stopped. Incompatible with -d.
    -t, --timeout TIMEOUT      Use this timeout in seconds for container
                               shutdown when attached or when containers are
                               already running. (default: 10)
    -V, --renew-anon-volumes   Recreate anonymous volumes instead of retrieving
                               data from the previous containers.
    --remove-orphans           Remove containers for services not defined
                               in the Compose file.
    --exit-code-from SERVICE   Return the exit code of the selected service
                               container. Implies --abort-on-container-exit.
    --scale SERVICE=NUM        Scale SERVICE to NUM instances. Overrides the
                               `scale` setting in the Compose file if present.
```

为服务构建（重新）创建、启动和附加到容器。

除非它们已经在运行，否则此命令也会启动任何链接的服务。

`docker-compose-up`命令聚合每个容器的输出（基本上运行`docker-compose logs-f`）。当命令退出时，所有容器都将停止。运行`docker-compose up -d`在后台启动容器并让它们继续运行。

如果存在服务的现有容器，并且在容器创建后更改了服务的配置或镜像，`docker-compose up`将通过停止并重新创建容器（保留已装入的卷）来获取更改。要防止compose接收更改，请使用`--no-recreate`标志。

如果要强制Compose停止并重新创建所有容器，请使用`--force`重新创建标志。

如果进程遇到错误，则此命令的退出代码为`1`。

如果使用`sigint（ctrl+c）`或`sigterm中`断进程，容器将停止，退出代码为`0`。

如果在这个关闭阶段再次发送`sigint`或`sigterm`，运行中的容器将被终止，退出代码为`2`。
