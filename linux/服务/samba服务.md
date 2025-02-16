<https://www.cnblogs.com/muscleape/p/6385583.html>  smb

有密码的

```
[global]
workgroup = WORKGROUP
netbios name = Test_samba
server string = Linux Samba Server TestServer


[test]
path = /opt/iso
writeable = NO
browseable = yes
guest ok = yes
检验 smbclient //100.100.129.249/test

```

需要使用smbpasswd 设置密码

无密码的  匿名访问的

```
[global]
workgroup = WORKGROUP
netbios name = Test_samba
server string = Linux Samba Server TestServer
security = user
map to guest = bad user


[test]
path = /opt/iso
public=yes
writeable = NO
browseable = yes
guest ok = yes

```

