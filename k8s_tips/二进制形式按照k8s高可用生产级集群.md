# 

<https://github.com/easzlab/kubeasz>  

使用这个脚本搭建的时候

先把自己的设备 做免密登录 以及允许root账户登录

在 Ubuntu 系统中实现免密登录通常是通过使用 SSH 密钥对来实现的。以下是在 Ubuntu 中设置免密登录的步骤：

1. **生成 SSH 密钥对**：

- - 在本地计算机上打开终端。
  - 使用以下命令生成 SSH 密钥对：
  - 按照提示输入密钥保存路径和密码（如果不需要密码，可以直接按回车）。

```
ssh-keygen -t rsa
```

1. **将公钥复制到目标服务器**：

- - 使用以下命令将公钥复制到目标服务器上：

```
ssh-copy-id username@remote_host
```

其中 username 是目标服务器上的用户名，remote_host 是目标服务器的 IP 地址或主机名。

1. **验证免密登录**：

- - 现在您应该可以通过以下命令无需输入密码登录到目标服务器：

```
ssh username@remote_host
```

1. **可选步骤 - 配置 SSH 选项**：

- - 您可以编辑 SSH 配置文件（~/.ssh/config）来配置更多选项，比如指定密钥文件、端口等。

通过以上步骤，您可以在 Ubuntu 系统中实现免密登录。请确保在设置之前备份重要文件，并根据实际情况替换命令中的用户名和主机名。如果您遇到任何问题或有其他疑问，请随时告诉我。

在 Ubuntu 系统中，默认情况下，root 用户是被禁止远程登录的，这是出于安全考虑。然而，您可以通过以下步骤来允许 root 用户远程登录：

1. **启用 root 用户账户**：

- - 如果您的系统中 root 用户已经被禁用，可以使用以下命令启用 root 用户：
  - 输入您当前用户的密码，然后设置 root 用户的密码。

```
sudo passwd root
```

1. **允许 root 用户远程登录**：

- - 编辑 SSH 配置文件以允许 root 用户远程登录。使用以下命令打开 SSH 配置文件：
  - 找到 PermitRootLogin 一行，并将其改为 PermitRootLogin yes。如果没有这一行，可以手动添加。
  - 保存文件并退出编辑器。

```
sudo nano /etc/ssh/sshd_config
```

1. **重启 SSH 服务**：

- - 使用以下命令重启 SSH 服务以使更改生效：

```
sudo systemctl restart sshd
```

现在，您应该能够使用 root 用户远程登录到您的 Ubuntu 系统了。请注意，允许 root 用户远程登录可能会增加系统的安全风险，因此请谨慎使用，并确保您的系统受到良好的保护。

都好了之后呢  

bash k8s_install_new.sh 123456 10.0.0 201\ 202\ 203\ 204 containerd calico boge.com test-cn

执行命令 执行完 ansilbe报错的话 

74  2024-02-28 21:56:21 root cd /etc/kubeasz/

   75  2024-02-28 21:56:23 root ls

   76  2024-02-28 21:56:45 root ./ezctl destory test-cn

执行这些命令

这命令是删除配置的命令

然后 rm -rf /etc/kubeasz/clusters/test-cn 删掉刚刚集群信息

重新使用bash k8s_install_new.sh 123456 10.0.0 201\ 202\ 203\ 204 containerd calico boge.com test-cn

报错的原因可能是 ansible 网络没下发完 然后脚本继续往下走了 所以多执行几次 这边执行三次成功 