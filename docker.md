[TOC]
## 1.Docker 安装
> Docker 社区版各个环境的安装参考官方文档：https://docs.docker.com/install/

<br>

## 2.Docker 命令
通过 --help 参数可以看到 docker 提供了哪些命令，可以看到 docker 的用法是 docker [选项] 命令 。
命令有两种形式，Management Commands 是子命令形式，每个命令下还有子命令；Commands 是直接命令，相当于子命令的简化形式。

<br>

## 3.Docker 镜像管理
#### 1.镜像简介
镜像包含了一个软件的运行环境，是一个不包含Linux内核而又精简的Linux操作系统，一个镜像可以创建N个容器。
镜像是一个分层存储的架构，由多层文件系统联合组成。镜像构建时，会一层层构建，前一层是后一层的基础。
从下载过程中可以看到，镜像是由多层存储所构成。下载也是一层层的去下载，并非单一文件。下载过程中给出了每一层的 ID 的前 12 位。并且下载结束后，给出该镜像完整的 sha256 的摘要，以确保下载一致性。


```
通过 docker history <ID/NAME> 查看镜像中各层内容及大小，每层会对应着 Dockerfile 中的一条指令。
```

#### 2.镜像管理
1. **搜索镜像** 
```
docker search <NAME> [选项]
```

2. **下载镜像**

```
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```
> 仓库名：仓库名是两段式名称，既 <用户名>/<软件名> 。对于 Docker Hub，如果不给出用户名，则默认为 library ，也就是官方镜像。

3. **列出本地镜像**

```
docker images [选项]
```
4. **给镜像打 Tag**

```
docker tag <IMAGE ID> [<用户名>/]<镜像名>:<标签>
```

5. **删除本地镜像**

```
docker rmi [选项] <镜像1> [<镜像2> ...]
```
> 删除所有虚悬镜像：docker rmi $(docker images -q -f dangling=true)

> 删除所有仓库名为  redis  的镜像：docker rmi $(docker images -q redis)

> 删除所有在  mysql:8.0  之前的镜像：docker rmi $(docker images -q -f before=mysql:8.0)

6. **导出/入镜像**

```
docker save -o <镜像文件> <镜像>
docker load -i <镜像文件>
```

<br>

## 4.Docker 容器管理
#### 1.创建容器
启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（stopped）的容器重新启动。Docker 容器非常轻量级，很多时候可以随时删除和新创建容器。创建容器的主要命令为 **docker run（或docker container run）**

1. **创建并进入容器**

```
docker run -ti <IMAGE ID OR NAME>  /bin/bash
```
> 创建容器，通过 -ti 参数分配一个 bash 终端，并进入容器内执行命令。退出容器时容器就被终止了。

2. **容器后台运行**

```
docker run -d <IMAGE ID OR NAME>
```
3. **进入容器**

```
docker exec -ti <CONTAINER ID> bash
```
4. **指定端口**

```
docker run -d -p <宿主机端口>:<容器内端口> -name <name> <IMAGE ID>
```
5. **宿主机重启时自动重启容器**

```
docker run -d --restart always <IMAGE ID>
```
#### 2.容器资源限制
> 容器资源限制主要是对内存的限制和使用CPU数量的限制

1. [ 参考链接](https://www.cnblogs.com/chiangchou/p/docker.html#_label3_1)
2. **查看容器资源使用统计：** ```docker stats <CONTAINER ID>```

#### 3.容器常用命令
1. **停止容器：**
```
docker stop <CONTAINER ID>
```

2. **重启容器：**
```
docker start <CONTAINER ID>
```
3. **删除已停止的容器：** 
```
docker rm <CONTAINER ID>
```

4. **强制删除运行中的容器：**
```
docker rm -f <CONTAINER ID>
```

5. **批量删除Exited(0)状态的容器：**
```
docker rm $(docker ps -aq -f exited=0)
```

6. **批量删除所有容器：**
```
docker rm -f $(docker ps -aq)
```

7. **查看容器日志：**
```
docker logs <CONTAINER ID OR NAMES>
```
8. **查看容器详细信息：**
```
docker inspect <CONTAINER ID>
```

9. **列出或指定容器端口映射：**
```
docker port <CONTAINER ID>
```
10. **显示一个容器运行的进程：**
```
docker top <CONTAINER ID>
```

11. **拷贝文件/文件夹到容器：**
```
docker cp <dir> <CONTAINER ID>:<dir>
```

12. **查看容器与镜像的差异：**
```
docker diff <CONTAINER ID>
```

<br>


## 5.镜像制作

#### 1.Dockerfile
> Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。Dockerfile 中每一个指令都会建立一层，在其上执行后面的命令，执行结束后， commit 这一层的修改，构成新的镜像。对于有些指令，尽量将多个指令合并成一个指令，否则容易产生非常臃肿、非常多层的镜像，不仅仅增加了构建部署的时间，也很容易出错。而且Union FS 是有最大层数限制的，比如 AUFS不得超过 127 层。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

1. **FROM**
> 一个 Dockerfile 中 FROM 是必备的指令，并且必须是第一条指令。

> 格式：FROM <镜像名称>[:<TAG>]

> Docker 还存在一个特殊的镜像，名为 scratch 。这个镜像并不实际存在，它表示一个空白的镜像。如果以 scratch 为基础镜像的话，意味着不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

2. **RUN**
> RUN 指令是用来执行命令行命令的。由于命令行的强大能力， RUN 指令在定制镜像时是最常用的指令之一。

> 多个 RUN 指令尽量合并成一个，使用 && 将各个所需命令串联起来。Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式。

```
shell 格式： RUN <命令> ，就像直接在命令行中输入的命令一样。
exec 格式： RUN ["可执行文件", "参数1", "参数2"]
```

3. **COPY**
> COPY 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。<源路径> 可以是多个，甚至可以是通配符。<目标路径> 可以是容器内的绝对路径，也可以是相对于工作目录的相对路径。

> 使用 COPY 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。

```
COPY <源路径>,...  <目标路径>
COPY ["<源路径1>",... "<目标路径>"]
```

4. **ENV**
> 设置环境变量，无论是后面的其它指令，如 RUN ，还是运行时的应用，都可以直接使用这里定义的环境变量。


```
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
```

5. **USER**
> USER 用于切换到指定用户，这个用户必须是事先建立好的，否则无法切换。


```
USER <用户名>
```

6. **EXPOSE**
> EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。

```
EXPOSE <端口1> [<端口2>...]
```
7. **WORKDIR**
> 使用 WORKDIR 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，该目录需要已经存在， WORKDIR 并不会帮你建立目录。

> 在Dockerfile 中，两行 RUN 命令的执行环境是不同的，是两个完全不同的容器。每一个 RUN 都是启动一个容器、执行命令、然后提交存储层文件变更。因此如果需要改变以后各层的工作目录的位置，那么应该使用  WORKDIR  指令。


```
WORKDIR <工作目录路径>
```

8. **ENTRYPOINT**
> ENTRYPOINT 的目的和 CMD 一样，都是在指定容器启动程序及参数。 ENTRYPOINT 在运行时也可以替代，不过比 CMD 要略显繁琐，需要通过 docker run 的参数 --entrypoint 来指定。


```
shell 格式： ENTRYPOINT <命令>
exec 格式： ENTRYPOINT ["可执行文件", "参数1", "参数2"...]
```

#### 2.构建镜像

```
docker build -t <镜像名称>:<TAG> [-f <Dockerfile 路径>] <上下文目录>
```
 > 上下文目录：镜像构建的上下文。Docker 是 C/S 结构，docker 命令是客户端工具，一切命令都是使用的远程调用形式在服务端(Docker 引擎)完成。当构建的时候，用户指定构建镜像上下文的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。这样在 COPY 文件的时候，实际上是复制上下文路径下的文件。一般来说，应该将 Dockerfile 以及所需文件置于一个空目录下，并指定这个空目录为上下文目录。

> Dockerfile 路径：可选，缺省时会寻找当前目录下的 Dockerfile 文件，如果是其它名称的 Dockerfile，可以使用 -f 参数指定文件路径。


