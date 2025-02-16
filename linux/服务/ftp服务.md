免密登录的配置文件

```
#Allow anonymous FTP
anonymous_enable=YES

# Disable local user login (if only anonymous FTP is needed)
local_enable=NO

# Enable chroot for anonymous users, limiting them to their root directory
chroot_local_user=YES
chroot_list_enable=NO

# Anonymous user settings
anon_upload_enable=NO
anon_mkdir_write_enable=NO

# Directory messages, logging
dirmessage_enable=YES
xferlog_enable=YES
xferlog_std_format=YES

# Timeout settings
idle_session_timeout=600
data_connection_timeout=120

# Listening settings (select either IPv4 or IPv6)
listen=NO
listen_ipv6=YES

# Specify the root directory for anonymous users
anon_root=/opt/iso

# Set appropriate directory permissions for security
# sudo mkdir -p /opt/iso
# sudo chown nobody:nogroup /opt/iso
# sudo chmod a-w /opt/iso

# Disable writes and uploads by anonymous users
write_enable=NO

# Security settings: TCP Wrappers, PAM service
userlist_enable=YES
tcp_wrappers=YES
pam_service_name=vsftpd

```

登录的时候用户要填anonymous 密码随意 

账号密码登录的配置文件

```
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_file=/var/log/xferlog
xferlog_std_format=YES
chroot_local_user=YES
chroot_list_enable=NO
allow_writeable_chroot=YES
chroot_list_file=/etc/vsftpd/chroot_list
listen=NO
listen_ipv6=YES
port_enable=YES
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
pasv_min_port=64000
pasv_max_port=65000
local_root=/opt


```

<https://cloud.tencent.com/developer/article/1925882> 

<https://blog.csdn.net/weixin_45688268/article/details/126337169> 