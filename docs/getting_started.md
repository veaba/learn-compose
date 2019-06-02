# 开始使用Docker Compose
*预计阅读时间：10分钟*
在这个页面上，您构建了一个运行在Docker Compose上的简单的python Web应用程序。应用程序使用flask框架并在redis中维护一个计数器。虽然示例使用了Python，但是这里演示的概念应该是可以理解的，即使您不熟悉它。

## 先决条件
确保您已经安装了[Docker Engine](https://docs.docker.com/install/)和[Docker Compose](https://docs.docker.com/compose/install/)。您不需要安装python或redis，因为这两者都由docker images提供。

## 步骤1：设置
定义应用程序依赖项。
1. 为项目创建目录：
```shell
$ mkdir composetest
$ cd composetest
```
2. 在项目目录中创建名为app.py的文件，并将其粘贴到：
```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)


def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```
在本例中，`redis`是应用程序网络上redis容器的主机名。我们使用redis的默认端口`6379`。

> **可能的错误** 
> 注意`get_hit_count`函数的编写方式。如果Redis服务不可用，这个基本的重试循环允许我们多次尝试请求。当应用程序联机时，这在启动时很有用，但如果在应用程序的生命周期内需要随时重新启动Redis服务，这也会使我们的应用程序更有弹性。在集群中，这也有助于处理节点之间的瞬时连接丢失。

3. 在项目目录中创建另一个名为`requirements.txt`的文件，并将其粘贴到：

```shell
flask
redis
```
## 步骤2：创建一个Dockerfile

在这一步中，您将编写一个构建docker映像的dockerfile。镜像包含了python应用程序需要的所有依赖项，包括python本身。

在项目目录中，创建名为`dockerfile`的文件并粘贴以下内容：

```shell
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP app.py
ENV FLASK_RUN_HOST 0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["flask", "run"]
```

这告诉Docker：
- 从python 3.7映像开始构建映像
- 将工作目录设置为`/code` 
- 设置`flask`命令使用的环境变量
- 安装gcc，让诸如markupsafe和sqlacalchemy之类的python包可以编译加速
- 复制`requirements.txt`并安装python依赖项
- 复制当前目录`.`在项目中到WorkDir `.`在镜像中
- 将容器的默认命令设置为`flask run`

有关如何编写dockerfile的详细信息，请参阅 [docker用户指南](https://docs.docker.com/engine/tutorials/dockerimages/#building-an-image-from-a-dockerfile) 和 [dockerfile参考](https://docs.docker.com/engine/reference/builder/)。




## 步骤3：在Compose文件中定义服务
在项目目录中创建一个名为docker-compose.yml的文件，并粘贴以下内容：

```yml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

这个Compose文件定义了两个服务：`Web`和`Redis`。

### Web 服务

`Web`服务使用从当前目录中的`Dockerfile`构建的映像。然后它将容器和主机绑定到暴露的端口`5000`,此示例服务使用FlaskWeb服务器`5000`的默认端口。

### Redis 服务

`Redis`服务使用从Docker Hub注册表中提取的公共[Redis](https://registry.hub.docker.com/_/redis/)映像。

## 步骤4：使用Compose功能构建和运行应用程序
1. 从项目目录中，通过运行`docker-compose up`启动应用程序。
```shell
$ docker-compose up
Creating network "composetest_default" with the default driver
Creating composetest_web_1 ...
Creating composetest_redis_1 ...
Creating composetest_web_1
Creating composetest_redis_1 ... done
Attaching to composetest_web_1, composetest_redis_1
web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
redis_1  | 1:C 17 Aug 22:11:10.480 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 17 Aug 22:11:10.480 # Redis version=4.0.1, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 17 Aug 22:11:10.480 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
web_1    |  * Restarting with stat
redis_1  | 1:M 17 Aug 22:11:10.483 * Running mode=standalone, port=6379.
redis_1  | 1:M 17 Aug 22:11:10.483 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
web_1    |  * Debugger is active!
redis_1  | 1:M 17 Aug 22:11:10.483 # Server initialized
redis_1  | 1:M 17 Aug 22:11:10.483 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
web_1    |  * Debugger PIN: 330-787-903
redis_1  | 1:M 17 Aug 22:11:10.483 * Ready to accept connections
```
compose提取一个redis映像，为代码构建一个映像，并启动您定义的服务。在这种情况下，代码在构建时静态复制到映像中。

2. 在浏览器中输入http://localhost:5000/，查看应用程序的运行情况。
如果您在Linux上本机使用docker，在Mac上使用docker desktop，或者在Windows上使用docker desktop，那么web应用程序现在应该在docker守护进程主机上监听端口5000。将您的Web浏览器指向 http://localhost:5000 以查找`Hello World`消息。如果无法解决此问题，您还可以尝试 http://127.0.0.1:5000 。

如果您在Mac或Windows上使用docker machine，请使用`docker machine ip MACHINE_VM`获取docker主机的IP地址。然后，在浏览器中打开`http://MACHINE_VM_IP:5000`。

您应该在浏览器中看到一条消息，说：

```shell
Hello World! I have been seen 1 times.
```
![quick-hello-world-1.png](/images/quick-hello-world-1.png)

3. 刷新页面。

数字应该递增。
```shell
Hello World! I have been seen 2 times.
```

![quick-hello-world-2.png](/images/quick-hello-world-2.png)

4. 切换到另一个终端窗口，键入`docker image ls`以列出本地镜像。

此时列出的图片应该返回`redis`和`web`。

```shell
$ docker image ls
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
composetest_web         latest              e2c21aa48cc1        4 minutes ago       93.8MB
python                  3.4-alpine          84e6077c7ab6        7 days ago          82.5MB
redis                   alpine              9d8fa9aa0e5b        3 weeks ago         27.5MB
```

您可以使用`docker inspect<tag or id>`检查镜像。

5. 停止应用程序，要么从第二个终端的项目目录中运行`docker compose down`，要么在启动应用程序的原始终端中按`ctrl+c`。

## 步骤5：编辑Compose文件以添加绑定装载
在项目目录中编辑`docker-compose.yml`以添加`Web`服务的绑定装载：

```yml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```
new `volumes` 键将主机上的项目目录（当前目录）装载到容器中的`/code`，允许您动态修改代码，而无需重新生成映像。`environment`键设置`FLASK_ENV`环境变量，该变量指示`flask run`以开发模式运行，并在更改时重新加载代码。这种模式应该只在开发中使用。


## 步骤6：重新构建并运行带有Compose功能的应用程序

在项目目录中，键入`docker-compose up`以使用更新的compose文件构建应用程序，然后运行它。

```shell
$ docker-compose up
Creating network "composetest_default" with the default driver
Creating composetest_web_1 ...
Creating composetest_redis_1 ...
Creating composetest_web_1
Creating composetest_redis_1 ... done
Attaching to composetest_web_1, composetest_redis_1
web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
...
```

再次在Web浏览器中检查`hello world`消息，并刷新以查看计数增量。

> **共享文件夹、卷和绑定装载**
> - 如果您的项目`在用户`目录（`cd ~`）之外，那么您需要共享您正在使用的dockerfile和卷的驱动器或位置。如果出现运行时错误，表明找不到应用程序文件、卷装入被拒绝或服务无法启动，请尝试启用文件或驱动器共享。对于不在`C:\Users`（Windows）或`/Users`（Mac）范围内的项目，卷装入需要共享驱动器；对于使用[Linux容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers-beta-feature)的Windows，Docker桌面上的任何项目都需要[共享驱动器](https://docs.docker.com/docker-for-windows/#shared-drives)。有关更多信息，请参阅Windows Docker桌面上的共享驱动器、Mac Docker上的[文件共享](https://docs.docker.com/docker-for-mac/#file-sharing)，以及有关如[何管理容器中数据](https://docs.docker.com/engine/tutorials/dockervolumes/)的一般示例。
> - 如果您在旧的Windows操作系统上使用Oracle VirtualBox，则可能会遇到与此[VB故障](https://www.virtualbox.org/ticket/14920)通知单中描述的共享文件夹有关的问题。较新的Windows系统满足[Windows Docker桌面](https://docs.docker.com/docker-for-windows/install/)的要求，不需要virtualbox。



## 步骤7：更新应用程序

由于应用程序代码现在使用卷装入容器中，因此您可以对其代码进行更改并立即看到更改，而无需重新生成镜像。

1. 在`app.py`中更改问候语并保存。例如，改变`Hello world！`从Docker到留言`Hello from Docker!`：

```shell
return 'Hello from Docker! I have been seen {} times.\n'.format(count)
```
2. 在浏览器中刷新应用程序。问候语应更新，计数器仍应递增。

![quick-hello-world-3.png](/images/quick-hello-world-3.png)

## 步骤8：使用其他命令进行实验

如果要在后台运行服务，可以将`-d`标志（对于“分离”模式）传递给`docker-compose up`，并使用`docker-compose ps`查看当前正在运行的内容：

```shell
$ docker-compose up -d
Starting composetest_redis_1...
Starting composetest_web_1...

$ docker-compose ps
Name                 Command            State       Ports
-------------------------------------------------------------------
composetest_redis_1   /usr/local/bin/run         Up
composetest_web_1     /bin/sh -c python app.py   Up      5000->5000/tcp
```

`docker-compose run`命令允许您为服务运行一次性命令。例如，要查看`web`服务可用的环境变量：

```shell
$ docker-compose run web env
```

参见 `docker compose ——help` 查看其他可用命令。您还可以为bash和zsh shell[安装命令完成](https://docs.docker.com/compose/completion/)，这也显示了可用的命令。

如果您使用`docker-compose up-d`开始Compose，请在完成服务后停止它们：

```shell
$ docker-compose stop
```

您可以使用`down`命令将所有内容放下，完全移除容器。pass `--volumes` ，用于同时删除redis容器使用的数据卷：

```shell
$ docker-compose down --volumes
```

在这一点上，您已经了解了Compose工作的基本原理。


## 下一步：
- 接下来，尝试 [Django](https://docs.docker.com/compose/django/)、[Rails](https://docs.docker.com/compose/rails/) 或[WordPress](https://docs.docker.com/samples/library/wordpress/)的快速入门指南。
- [浏览Compose命令的完整列表](https://docs.docker.com/compose/reference/)
- [Compose配置文件引用](https://docs.docker.com/compose/compose-file/)
- 要了解有关卷和绑定装载的更多信息，请参阅在[Docker中管理数据](https://docs.docker.com/engine/admin/volumes/)