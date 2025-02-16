[TOC]

# 一.Helm概述

## 1.为什么需要Helm

```
    由于Kubernetes缺少对发布的应用版本管理和控制，使得部署的应用维护和更新等面临诸多的太挑战。主要面临以下问题:
        (1)如果将这些服务作为一个整体管理?
        (2)这些资源文件如何高效复用?
        (3)不支持应用级别的版本管理?
```

## 2.Helm介绍

```
    Helm是Kubernetes的包管理工具，就像Linux下的包管理器，如centos系统的yum，Ubuntu系统的apt等，可以方便将之前打包好的yaml文件部署到Kubernetes上。

    helm有以下几个重要概念:
        helm:
            一个命令行客户端工具，主要用于kubernetes应用chart的创建，打包，发布和管理。
        chart:
            应用描述，一系列用于描述K8S资源相关文件的集合。
        Release:
            基于Chart的部署实体，一个chart被Helm运行后将会生成对应的一个release;将在K8S中创建出真实运行的资源对象。
```

## 3.Helm版本介绍

```
    如下图所示，Helm目前有两个版本，即V2和V3。
    
    2019年11月Helm团队发布V3版本，相比v2版本最大变化是将Tiller删除，并大部分代码重构。

    helm v3相比helm v2还做了很多优化，比如不同命名空间资源同名的情况在v3版本是允许的，我们在生产环境中使用建议搭建使用v3版本，不仅仅是因为它版本功能较强，而且相对来说也更加稳定了。

    官方地址:
        https://helm.sh/docs/intro/install/

    github地址:
        https://github.com/helm/helm/releases
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022193227716.png)

## 4.Helm的工作流程概述

```
    如下图所示，helm有以下几个核心功能:
        (1)将变量从"value.yaml"文件中获取，并渲染到chart模板文件中;
        (2)chart模板文件对应的是一系列yaml文件，会基于这些yaml清单来部署应用到kuberntes集群;
        (3)helm也有其对应的Chart仓库，这些公共仓库是一些其他开发或者运维人员编写好的chart，如果他们写的chart在我们工作中能用到就是最好的了;
```

# 二.Kubernetes的应用包管理器Helm客户端部署

## 1.下载helm二进制软件包

```
    使用heml很简单，你只需要下载一个二进制客户端包即可，会通过kubeconfig配置(通常位置在"$HOME/.kube/config")来连接kubernetes集群。

    如下图所示，我们下载最新的helm软件包，下载地址为:"https://github.com/helm/helm/releases"
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020113549558.png)

## 2.解压helm二进制软件包并配置环境变量

```
mkdir -pv /oldboyedu/softwares
tar zxf helm-v3.7.1-linux-amd64.tar.gz -C /oldboyedu/softwares/
ln -sv /oldboyedu/softwares/linux-amd64/helm /usr/bin/
helm version
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020114023167.png)

## 3.helm可用命令概述

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020114224174.png)

```
	completion:
        生成命令补全的功能。使用"source <(helm completion bash)"

    create:
        创建一个chart并指定名称。

    dependency:
        管理chart依赖关系。

    env:
        查看当前客户端的helm环境变量信息。

    get:
        下载指定版本的扩展信息。

    help:
        查看帮助信息。

    history:
        获取发布历史记录。

    install:
        安装chart。

    lint:
        检查chart中可能出现的问题。

    list:
        列出releases信息。

    package:
        将chart目录打包到chart存档文件中。

    plugin:
        安装、列出或卸载Helm插件。

    pull:
        从存储库下载chart并将其解包到本地目录。

    repo:
        添加、列出、删除、更新和索引chart存储库。

    rollback:
        将版本回滚到以前的版本。

    search:
        在chart中搜索关键字。

    show：
        显示chart详细信息。

    status:
        显示已有的"RELEASE_NAME"状态。

    template:
        本地渲染模板。

    test:
        运行版本测试。

    uninstall:
        卸载版本。

    upgrade:
        升级版本。

    verify:
        验证给定路径上的chart是否已签名且有效
  
    version:
        查看客户端版本。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020114240268.png)

## 4.配置helm命令自动补全

```
helm completion bash > .helmrc && echo "source .helmrc" >> .bashrc 
source .bashrc
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020160211470.png)

# 三.kubernetes的应用包管理器Helm工具创建Chart

## 1.Chart的组织结构

```
chart是一个组织在文件目录中的集合。目录名称就是chart名称（没有版本信息）。

因而描述WordPress的chart可以存储在"wordpress/"目录中。以官方的"wordpress"为例，其包含以下文件内容:
	Chart.yaml
  		一个包含chart的信息。
  		
  	LICENSE
  		包含chart许可证的纯文本文件。
  	
	README.md
  		一个可读的自述文件，通常是给用户看的，以便于用户更加了解该Chart的使用。
  	
	values.yaml
  		定义此chart的默认配置值。
  		
  	values.schema.json  
  		用于在values.yaml文件上施加结构的JSON模式

	charts/
    	包含此Chart的任何图表(chart)的目录。
    	
    crds/
  		自定义资源定义

	templates/         
		模板目录，与values.yaml中的值组合使用，将用来生成Kubernetes清单文件。
	
	templates/NOTES.txt 
		包含简短用法说明的纯文本文件。
		
		
参考链接:
	https://helm.sh/docs/topics/charts/#the-chart-file-structure
	https://helm.sh/zh/docs/topics/charts/#chart-%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020115440151.png)

## 2.使用helm管理chart的生命周期

```
(1)创建一个名为"oldboyedu-linux"的Char(默认部署的是nginx 1.16哟~)
cd /oldboyedu/data
helm create oldboyedu-linux

(2)创建kubernetes命名空间
kubectl create namespace oldboyedu-helm

(3)查看releases信息和Pod信息
helm list -n oldboyedu-helm 
kubectl get pods -n oldboyedu-helm  -o wide

(4)部署自定义的Chart
helm install my-web01 /oldboyedu/data/oldboyedu-linux -n oldboyedu-helm

(5)再次查看releases信息和Pod信息
helm list -n oldboyedu-helm 
kubectl get pods -n oldboyedu-helm  -o wide

(6)卸载自定义的Chart
helm uninstall my-web01 -n oldboyedu-helm 


温馨提示:
	一旦我们卸载了一个Chart，这意味着其创建的K8S资源也将被随之删除哟，因此生产环境中谨慎操作！
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020161447259.png)

## 3.基于helm部署tomcat-demo应用-未使用"values.yaml"文件

```
(1)创建一个名为"oldboyedu-linux"的Chart(默认部署的是nginx 1.16哟~)
cd /oldboyedu/data
helm create oldboyedu-linux


(2)删除模板文件
cd /oldboyedu/data/oldboyedu-linux/templates/
rm -rf *.yaml
rm -rf tests/


(3)自定义部署chart后的提示信息
cd /oldboyedu/data/oldboyedu-linux/
> values.yaml   # 
echo "Welcome oldboyedu Linux" > templates/NOTES.txt  
cat > Chart.yaml <<EOF
apiVersion: v2
name: oldboyedu-linux
description:  oldboyedu k8s tomcat demo.
type: application
version: v0.1
appVersion: "1.0"
EOF

(3)在"oldboyedu-helm"名称空间下，自定义tomcat服务基于helm部署，注意文件的存放路径哟
cd /oldboyedu/data/oldboyedu-linux/templates

cat > 01-mysql-deploy.yml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: oldboyedu-db-mysql57
  namespace: oldboyedu-helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app: oldboyedu-mysql57
  template:
    metadata:
      labels:
        app: oldboyedu-mysql57
    spec:
      containers:
        - name: mysql
          image: k8s101.oldboyedu.com:5000/mysql:5.7
          ports:
          - containerPort: 3306
          env:
          - name: MYSQL_ROOT_PASSWORD
            value: '123456'
EOF

cat > 02-mysql-svc.yml << EOF
apiVersion: v1
kind: Service
metadata:
  name: oldboyedu-db-mysql57-svc
  namespace: oldboyedu-helm
spec:
  ports:
    - port: 3306
      targetPort: 3306
  selector:
    app: oldboyedu-mysql57
EOF

cat > 03-tomcat-deploy.yml <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: oldboyedu-web-tomcat-app
  namespace: oldboyedu-helm
spec:
  replicas: 3
  selector:
    matchLabels:
      app: oldboyedu-tomcat-app
  template:
    metadata:
      labels:
        app: oldboyedu-tomcat-app
    spec:
      containers:
        - name: myweb
          image: k8s101.oldboyedu.com:5000/tomcat-app:v1
          ports:
          - containerPort: 8080
          env:
          - name: MYSQL_SERVICE_HOST
            value: oldboyedu-db-mysql57-svc
          - name: MYSQL_SERVICE_PORT
            value: '3306'
EOF

cat > 04-tomcat-svc.yml <<EOF
apiVersion: v1
kind: Service
metadata:
  name: oldboyedu-web-tomcat-app-svc
  namespace: oldboyedu-helm
spec:
  type: NodePort
  ports:
    - port: 8080
      nodePort: 30008
  selector:
    app: oldboyedu-tomcat-app
EOF


(4)安装咱们自定义的chart
helm install oldboyedu-tomcat /oldboyedu/data/oldboyedu-linux/ -n oldboyedu-helm 


(5)查看安装是否成功
helm list -n oldboyedu-helm
kubectl get all -n oldboyedu-helm


(6)访问tomcat的WebUI
如下图所示，直接访问"http://10.0.0.101:30008/demo/"即可！


(7)测试结束，删除资源
helm uninstall oldboyedu-tomcat -n oldboyedu-helm


温馨提示:
	(1)为了方便学习，我们先清空"values.yaml"里面的所有配置信息，稍后我们会手写该文件内容;
	(2)在“NOTES.txt”文件本案例并不会引用任何变量，而且"values.yaml"已被咱们清空;
	(3)上面的部署Pod的方式就是基于之前编写的yaml文件来完成部署的，貌似也看不出来helm的功能呀！没错，如果按照上面的方式的确没有体现出helm的强大之处，但上面的案例我只是想要告诉大家helm有这个功能，但想要充分返回helm的独特功能，还得借助"values.yaml"文件。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020174101272.png)

## 4.基于helm部署Nginx应用-使用"values.yaml"文件

```
(1)创建一个名为"oldboyedu-nginx"的Chart(默认部署的是nginx 1.16哟~)
cd /oldboyedu/data
helm create oldboyedu-nginx


(2)自定义"values.yaml"文件
cd /oldboyedu/data/oldboyedu-nginx/
cat > values.yaml << EOF
school: ["北京","上海","深圳"]
replicas: 3
image: "k8s101.oldboyedu.com:5000/nginx"
imageTag: "1.14"
EOF


(3)自定义版本Chat版本信息
cd /oldboyedu/data/oldboyedu-nginx/
cat > Chart.yaml << EOF
apiVersion: v2
name: oldboyedu-nginx
description: oldboyedu Linux Nginx demo ...
type: application
version: v0.1
appVersion: 0.1
EOF


(4)删除无用的文件
cd /oldboyedu/data/oldboyedu-nginx/templates
rm -rf *.yaml
rm -rf tests
echo """
	老男孩教育欢迎您......[ 官网地址: https://www.oldboyedu.com/ ]
	老男孩教育(oldboyedu)全国学校城市: {{ .Values.school }}, image: [{{ .Values.image }}:{{ .Values.imageTag }}]" > NOTES.txt


(5)创建"Deployment"和"Service"资源的配置文件并引用"values.yaml"文件中的变量
cd /oldboyedu/data/oldboyedu-nginx/templates
cat > myweb-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  # 注意哈，此处的".Release.Name"我们并没有在"../values.yaml"文件中定义，该变量表示的是我们在部署chart时指定Release的名称
  name: {{ .Release.Name }}-deployment
  namespace: oldboyedu-helm
spec:
  # 此处我引用的是"../values.yaml"文件中的"replicas"变量
  replicas: {{ .Values.replicas }}
  selector:
    matchLabels:
      app: mynginx
  template:
    metadata:
      labels:
        app: mynginx
    spec:
      containers:
      - name: web
        # 此处我引用的是"../values.yaml"文件中的变量
        image: {{ .Values.image }}:{{ .Values.imageTag }}
EOF

cat > myweb-service.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-service
  namespace: oldboyedu-helm
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: mynginx
  type: NodePort
EOF


(6)部署服务
helm install oldboyedu-web-nginx /oldboyedu/data/oldboyedu-nginx/ -n oldboyedu-helm 


(7)验证服务
使用curl命令访问Pod的IP即可，注意观察Nginx的版本哟~


(8)卸载服务
helm uninstall oldboyedu-web-nginx -n oldboyedu-helm


温馨提示:
	helm的核心是模板，即模板化K8S YAML文件。部署多个应用时，将需要改动的字段进行模板化，可动态传入。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211020190422971.png)

## 5.使用了"--dry-run"选项并未真正部署!它会列出要执行的yaml文件内容!

```
helm install oldboyedu-web-nginx /oldboyedu/data/oldboyedu-nginx/ -n oldboyedu-helm --dry-run


温馨提示:
	如下图所示，并不会部署服务，而是会输出要运行的资源清单文件列表哟。
```

# 四.Kubernetes的应用包管理器Helm工具升级Chart的Release

## 1.基于helm升级Chart的Release概述

```
为了实现Chart复用，可动态传参修改"values.yaml"文件中的变量值，有以下两种方式:
	--values,-f:
		基于yaml配置文件方式升级Release。
		例如:"helm upgrade  -f /oldboyedu/data/oldboyedu-nginx/values.yaml myweb02 /oldboyedu/data/oldboyedu-nginx"
		
	--set:
		基于命令行方式升级Release。例如:"helm upgrade --set imageTag=1.18 myweb02 /oldboyedu/data/oldboyedu-nginx"

    如下图所示，对比了基于yaml配置文件和基于命令行方式升级Release的差别。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021091026504.png)

## 2.使用helm基于yaml配置文件方式升级Nginx应用

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021093529021.png)

```
(1)安装带升级的Chart
helm install oldboyedu-nginx-001 /oldboyedu/data/oldboyedu-nginx/ -n oldboyedu-helm
helm list -n oldboyedu-helm

(2)查看上一步已安装Chart的"Release"信息历史的发布版本
helm history oldboyedu-nginx-001 -n oldboyedu-helm

(3)在为升级服务之前，请使用curl命令测试
kubectl get pods -n oldboyedu-helm -o wide
curl -I 10.244.2.39  # 该IP是上面的命令查看Pod的IP的引用哟,注意观察nginx的版本信息。

(4)将"oldboyedu-nginx-001"这个已安装Chart的"Release"进行升级(sed指令的操作结果如上图所示)
sed -ri 's#(imageTag: )"1.14"#\1"1.16"#' /oldboyedu/data/oldboyedu-nginx/values.yaml 
helm upgrade -f /oldboyedu/data/oldboyedu-nginx/values.yaml oldboyedu-nginx-001  /oldboyedu/data/oldboyedu-nginx

(5)验证升级是否成功(升级意味会创建新的Pod，而删除旧的Pod哟~)
helm list -n oldboyedu-helm  # 升级后观察的REVISION信息，如下图所示。
kubectl get pods -n oldboyedu-helm -o wide
curl -I 10.244.3.33

(6)查看名为“oldboyedu-nginx-001”的Chart历史的版本信息。
helm history oldboyedu-nginx-001 -n oldboyedu-helm
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021094050422.png)

## 3.使用helm基于命令行方式升级Nginx应用

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021102951652.png)

```
(1)如上图所示，基于命令行升级的方式特别的简单
helm upgrade --set imageTag=1.18 oldboyedu-nginx-001 /oldboyedu/data/oldboyedu-nginx -n oldboyedu-helm 

(2)测试是否升级成功
	测试步骤如下图所示。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021103755372.png)

## 4.使用helm基于命令行方式升级Release，将nginx版本修改为"1.14"版本，并将Pod的副本数设置为1

```
helm upgrade --set imageTag=1.14,replicas=1 oldboyedu-nginx-001 /oldboyedu/data/oldboyedu-nginx -n oldboyedu-helm
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021104442974.png)

# 五.Kubernetes的应用包管理器Helm工具回滚Chart的Release

## 1.未回滚之前，查看某个Chart的历史发布版本信息

```
如下图所示，我们在此之前已经做了很多次操作了，接下来我们进行一下版本回滚试试看。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021104940092.png)

## 2.回滚到上一个发行版本

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021110353335.png)

```
(1)如上图所示,查看回滚之前的状态后，执行下面的命令进行回滚到上一个版本(当前版本为4，回滚到3哟~)
helm rollback oldboyedu-nginx-001 -n oldboyedu-helm 

(2)回滚到上一个版本
	测试结果如下图所示。
	
	
温馨提示:
	我这里测试的实验和之前的笔记相吻合，也就是在回滚之前，副本数为1，nginx镜像版本为1.14.2哟~
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021110505576.png)

## 3.回滚到指定的发型版本

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021110922575.png)

```
helm rollback oldboyedu-nginx-001 1 -n oldboyedu-helm
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021111118883.png)

# 六.Kubernetes的应用包管理器Helm工具公共Chart仓库管理

## 1.主流的Chart仓库概述

```
    国内Chart仓库，可以直接使用他们制作好的包:
        官方仓库:
            https://hub.kubeapps.com/charts/incubator

        微软仓库:
            http://mirror.azure.cn/kubernetes/charts/

        阿里云仓库:
            https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

    添加仓库的方式:
        helm repo list
            查看现有的仓库信息，默认情况下是没有任何仓库地址的
        helm repo add azure http://mirror.azure.cn/kubernetes/charts/ 
            注意哈，此处我们将微软云的仓库添加到咱们的helm客户端仓库啦~
        helm repo update  
            我们也可以更新仓库信息哟~
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021115502502.png)

## 2.手动添加微软云和阿里云Chart仓库

```
(1)未添加存储库之前，先查看仓库信息
helm repo list

(2)添加微软云的Chart仓库repositories
helm repo add azure http://mirror.azure.cn/kubernetes/charts/

(3)添加阿里云的Chart仓库 repositories
helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

(4)再次查看仓库信息
helm repo list

(5)更新仓库信息
helm repo update


温馨提示:
	当我们成功添加了存储库后记得更新一下哈，这会方便我们下一步进行哟~
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021160423739.png)

## 3.搜索我们关心的chart

```
helm search repo (mysql|redis|kafka|openvpn|...)


温馨提示:
	你可以尝试搜索一下所关心的Chart是否拥有，如果有的话可以下载直接使用哟。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021161721954.png)

## 4.案例一: 下载公网的mysql来进行使用

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021164010079.png)

```
(1)我们将"aliyun/mysql"这个chart下载到本地并解压
helm pull aliyun/mysql --untar

(2)修改"mysql/templates/deployment.yaml"文件
sed -ri 's#(apiVersion: )extensions/v1beta1#\1apps/v1#' mysql/templates/deployment.yaml

(3)再次修改"mysql/templates/deployment.yaml"文件，在deployment资源的spec下级添加如下代码，和"template"要同级哟~
      selector:
        matchLabels:
          app: {{ template "mysql.fullname" . }}

(4)手动创建PV，因为咱们下一步安装MySQL会用到PVC，会自动关联后台的PV，如果有动态PV则可以省略该步骤!
cat > 01-pv-10g.yaml << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oldboyedu-pv01
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  hostPath:
    path: "/tmp/oldboyedu-mysql"
EOF

(5)安装修改后的mysql，安装成功如下图所示
cd /oldboyedu/data
helm install oldboyedu-mysql mysql/

(6)测试链接MySQL
kubectl get pods -o wide  # 查看MySQL的IP地址
mysql -h 10.244.1.34 -p`kubectl get secret --namespace default oldboyedu-mysql-mysql -o jsonpath="{.data.mysql-root-password}" | base64 -d`



温馨提示:
	如果集群不正常工作，请使用"kubectl describe pods [POD]"来进行分析问题。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021174302894.png)

## 5.课堂练习: 下载公网的redis来进行使用

```
略，笔记请自行填充...
```

# 七.Kubernetes的应用包管理器Helm工具私有Chart仓库管理

## 1.使用docker部署Chartmuseum私有Chart仓库

```
(1)创建数据持久化目录
mkdir -pv /oldboyedu/data/chartmuseum

(2)启动chartmuseum容器
docker container run -d \
  -p 8080:8080 \
  -e DEBUG=1 \
  -e STORAGE=local \
  -e STORAGE_LOCAL_ROOTDIR=/charts \
  -v /oldboyedu/data/chartmuseum:/charts \
  --restart always \
  chartmuseum/chartmuseum:latest

(3)如下图所示，使用curl工具访问下接口，没有报错就行，当前仓库内容还是空的
curl http://10.0.0.101:8080/api/charts



推荐阅读:
	https://github.com/helm/chartmuseum
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022094533930.png)

## 2.准备helm及离线chart，推送到私有库

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022113002144.png)

```
(1)添加官方的仓库并搜索你所关心的Chart
helm repo add azure http://mirror.azure.cn/kubernetes/charts/
helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts

(2)下载社区开源的Chart包到本地私有仓库
cd /oldboyedu/data/chartmuseum/
helm pull aliyun/mysql
helm pull aliyun/redis
helm pull azure/tomcat
helm pull azure/kafka-manager

(3)浏览器查看验证
	如下图所示，再次访问"http://10.0.0.101:8080/api/charts"即可。
	

温馨提示:
	(1)外面也可以使用curl命令结合jq工具(需要epel源才能安装)来查看输出哟;
		curl http://10.0.0.101:8080/api/charts | jq
		
	(2)我的浏览器可以看到很不错的JSON输出，是因为用了Google浏览器的插件啦，可自行下载。
		https://github.com/gildas-lormeau/JSONView-for-Chrome
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022115221501.png)

## 3.添加私有的仓库

```
(1)查看本地的源
helm repo list 

(2)添加私有的helm仓库
helm repo add oldboyedu-helm http://10.0.0.101:8080/

(3)只查看私有仓库的helm仓库的Chart信息
helm search repo oldboyedu-helm
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022162241648.png)

## 4.使用 helm cm-push 插件上传自定义Chart

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022170345165.png)

```
(1)将自定义的Chart进行打包
helm package oldboyedu-mysql

(2)修改权限，否则执行下一条命令时会报错""
chmod 777 /oldboyedu/data/chartmuseum/

(3)使用 helm cm-push 插件上传自定义Chart
yum -y install git
helm plugin install https://gitee.com/jasonyin2020/helm-push.git
helm cm-push oldboyedu-k8s122-mysql-v0.1.tgz oldboyedu-helm
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211022192405490.png)

## 5.其它操作

```
推荐阅读:
	https://www.jianshu.com/p/18478cf7a37f
```

# 八.helm客户端其它常用的命令指南

```
helm --help
helm [command] --help
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021115626225.png)

# 九.可能会遇到的错误

## 1.Error: INSTALLATION FAILED: unable to build kubernetes objects from release manifest: unable to recognize "": no matches for kind "Deployment" in version "extensions/v1beta1"

```
问题原因:
	如下图所示，很明显是由于deployment资源的版本有问题导致的。这是由于开源的Chart的yaml清单基于较低的K8S版本编写，而我们使用的

解决方案:
	方案一:
		更换较低的K8S集群版本，这种方案很明显不太可取。成本太大。
		
	方案二:
		手动修改k8s的资源清单，将版本号改为新版本的即可，这需要维护者多少懂一点K8S集群知识。
		
	方案三:
		更换其它的Chart来替换，但是这就得看是否有相关的开源Chart，这种情况就得看运气了。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021165631257.png)

## 2.Error: INSTALLATION FAILED: unable to build kubernetes objects from release manifest: error validating "": error validating data: ValidationError(Deployment.spec): missing required field "selector" in io.k8s.api.apps.v1.DeploymentSpec

```
问题原因:
	如下图所示，缺少"selector"标签选择器。

解决方案:
	在deployment资源的spec下级添加如下代码，和"template"要同级哟~
      selector:
        matchLabels:
          app: {{ template "mysql.fullname" . }}
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021172941343.png)

## 3.0/4 nodes are available: 4 pod has unbound immediate PersistentVolumeClaims.

```
问题原因:
	创建Pod时PVC无法关联后台的PV，需要运维人员介入手动处理。


解决方案:
	方案一:
		手动创建可用的PV。
            cat > 01-pv-10g.yaml << EOF
            apiVersion: v1
            kind: PersistentVolume
            metadata:
              name: oldboyedu-pv01
            spec:
              capacity:
                storage: 10Gi
              volumeMode: Filesystem
              accessModes:
                - ReadWriteOnce
              persistentVolumeReclaimPolicy: Recycle
              hostPath:
                path: "/tmp/oldboyedu-mysql"
            EOF
            kubectl apply -f 01-pv-10g.yaml

	方案二:
		使用动态PV。
```

![img](04-%E8%80%81%E7%94%B7%E5%AD%A9%E6%95%99%E8%82%B2-helm%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.assets/image-20211021174733117.png)