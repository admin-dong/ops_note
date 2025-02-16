首先修改系统语言

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1717859616407-a0bd9529-c352-47c0-a357-f918e49b7651.png)

```
修改系统语言：
localectl set-locale langzh_cn.utf8    #修改后重新登录。
[root@oldboy-85-vip-king-v2 ~]# localectl status
system locale: langzh_cn.utf-8
vc keymap: cn
x11 layout: cn
```

# 安装

![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1717859685164-a1d2783f-3c68-489a-a06e-16888f218ab7.png)

```
安装python
yum install -y python3 python3-pip
更新pip3(软件，设置）,下载python软件的工具。
python3 -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip
-m指定使用pip命令
-i指定下载软件包的地址，临时
--upgrade升级
永久性设置pip源
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
安装tldr
pip3 install tldr

先升级一下  
tldr -u

```