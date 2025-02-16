nsenter 位于 util-linux 包中，一般常用的 Linux 发行版都已经默认安装。如果你的系统没有安装，可以使用以下命令进行安装：

```
yum install util-linux
```

一个比较典型的用途就是进入容器的网络命名空间。通常容器为了轻量级，大多都是不包含较为基础网络管理调试工具，比如：ip、ping、telnet、ss、tcpdump 等命令，给调试容器内网络带来相当大的困扰。

nsenter 命令可以很方便的进入指定容器的网络命名空间，使用宿主机的命令调试容器网络。 除此以外，nsenter 还可以进入 mnt、uts、ipc、pid、user 等命名空间，以及指定根目录和工作目录

### 使用

命令参数如下：

```
nsenter [options] [program [arguments]]

options:

-a, --all enter all namespaces of the target process by the default /proc/[pid]/ns/* namespace paths.
-m, --mount[=<file>]：进入 mount 命令空间。如果指定了 file，则进入 file 的命名空间
-u, --uts[=<file>]：进入 UTS 命名空间。如果指定了 file，则进入 file 的命名空间
-i, --ipc[=<file>]：进入 System V IPC 命名空间。如果指定了 file，则进入 file 的命名空间
-n, --net[=<file>]：进入 net 命名空间。如果指定了 file，则进入 file 的命名空间
-p, --pid[=<file>：进入 pid 命名空间。如果指定了 file，则进入 file 的命名空间
-U, --user[=<file>：进入 user 命名空间。如果指定了 file，则进入 file 的命名空间
-t, --target <pid> # 指定被进入命名空间的目标进程的 pid
-G, --setgid gid：设置运行程序的 GID
-S, --setuid uid：设置运行程序的 UID
-r, --root[=directory]：设置根目录
-w, --wd[=directory]：设置工作目录
```

常用参数

```
$ nsenter -a -t <pid> <command>
$ nsenter -m -u -i -n -p -t <pid> <command>
```

## 使用

通过在实际k8s环境举例说明如何在容器中抓包

### 获取容器ID

以进入coredns这个容器抓包

```
kubectl describe pods coredns-68946bf5b-jvqnx -n kube-system
```

containerd运行时输出片段

![云计算-使用nesenter进入netns抓包分析pod网络故障_抓包](https://file.cfanz.cn/uploads/png/2024/11/13/11/29P4BfC068.png)

```
Controlled By:  ReplicaSet/coredns-68946bf5b
Containers:
  coredns:
    Container ID:  containerd://28e21dcef060893cef07f4c109aaee7813e0d18d83b2c2566db818ab122f900c
```

docker运行时输出片段

```
Controlled By:  ReplicaSet/coredns-68946bf5b
Containers:
  coredns:
    Container ID:  docker://28e21dcef060893cef07f4c109aaee7813e0d18d83b2c2566db818ab122f900c
```

### 获取PID

拿到 container id 后，我们登录到 pod 所在节点上去获取其主进程 pid。

首先确定pod运行在哪个节点上

![云计算-使用nesenter进入netns抓包分析pod网络故障_抓包_02](https://file.cfanz.cn/uploads/png/2024/11/13/11/TRJYRC6Y39.png)

登录该节点

containerd 运行时使用 crictl 命令获取:

```
crictl inspect 28e21dcef060893cef07f4c109aaee7813e0d18d83b2c2566db818ab122f900c | grep -i pid
#输出
    "pid": 4605,
            "pid": 1
            "type": "pid"
```

![云计算-使用nesenter进入netns抓包分析pod网络故障_抓包_03](https://file.cfanz.cn/uploads/png/2024/11/13/11/T600S747R0.png)

查询到pid为4605

dockerd 运行时使用 docker 命令获取:

```
$ docker inspect e64939086488a9302821566b0c1f193b755c805f5ff5370d5ce5e6f154ffc648 | grep -i pid
#输出
						"Pid": 910351,
            "PidMode": "",
            "PidsLimit": 0,
```

## 进入容器的netns

```
nsenter -n -t 4605
```

成功进入容器的 netns，可以使用节点上的网络工具进行调试网络，可以首先使用 `ip a` 验证下 ip 地址是否为 pod ip:

![云计算-使用nesenter进入netns抓包分析pod网络故障_f5_04](https://file.cfanz.cn/uploads/png/2024/11/13/11/eO16155170.png)

此时进入了容器空间，需要抓包，则可以使用节点上的抓包工具

## tcpdump抓包

常用抓包

```
# 抓包内容实时显示到控制台
tcpdump -i eth0 host 10.0.0.10 -nn -tttt
tcpdump -i any host 10.0.0.10 -nn -tttt
tcpdump -i any host 10.0.0.10 and port 8088 -nn -tttt
# 抓包存到文件
tcpdump -i eth0 -w test.pcap
# 读取抓包内容
tcpdump -r test.pcap -nn -tttt
```

![云计算-使用nesenter进入netns抓包分析pod网络故障_命名空间_05](https://file.cfanz.cn/uploads/png/2024/11/13/11/fceC8E9411.png)

-r: 指定包文件。

-nn: 显示数字ip和端口，不转换成名字。

-tttt: 显示时间戳格式: 2006-01-02 15:04:05.999999。

抓到包后，通过wireshark分析

![云计算-使用nesenter进入netns抓包分析pod网络故障_f5_06](https://file.cfanz.cn/uploads/png/2024/11/13/11/3C032H2A55.png)

## 总结

- 如果不需要进入容器内部，则直接可以tcpdum -i指定节点的物理网卡或者虚拟网卡抓包
- 正常网络排查方法，节点上抓取物理网卡一次，容器上抓取虚拟网卡一次，两边一对比，就能判断出网络流量进入容器的时候是否存在异常

