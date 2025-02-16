**软件简介**

简介

```
	knb 全称 Kubernetes Network Benchmark,它是一个 bash 脚本，它将在目标 Kubernetes 集群上启动网络基准测试。
	
参考资料：
	https://github.com/InfraBuilder/k8s-bench-suite
```

```
它的主要特点如下：
    依赖很少的普通 bash 脚本
    完成基准测试仅需 2 分钟
    能够仅选择要运行的基准测试的子集
    测试TCP 和 UDP带宽
    自动检测 CNI MTU
    在基准报告中包括主机 cpu 和 ram 监控
    能够使用 plotly/orca 基于结果数据创建静态图形图像（参见下面的示例）
    无需 ssh 访问，只需通过标准 kubectl访问目标集群
    不需要高权限，脚本只会在两个节点上启动非常轻量级的 Pod。
    它提供了数据的图形生成镜像和配套的服务镜像
```

准备工作

```
master节点下载核心服务镜像
docker pull olegeech/k8s-bench-suite

特定node节点下载镜像
docker pull infrabuilder/bench-custom-monitor
docker pull infrabuilder/bench-iperf3
docker pull quay.io/plotly/orca
```

注意事项

```
由于需要测试k8s节点间的网络通信质量并且将结果输出，所以它在操作的过程中需要提前做一些准备
	1 准备k8s集群的认证config文件
	2 准备数据文件的映射目录
```

**简单实践**

命令解析

```
对于容器内部的knb命令来说，它的基本格式如下：
	knb --verbose --client-node 客户端节点 --server-node 服务端节点
	
注意：
	docker run时候，可以通过NODE_AUTOSELECT从集群中自动选择几个节点来运行测试
		- 如果master节点的污点无法容忍，从而导致失败
```

```
查看命令帮助
docker run -it --hostname knb --name knb --rm -v /tmp/my-graphs:/my-graphs -v /root/.kube/config:/.kube/config olegeech/k8s-bench-suite --help

注意：
	如果网络效果不是很好的话，会因为镜像无法获取而导致失败，需要重试几次即可
	所以，如果可以的话，提前下载到特定的工作节点上
```

测试效果

```
生成检测数据
docker run -it --hostname knb --name knb --rm -v /tmp/my-graphs:/my-graphs -v /root/.kube/config:/.kube/config olegeech/k8s-bench-suite --verbose --server-node kubernetes-node1 --client-node kubernetes-node2 -o data -f /my-graphs/mybench.knbdata
```

```
以json方式显示数据
docker run -it --hostname knb --name knb --rm -v /tmp/my-graphs:/my-graphs -v /root/.kube/config:/.kube/config olegeech/k8s-bench-suite -fd /my-graphs/mybench.knbdata -o json
```

```
根据数据进行绘图展示
 docker run -it --hostname knb --name knb --rm -v /tmp/my-graphs:/my-graphs -v /root/.kube/config:/.kube/config olegeech/k8s-bench-suite -fd /my-graphs/mybench.knbdata --plot --plot-dir /my-graphs/
设定数据的图形样式
docker run -it --hostname knb --name knb --rm -v /tmp/my-graphs:/my-graphs -v /root/.kube/config:/.kube/config olegeech/k8s-bench-suite -fd /my-graphs/mybench.knbdata --plot --plot-args '--width 900 --height 600' --plot-dir /my-graphs
```

**小结**