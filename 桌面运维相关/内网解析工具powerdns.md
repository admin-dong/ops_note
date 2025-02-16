powerdns



# 安装部署PowerDNS--实现内网DNS解析

<https://blog.csdn.net/liuy5277/article/details/140472354>

数据库安装在本地 

安装命令 docker run -d -p 80:80 -e SECRET_KEY=wmqpdns -e DB_HOST=172.16.15.20 -e DB_PORT=3306 -e DB_NAME=powerdnsadmin -e DB_USER=root -e DB_PASSWORD=Jidou1234 --name powerdns-admin2.3 ngoduykhanh/powerdns-admin:0.2.3

数据库备份路径：/app/mysqldnsback/ 备份脚本： /app/backup.sh