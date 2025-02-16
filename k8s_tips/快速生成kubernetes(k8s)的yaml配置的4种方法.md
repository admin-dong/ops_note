# 快速生成k8s的yaml配置的4种方法

通过kubectl命令行快速生成一个deployment及service的yaml标准配置

```
# 这条命令是不是很眼熟，对了，这就是博哥前面的课程里面创建deployment的命令，我们在后面加上`--dry-run -o yaml`,--dry-run代表这条命令不会实际在K8s执行，-o yaml是会将试运行结果以yaml的格式打印出来，这样我们就能轻松获得yaml配置了

# kubectl create deployment nginx --image=nginx --dry-run -o yaml       
apiVersion: apps/v1     # <---  apiVersion 是当前配置格式的版本
kind: Deployment     #<--- kind 是要创建的资源类型，这里是 Deployment
metadata:        #<--- metadata 是该资源的元数据，name 是必需的元数据项
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:        #<---    spec 部分是该 Deployment 的规格说明
  replicas: 1        #<---  replicas 指明副本数量，默认为 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:        #<---   template 定义 Pod 的模板，这是配置文件的重要部分
    metadata:        #<---     metadata 定义 Pod 的元数据，至少要定义一个 label。label 的 key 和 value 可以任意指定
      creationTimestamp: null
      labels:
        app: nginx
    spec:           #<---  spec 描述 Pod 的规格，此部分定义 Pod 中每一个容器的属性，name 和 image 是必需的
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}


# 基于上面的deployment服务生成service的yaml配置
# kubectl expose deployment nginx --port=80 --target-port=80 --dry-run -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
status:
  loadBalancer: {}
```

2、利用helm查看各种官方标准复杂的yaml配置以供参考

```
# 以查看rabbitmq集群安装的配置举例
# 首先添加chart仓库
helm repo add aliyun-apphub https://apphub.aliyuncs.com
helm repo update

# 这里我们在后面加上 --dry-run --debug 就是模拟安装并且打印输出所有的yaml配置
helm install -n rq rabbitmq-ha aliyun-apphub/rabbitmq-ha --dry-run --debug 
```

3、将docker-compose转成k8s的yaml格式配置

```
# 下载二进制包
# https://github.com/kubernetes/kompose/releases

# 开始转发yaml配置
./kompose-linux-amd64 -f docker-compose.yml convert
```

4、docker命令输出转换成对应的yaml文件示例

```
这里以 Prometheus Node Exporter 为例演示如何运行自己的 DaemonSet。
Prometheus 是流行的系统监控方案，Node Exporter 是 Prometheus 的 agent，以 Daemon 的形式运行在每个被监控节点上。
如果是直接在 Docker 中运行 Node Exporter 容器，命令为：

docker run -d \
    -v "/proc:/host/proc" \
    -v "/sys:/host/sys" \  
    -v "/:/rootfs" \
    --net=host \  
    prom/node-exporter \  
    --path.procfs /host/proc \  
    --path.sysfs /host/sys \  
    --collector.filesystem.ignored-mount-points "^/(sys|proc|dev|host|etc)($|/)"



将其转换为 DaemonSet 的 YAML 配置文件 node_exporter.yml：

apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: node-exporter-daemonset
spec:
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      hostNetwork: true      # <<<<<<<< 1 直接使用 Host 的网络
      containers:
      - name: node-exporter
        image: prom/node-exporter
        imagePullPolicy: IfNotPresent
        command:             # <<<<<<<< 2 设置容器启动命令
        - /bin/node_exporter
        - --path.procfs
        - /host/proc
        - --path.sysfs
        - /host/sys
        - --collector.filesystem.ignored-mount-points
        - ^/(sys|proc|dev|host|etc)($|/)
        volumeMounts:        # <<<<<<<< 3 通过Volume将Host路径/proc、/sys 和 / 映射到容器中
        - name: proc
          mountPath: /host/proc
        - name: sys
          mountPath: /host/sys
        - name: root
          mountPath: /rootfs
      volumes:
      - name: proc
        hostPath:
          path: /proc
      - name: sys
        hostPath:
          path: /sys
      - name: root
        hostPath:
          path: /


```