**1) 原因 **

```
用户家目录没有,用户家目录下面的配置文件没了 ~/.bashrc 
~/.bash_profile
```

**2) ****解决 **

通过/etc/skel/.bash* 复制并解决 

```
cp /etc/skel/.bash* ~
```

/etc/skel 目录所有**新用户**的家目录的模板. 

**3) ****复现故障 **

```
#oldboy用户下操作
\rm -fr ~/.*
cp /etc/skel/.bash*   ~
```

