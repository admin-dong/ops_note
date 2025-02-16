**20230111ingress日志打印自定义header**

*背景：，在流量入口(kong)给每个请求里面加了个x-transaction-id的header，ingress日志里面没有记录x-transaction-id，有时候排查问题会不方便，所以需要修改ingress配置，将x-transaction-id打印在日志里面*

## **默认****ingress日志格式如下：**

$$
Bashlog_format upstreaminfo '$the_real_ip - [$the_real_ip] - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id;
$$



## **将x-transaction-id增加到尾部即可**

然后自定义的变量需要加上http_前缀，并且变量里面的短横杠要换成下划线。也就是变量名http_x_transaction_id

## **修改****ingress****配置**

找到ingress对应的configmap，然后在data字段内增加以下配置：
$$
Bash log-format-upstream: $the_real_ip - [$the_real_ip] - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id $http_x_transaction_id
$$


## **验证**

$$
Bash/etc/nginx$ grep log_format /etc/nginx/nginx.conf log_format upstreaminfo '$the_real_ip - [$the_real_ip] - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id $http_x_transaction_id'; log_format log_stream [$time_local] $protocol $status $bytes_sent $bytes_received $session_time;
$$

增加后会自动生效，直接去容器里面确认nginx配置即可

发送一个请求并查看ingress日志
$$
Bash~]$ curl -H 'host:nginx.example.com' -H 'x-transaction-id:1234567890' http://10.92.197.0/~]$ docker logs --tail 1 2ac10.92.197.0 - [10.92.197.0] - - [11/Jan/2023:05:43:36 +0000] "GET / HTTP/1.1" 302 29 "-" "curl/7.29.0" 109 0.001 [test-grafana-3000] 192.168.252.65:3000 29 0.002 302 cfe18c517ef1a08f050dbdf5d1b43e24 1234567890
$$
