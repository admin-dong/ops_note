server {

        listen       443 ssl;
        # 域名
        server_name  travel-test.aixxxx.com;
        # 站点字符集设定
        charset uft-8;
        # 证书路径
        ssl_certificate      cert/__aixxxx_com.crt;
        # 私钥路径
        ssl_certificate_key  cert/__aixxxx_com.key;
        # SSL会话缓存时间
        ssl_session_cache shared:SSL:1m;
        # SSL会话超时时间
        ssl_session_timeout  10m;
        # SSL握手协议版本限定
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        # SSL加密算法列表
        ssl_ciphers ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS;
        ssl_prefer_server_ciphers on;
    
        #limit_req zone=lhang burst=8 nodelay;
        #access_log  /var/log/nginx/log/host.access.log  main;
    
        # 匹配根
        location /
        {
                # 添加防止页面嵌入的头
                add_header X-Frame-Options SAMEORIGIN;
                # proxy_pass   https://xx.xx.xx.xx:10443/;
                # 代理到哪里，这里也可以配置为upstream名称
                proxy_pass   https://xx.xx.xx.xx:22443/;
                # nginx控制器类L4 LB
                #proxy_pass   https://xx.xx.xx.xx:24443/;
                proxy_redirect off;
                proxy_intercept_errors on;
                proxy_next_upstream error timeout http_500 http_502 http_503 http_504 http_404;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header REMOTE-HOST $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               # 连接超时时间设定
               # proxy_connect_timeout 30;
               # 发送超时设定
               # proxy_send_timeout 30;
               # 接收超时设定
               # proxy_read_timeout 60;
               # 代理服务器缓冲区大小
               # proxy_buffer_size 256k;
               # proxy_buffers 4 256k;
               # proxy_busy_buffers_size 256k;
               # proxy_temp_file_write_size 256k;
               # proxy_max_temp_file_size 128m;
        }
}