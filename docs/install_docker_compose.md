## 安装Docker Compose
预计阅读时间：7分钟

您可以在MacOS、Windows和64位Linux上运行compose。

## 先决条件
docker compose依赖docker引擎完成任何有意义的工作，因此请确保您在本地或远程安装了docker引擎，具体取决于您的设置。
- 在桌面系统（如Mac和Windows的Docker桌面）上，Docker Compose是桌面安装的一部分。
- 在Linux系统上，首先按照[docker](https://docs.docker.com/install/#server)页面上的描述为您的操作系统安装docker，然后返回此处获取有关在Linux系统上安装compose的说明。
- 要以非根用户身份运行compose，请参见将[docker管理为非根用户](https://docs.docker.com/install/linux/linux-postinstall/)。
## 安装Compose
按照下面的说明在Mac、Windows、Windows Server 2016或Linux系统上安装compose，或者查找其他选项，如使用pip python包管理器或将compose安装为容器。

*安装其他版本*
>下面的说明概述了Compose当前稳定版本（v1.24.0）的安装。要安装不同版本的compose，请将给定的版本号替换为所需的版本号。Compose版本也列在GitHub的Compose库发布页面上，可直接下载。要安装Compose的预发行版，请参阅“安装预发行版本”部分。

### 在MacOS 安装Compose

Mac的Docker桌面和Docker工具箱已经包括Compose以及其他Docker应用程序，因此Mac用户不需要单独安装Compose。Docker安装说明如下：

- [获取Mac的Docker桌面(https://docs.docker.com/docker-for-mac/install/)]
- [获取Docker工具箱](https://docs.docker.com/toolbox/overview/)（用于旧系统）

### 在Windows 桌面系统 安装 Compose

`docker desktop for windows`和`docker toolbox`已经包括compose和其他docker应用程序，因此大多数Windows
用户不需要单独安装compose。Docker安装说明如下：

- [获取Windows的Docker桌面](https://docs.docker.com/docker-for-windows/install/)
- [获取 Docker工具箱](https://docs.docker.com/toolbox/overview/)（用于旧系统）

如果直接在Microsoft Windows Server上运行Docker守护进程和客户端，请按照Windows Server选项卡中的说明进行操作。
### 在Windows Service 安装 Compose

如果您使用 [docker engine-enterprise](https://docs.docker.com/install/windows/docker-ee/) 在Microsoft Windows Server上直接运行docker守护进程和客户端，并希望安装docker compose，请按照以下说明操作。

1. 启动“提升的”PowerShell（以管理员身份运行）。搜索PowerShell，右键单击，然后选择以管理员身份运行。当被问及是否允许此应用对您的设备进行更改时，请单击“是”。

2. 在PowerShell中，由于GitHub现在需要TLS1.2，请运行以下命令：
```shell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

然后运行以下命令下载当前稳定版本的compose（v1.24.0）
```shell
Invoke-WebRequest "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-Windows-x86_64.exe" -UseBasicParsing -OutFile $Env:ProgramFiles\Docker\docker-compose.exe
```

注意：在Windows Server 2019上，可以将compose可执行文件添加到$env:programfiles\docker。因为这个目录是在系统路径中注册的，所以您可以在随后的步骤中运行docker compose--version命令，而不需要额外的配置。

```shell
> To install a different version of Compose, substitute `1.24.0`
> with the version of Compose you want to use.
```
1. 测试安装
```shell
docker-compose --version

docker-compose version 1.24.0, build 01110ad01
```

### 在Linux 安装 Compose
在Linux上，您可以从Github上的 [compose repository release](https://github.com/docker/compose/releases) 页面下载docker compose二进制文件。按照链接中的说明进行操作，这涉及到在终端中运行`curl`命令来下载二进制文件。这些分步说明也包括在下面。

1. 运行此命令下载`Docker Compose`的当前稳定版本：

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

> 要安装不同版本的Compose，请将1.24.0替换为要使用的Compose版本。

如果使用`curl`安装时遇到问题，请参阅上面的 [“备选安装选项”](https://docs.docker.com/compose/install/#alternative-install-options) 选项卡。

2. 对二进制文件应用可执行权限：

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

>**注意**：如果安装后`docker-compose`命令失败，请检查路径。还可以创建到/usr/bin或路径中任何其他目录的符号链接。

比如：
```shell
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

1. 任选为`bash`和`zsh` shell安装[命令完成](https://docs.docker.com/compose/completion/)。
2. 测试安装

```shell
$ docker-compose --version
docker-compose version 1.24.0, build 1110ad01
```
### 备选安装选项
- [使用PIP安装](https://docs.docker.com/compose/install/#install-using-pip)
- [作为容器安装](https://docs.docker.com/compose/install/#install-as-a-container)

#### 使用PIP安装

> 对于`Alpine`，需要以下依赖包：`py-pip`、`python-dev`、`libffi-dev`、`openssl-dev`、`gcc`、`libc-dev`和`make`。

可以使用`pip`从[pypi](https://pypi.python.org/pypi/docker-compose)安装compose。如果使用`pip`安装，我们建议您使用[virtualenv](https://virtualenv.pypa.io/en/latest/)，因为许多操作系统都有与docker-compose依赖项冲突的python系统包。请参阅[virtualenv教程](http://docs.python-guide.org/en/latest/dev/virtualenvs/)开始。

```shell
pip install docker-compose
```

如果您不使用virtualenv，

```shell
sudo pip install docker-compose
```

> 需要PIP版本6.0或更高版本。


#### 作为容器安装

Compose也可以从一个小型的bash脚本包装器在容器中运行。要将compose安装为容器，请运行以下命令：

```shell
$ sudo curl -L --fail https://github.com/docker/compose/releases/download/1.24.0/run.sh -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

### 安装预发布版本

如果您有兴趣尝试一个预发布版本，您可以从[GitHub上的编写存储库发布页面](https://github.com/docker/compose/releases)下载候选版本。按照链接中的说明进行操作，这涉及到在终端中运行`curl`命令来下载二进制文件。

从`“master”`分支构建的预发布版本也可从https://dl.bintray.com/docker compose/master/下载。

> 预发布版本允许您在发布新功能之前试用它们，但可能不太稳定。


## 升级改造

如果要从compose 1.2或更早版本升级，请在升级compose之后删除或迁移现有容器。这是因为，从1.3版开始，Compose使用Docker标签跟踪容器，需要重新创建容器以添加标签。

如果compose检测到创建的容器没有标签，它将拒绝运行，这样您就不会得到两组标签。如果要继续使用现有容器（例如，因为它们具有要保留的数据卷），可以使用compose 1.5.x使用以下命令迁移它们：

```shell
docker-compose migrate-to-labels
```
或者，如果您不担心保留它们，可以删除它们。Compose只会创建新的。

```shell
docker container rm -f -v myapp_web_1 myapp_db_1 ...
```

## 卸载

要在使用`curl`安装时卸载`Docker Compose`，请执行以下操作：

```shell
sudo rm /usr/local/bin/docker-compose
```
要在使用`pip`安装时卸载Docker Compose，请执行以下操作：

```shell
pip uninstall docker-compose
```

> **有“权限被拒绝”错误吗？** 如果使用上述任一方法时出现`“权限被拒绝”`错误，则可能没有删除`Docker-Compose`的适当权限。要强制删除，请将`sudo`预先发送到上述任一命令，然后再次运行。

## 下一步

- [用户指南](https://docs.docker.com/compose/)
- [入门](https://docs.docker.com/compose/gettingstarted/)
- [开始使用Django](https://docs.docker.com/compose/django/)
- [开始使用Rails](https://docs.docker.com/compose/rails/)
- [开始使用WordPress](https://docs.docker.com/compose/wordpress/)
- [命令行参考](https://docs.docker.com/compose/reference/)
- [Compose文件引用](https://docs.docker.com/compose/compose-file/)
