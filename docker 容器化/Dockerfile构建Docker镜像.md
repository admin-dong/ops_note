# 一、前言

Dockerfile是由Docker运行的脚本，用于生成image。其原理类似于shell脚本，Dockefile内容是由一条一条命令组成，每一条命令都和Linux的某一个命令对应。Dockerfile可以定制自己额外的需求，在Dockerfile中添加或修改指令，重新生成image。查看Dockerfile还可以明确的知道image是如何产生的

# 二、Dockerfile书写规则

Dockerfile的指令是忽略大小写的，建议使用大写，使用#作为注释，每一行只支持一条指令，每条指令可以携带多个参数。

Dockerfile指令可以分为两种，构建指令和设置指令。

构建指令用于构建image，其指定的操作不会在运行的image的容器上执行；

设置指令用户设置image的属性，其指定的操作将在运行image的容器中执行。

## (1). FROM(指定基础image)

构建指令，该指令是必须填写且在第一行的。后续的指令都依赖于该指定的iamge，FROM指令指定的基础image可以是官方远程仓库中的，也可以位于本地仓库。

指令格式:

```
#指定基础image为该image的最后修改版本
FROM  <image>
#或者：
#指定基础image为该image的一个tag版本
FROM <image>:<tag>
```

## (2). MAINTAINER（用来指定镜像创建者信息）

构建指令，用于将image的制作者相关的信息写入到image中。当我们对该image执行docker inspect命令时，输出中有相应的字段记录该信息。

指令格式：

```
MAINTAINER <name>
```

## **(3). RUN（安装软件用）**

构建指令，RUN可以运行任何被基础image支持的命令。如基础image选择了ubuntu，那么软件管理部分只能使用ubuntu的命令。可以使用该指执行Linux命令

- RUN命令将在当前image中执行任意合法命令并提交执行结果。命令执行提交后，就会自动执行Dockerfile中的下一个指令。
- 层级 RUN 指令和生成提交是符合Docker核心理念的做法。它允许像版本控制那样，在任意一个点，对image 镜像进行定制化构建。
- RUN 指令缓存不会在下个命令执行时自动失效。比如 RUN apt-get dist-upgrade -y 的缓存就可能被用于下一个指令. --no-cache 标志可以被用于强制取消缓存使用。

指令格式：

```
RUN <command> (the command is run in a shell - /bin/sh -c)
RUN ["executable", "param1", "param2" ... ]  (exec form)
```

## **(4). CMD（设置container启动时执行的操作）**

设置指令，用于container启动时指定的操作。该操作可以是执行自定义脚本，也可以是执行系统命令。该指令只能在文件中存在一次，如果有多个，则只执行最后一条。

该指令有三种格式：

```
CMD ["executable","param1","param2"] (like an exec, this is the preferred form)
CMD command param1 param2 (as a shell)
```

当Dockerfile指定了ENTRYPOINT，那么使用下面的格式：

```
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
```

ENTRYPOINT指定的是一个可执行的脚本或者程序的路径，该指定的脚本或者程序将会以param1和param2作为参数执行。所以如果CMD指令使用上面的形式，那么Dockerfile中必须要有配套的ENTRYPOINT。

## **(5). ENTRYPOINT（设置container启动时执行的操作）**

设置指令，指定容器启动时执行的命令，可以多次设置，但是只有最后一个有效。 

两种格式:

```
ENTRYPOINT ["executable", "param1", "param2"] (like an exec, the preferred form)
ENTRYPOINT command param1 param2 (as a shell)
```

该指令的使用分为两种情况，一种是独自使用，另一种和CMD指令配合使用。

当独自使用时，如果你还使用了CMD命令且CMD是一个完整的可执行的命令，那么CMD指令和ENTRYPOINT会互相覆盖只有最后一个CMD或者ENTRYPOINT有效。

```
# CMD指令将不会被执行，只有ENTRYPOINT指令被执行  
CMD echo “Hello, World!”  
ENTRYPOINT ls -l
```

另一种用法和CMD指令配合使用来指定ENTRYPOINT的默认参数，这时CMD指令不是一个完整的可执行命令，仅仅是参数部分；ENTRYPOINT指令只能使用JSON方式指定执行命令，而不能指定参数。

```
FROM ubuntu  
CMD ["-l"]  
ENTRYPOINT ["/usr/bin/ls"]
```

## **(6). USER（设置container容器的用户）**

设置指令，设置启动容器的用户，默认是root用户。

```
# 指定memcached的运行用户  
ENTRYPOINT ["memcached"]  
USER daemon  
或  
ENTRYPOINT ["memcached", "-u", "daemon"]
```

## **(7). EXPOSE（指定容器需要映射到宿主机器的端口）**

设置指令，该指令会将容器中的端口映射成宿主机器中的某个端口。当你需要访问容器的时候，可以不是用容器的IP地址而是使用宿主机器的IP地址和映射后的端口。要完成整个操作需要两个步骤，首先在Dockerfile使用EXPOSE设置需要映射的容器端口，然后在运行容器的时候指定-p选项加上EXPOSE设置的端口，这样EXPOSE设置的端口号会被随机映射成宿主机器中的一个端口号。也可以指定需要映射到宿主机器的那个端口，这时要确保宿主机器上的端口号没有被使用。EXPOSE指令可以一次设置多个端口号，相应的运行容器的时候，可以配套的多次使用-p选项。

指令格式：

```
EXPOSE <port> [<port>...]
```

```
# 映射一个端口  
EXPOSE port1  
# 相应的运行容器使用的命令  
docker run -p port1 image  
# 映射多个端口  
EXPOSE port1 port2 port3  
# 相应的运行容器使用的命令  
docker run -p port1 -p port2 -p port3 image  
# 还可以指定需要映射到宿主机器上的某个端口号  
docker run -p host_port1:port1 -p host_port2:port2 -p host_port3:port3 image
```

端口映射是docker比较重要的一个功能，原因在于我们每次运行容器的时候容器的IP地址不能指定而是在桥接网卡的地址范围内随机生成的。宿主机器的IP地址是固定的，我们可以将容器的端口的映射到宿主机器上的一个端口，免去每次访问容器中的某个服务时都要查看容器的IP的地址。对于一个运行的容器，可以使用docker port加上容器中需要映射的端口和容器的ID来查看该端口号在宿主机器上的映射端口。

## **(8). ENV（用于设置环境变量）**

- ENV指令可以用于为docker容器设置环境变量
- ENV设置的环境变量，可以使用docker inspect命令来查看。同时还可以使用docker run --env <key>=<value>来修改环境变量。

格式：

```
ENV <key> <value>
```

设置了后，后续的RUN命令都可以使用，container启动后，可以通过docker inspect查看这个环境变量，也可以通过在docker run --env key=value时设置或修改环境变量。

假如你安装了JAVA程序，需要设置JAVA_HOME，那么可以在Dockerfile中这样写：

```
ENV JAVA_HOME /path/to/java/dirent
```

## **(9). ADD（从src复制文件到container的dest路径）**

构建指令，所有拷贝到container中的文件和文件夹权限为0755，uid和gid为0；如果是一个目录，那么会将该目录下的所有文件添加到container中，不包括目录；如果文件是可识别的压缩格式，则docker会帮忙解压缩（注意压缩格式）；如果<src>是文件且<dest>中不使用斜杠结束，则会将<dest>视为文件，<src>的内容会写入<dest>；如果<src>是文件且<dest>中使用斜杠结束，则会<src>文件拷贝到<dest>目录下。

格式：

```
ADD <src> <dest>
```

- <src> 是相对被构建的源目录的相对路径，可以是文件或目录的路径，也可以是一个远程的文件url;
- <dest> 是container中的绝对路径

## **(10). VOLUME (指定挂载点)**

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

Volume设置指令，使容器中的一个目录具有持久化存储数据的功能，该目录可以被容器本身使用，也可以共享给其他容器使用。我们知道容器使用的是AUFS，这种文件系统不能持久化数据，当容器关闭后，所有的更改都会丢失。当容器中的应用有持久化数据的需求时可以在Dockerfile中使用该指令。

格式：

```
VOLUME ["<mountpoint>"]
```

例如：

```
FROM base  
VOLUME ["/tmp/data"]
```

运行通过该Dockerfile生成image的容器，/tmp/data目录中的数据在容器关闭后，里面的数据还存在。例如另一个容器也有持久化数据的需求，且想使用上面容器共享的/tmp/data目录，那么可以运行下面的命令启动一个容器：

```
docker run -t -i -rm -volumes-from container1 image2 bash
```

说明：container1为第一个容器的ID，image2为第二个容器运行image的名字。

## **(11). WORKDIR（切换目录）**

设置指令，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效。

格式：

```
WORKDIR /path/to/workdir
```

示例：

```
# 在 /p1/p2 下执行 vim a.txt  
WORKDIR /p1 
WORKDIR p2 
RUN vim a.txt
```

## **(12). ONBUILD（在子镜像中执行）**

ONBUILD 指定的命令在构建镜像时并不执行，而是在它的子镜像中执行。

格式：

```
ONBUILD <Dockerfile关键字>
```

## **(13). COPY(复制本地主机的src文件为container的dest)**

复制本地主机的src文件（为Dockerfile所在目录的相对路径、文件或目录 ）到container的dest。目标路径不存在时，会自动创建。

格式：

```
COPY <src> <dest>
```

当使用本地目录为源目录时，推荐使用COPY

## **(14). ARG(设置构建镜像时变量)**

ARG指令在Docker1.9版本才加入的新指令，ARG 定义的变量只在建立 image 时有效，建立完成后变量就失效消失

格式：

```
ARG <key>=<value>
```

## **(15). LABEL(定义标签)**

定义一个 image 标签 Owner，并赋值，其值为变量 Name 的值。

格式：

```
LABEL Owner=$Name
```

# 三、Dockerfile示例

## 编写Dockerfile

编译es启动

```
#基础镜像是centos
FROM centos:7
#指定镜像创建者信息
MAINTAINER someone
#使用RUN指定安装elasticsearch依赖的openJDK
RUN yum -y install java-1.8.0-openjdk
#将本地的elasticsearch离线包上传到容器的路径中
ADD /home/devops/huhu/elasticsearch-7.6.2.tar.gz /usr/local
RUN cd /usr/local/elasticsearch-7.6.2/config
RUN mkdir -p /data/behavior/log-node1
RUN mkdir /var/log/elasticsearch
#将本地的配置文件复制到elasticsearch的配置目录中
COPY elasticsearch.yml /usr/local/elasticsearch-7.6.2/config/
RUN useradd es && chown -R es:es /usr/local/elasticsearch-7.6.2
RUN chmod +x /usr/local/elasticsearch-7.6.2/bin/*
RUN chown -R es:es /var/log/elasticsearch
RUN chown -R es:es /data/behavior/log-node1
RUN sed -i s/-Xms1g/-Xms2g/g /usr/local/elasticsearch-7.6.2/config/jvm.options
RUN sed -i s/-Xmx1g/-Xmx2g/g /usr/local/elasticsearch-7.6.2/config/jvm.options
#打开elasticsearch的端口
EXPOSE 9200
EXPOSE 9300
#dockerfile最后启动elasticsearch
CMD su es && /usr/local/elasticsearch-7.6.2/bin/elasticsearch
```

## 上传源码包和配置文件

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329267735-ad925a4d-e651-4b60-a397-ea2e77150168.png)

## 修改配置文件

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329267755-29df362f-a09c-48be-ac05-29d620166af9.png)

## 构建镜像

```
docker build -t elasticsearch .
```

注意：命令中最后有一个点“.”，用于指定Dockerfile位置，当前已经进入到Dockerfile文件夹下，所以是点。可以替换成远程的git仓库，提前是该仓库目录下有Dockerfile

## 运行镜像

```
#查看镜像
sudo docker images
#启动镜像
sudo docker run -d -p 9200:9200 --name='elasticsearch' elasticsearch:latest
#查看容器
sudo docker ps -a
#进入容器
docker exec -it 容器ID /bin/bash
```