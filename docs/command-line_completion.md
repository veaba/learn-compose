# 命令行完成
*预计阅读时间：3分钟*

compose附带bash和zsh shell的[命令完成](http://en.wikipedia.org/wiki/Command-line_completion)。

## 安装命令完成

### Bash
确保安装了bash完成。

#### LINUX

1. 在一个当前 Linux OS（并非最小安装中），应该可以完成bash。
2. 将完成脚本放在 `/etc/bash_completion.d`。

```shell
 sudo curl -L https://raw.githubusercontent.com/docker/compose/1.24.0/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

### MAC

#### 通过自制安装
1. 通过`rew install bash-completion`安装
2. 安装后，BREW显示安装路径。确保将完成脚本放在路径中。
例如，在Mac 10.13.2上运行此命令时，请将完成脚本放入`/usr/local/etc/bash_completion.d/`
```shell
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.24.0/contrib/completion/bash/docker-compose -o /usr/local/etc/bash_completion.d/docker-compose
```
3. 将以下内容添加到 `~/.bash_profile`:
```shell
 if [ -f $(brew --prefix)/etc/bash_completion ]; then
 . $(brew --prefix)/etc/bash_completion
 fi
```

4. 你可以找到你的`~/.bash_profile`或者启动一个新的终端来利用完成。

#### 通过MacPorts安装
1. 运行 `sudo port install bash-completion` 安装bash完成。
2. 将下列行添加到`~/.bash_profile`:

```shell
 if [ -f /opt/local/etc/profile.d/bash_completion.sh ]; then
 . /opt/local/etc/profile.d/bash_completion.sh
 fi
```
3. 你可以找到你的 `~/.bash_profile`或者启动一个新的终端来利用完成。

### Zsh
确保您已在计算机上安装了`oh-my-zsh`。
#### WITH OH-MY-ZSH SHELL
将`docker`和`docker-compose`添加到`~/.zshrc`中的插件列表中，以便在oh my zsh shell中运行自动完成。在下面的示例中，`…`表示您可能安装的其他zsh插件。

```shell
plugins=(... docker docker-compose
)
```
#### WITHOUT OH-MY-ZSH SHELL
1. 将完成脚本放入`/path/to/zsh/completion` (一般地 `~/.zsh/completion/`)

```shell
 $ mkdir -p ~/.zsh/completion
 $ curl -L https://raw.githubusercontent.com/docker/compose/1.24.0/contrib/completion/zsh/_docker-compose > ~/.zsh/completion/_docker-compose
```

2. 通过添加`~/.zshrc`，在`$fpath`中包含目录：

```shell
 fpath=(~/.zsh/completion $fpath)
```

3. 确保已加载`compinit`，或者通过添加`~/.zshrc`来执行此操作：
```shell
 autoload -Uz compinit && compinit -i
```
4. 然后重新加载外壳：
```shell
 exec $SHELL -l
```
## 可用完成数
根据到目前为止在命令行上键入的内容，它将完成：
- 可用的`Docker-compose`命令
- 假设一个特别命令可以使用
- 在给定上下文中有意义的服务名称，例如具有正在运行或停止的实例的服务，或基于镜像的服务，而基于Dockerfiles的服务。对于`docker-compose scale`，已完成的服务名称自动附加“=”号。
- 所选选项的参数。例如，`docker-compose kill-s`完成了一些信号，比如`siguup`和`sigusr1`。

享受更快的作曲速度和更少的打字错误！

## Compose文档
- [用户指南](https://docs.docker.com/compose/)
- [入门](https://docs.docker.com/compose/gettingstarted/)
- [开始使用Django](https://docs.docker.com/compose/django/)
- [开始使用Rails](https://docs.docker.com/compose/rails/)
- [开始使用WordPress](https://docs.docker.com/compose/wordpress/)
- [命令行参考](https://docs.docker.com/compose/reference/)
- [Compose文件引用](https://docs.docker.com/compose/compose-file/)
