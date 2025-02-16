## **1.安装前置需求**

https://docs.sonarsource.com/sonarqube/9.9/requirements/prerequisites-and-overview/

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329796916-0ca44949-4c00-4e15-942d-12ad2f8f5b45.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329796898-5302fdda-2087-4ad4-927a-9c14a35eca7e.png)

此次安装选择jdk17 和 PostgreSQL-15

openjdk-17下载地址：

<https://jdk.java.net/archive/>

<https://download.java.net/java/GA/jdk17/0d483333a00540d886896bac774ff48b/35/GPL/openjdk-17_linux-x64_bin.tar.gz>

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329796996-d0b7c09b-bc54-4f91-b4d4-1b7554f6d1f6.png)

```
#如果没有rz命令使用以下命令安装
yum install -y lrzsz

#上传到服务器
rz

#解压
tar vxf openjdk-17_linux-x64_bin.tar.gz

#修改环境变量
sudo vim /etc/profile
```

```
#在profile最后面添加 {$JAVA_HOME}替换成你jdk的位置
export PATH="{$JAVA_HOME/bin}:$PATH"
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329797009-9795b2a1-25fb-408b-b3af-dbc65cabf88d.png)

```
#重新加载环境变量
source /etc/profile

#[可选]可以使用如下命令查看是否成功 成功则显示jdk版本等信息
java --version
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329797009-bcd8c8f7-e3b0-4cb3-b660-729955d34fc3.png)

按照图上所述配置其他变量值

有两种方法 永久修改 和 临时修改 

### 永久修改方法：

```
vim /etc/sysctl.conf
```

在此文件末尾添加如下

```
vm.max_map_count=524288
fs.file-max=131072
```

```
#重新加载变量
sysctl -p

#[可选] 使用如下命令打印出变量值看是否修改成功
sysctl vm.max_map_count
sysctl fs.file-max
```

```
vim /etc/security/limits.conf
```

在此文件添加

```
* - nofile 131072
* - nofile 8192
#注意 如果此两项已经存在则修改值即可
#hard
#soft
#都要修改
```

重新打开终端来加载变量

使用如下命令查看是否成功

```
ulimit -n
ulimit -u
```

### 临时修改方法：

```
sysctl -w vm.max_map_count=524288
sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192
```

## 2.**安装postgres-15**

```
#使用如下命令 建议一条一条执行 因为在安装的时候可能会出现依赖问题
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
sudo yum install -y postgresql15-server


sudo /usr/pgsql-15/bin/postgresql-15-setup initdb
sudo systemctl enable postgresql-15
sudo systemctl start postgresql-15
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329797910-b4d6532f-12f2-4e16-9d1c-c81da139f370.png)

如果出现以上问题 使用如下命令解决：

```
sudo yum install epel-release.noarch -y
sudo yum install libzstd.x86_64 -y
```

## **3.修改postgres-15 密码**

```
sudo -u postgres psql
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798127-db54d038-2db5-46ac-b8df-b4b11a6c88e3.png)

### 进入到psql操作模式

```
postgres=# ALTER USER postgres WITH PASSWORD '123456';     //密码自定义 这里直接给sonar超管角色postgres


postgres=# CREATE DATABASE sonarqube;     //创建sonarqube数据库
```

## 4.**安装配置****SonarQube**

下载地址：<https://www.sonarsource.com/products/sonarqube/downloads/>

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798152-da8b11f1-216d-454b-b313-1aadcd169978.png)

这里下载最新的LTS版本 9.9.1 LTS

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798500-bce71775-4c93-4cc5-9c92-4664161a14d1.png)

下载的文件是一个压缩包

将其上传到服务器上

```
#如果没有rz命令 使用以下命令安装
sudo install -y lrzsz

#选择文件上传
rz

#如果没有unzip 使用以下命令安装
sudo install -y unzip

#解压文件
unzip sonar-xxxx.zip
```

解压后如下图所示

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798595-550a3016-6efb-4464-8ac9-8f7615c1804b.png)

```
cd sonarqube-9.9.1.69595/
cd conf
vim sonar.properties
```

在配置最后添加如下配置

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798616-70318f1b-5b4a-4d68-adeb-167142282c0d.png)

```
sonar.jdbc.username=postgres
sonar.jdbc.password=123456
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
```

返回上级目录

```
cd ..
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798696-6f99c1b0-7f6a-4f9b-9f6b-27ef3661e55c.png)

进入 bin 目录

```
cd bin
#可以看到有四个文件 进入 linux-x86-64
cd linux-x86-64/

./sonar.sh start
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329798867-4eba7e0b-92c3-4e4e-95b8-d26abb5856d6.png)

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329799174-7ae94dcb-c054-46a5-9fe5-546eb601fffd.png)

记住千万不要使用root角色

不然会导致es启动失败

至此完毕

访问 http://{server_ip}:9000 ({server_ip} ---> 服务器ip地址)

即可看到以下画面

默认账号密码: admin/admin

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329799344-01cc3c86-1286-4eca-ae3a-40f88f9adda9.png)

如果启动失败

前往logs目录查看日志

```
cd /home/vagrant/sonarqube-9.9.1.69595/logs
```

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1731329799314-a1aefb8d-37ff-4b1f-9ad8-2e1a9dedd5a7.png)