#### Overview
- nginx has one master process and several worker processes. The main purpose of the master process is to read and evaluate configuration, and maintain worker processes. Worker processes do actual processing of requests. nginx employs event-based model and OS-dependent mechanisms to efficiently distribute requests among worker processes. 采用事件回调机制，master进程负责管理，worker进程负责处理请求。

#### Configuration [http://nginx.org/en/docs/dirindex.html]
- directive(指令): A simple directive consists of the name and parameters separated by spaces and ends with a semicolon (;).
- context(上下文): If a block directive can have other directives inside braces, it is called a context (examples: events, http, server, and location).


```css
#------------------------nginx_demo.conf------------------------

user  testuser testgroup; #进程启动用户
worker_processes  3; #工作进程数
worker_rlimit_nofile 65535; #每个worker打开文件描述符的最大值

error_log  logs/error.log  error; #设置错误日志的路径和最低级别error

pid        logs/nginx.pid; #记录master进程的PID


events { #连接配置上下文
    worker_connections  1024; #每个worker的最大并发连接数
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #日志格式，设为main
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $request_time "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main; # 设置访问日志的路径与格式

    sendfile        on;
    #tcp_nopush     on;

    #a timeout during which a keep-alive client connection will stay open on the server side.
    keepalive_timeout  65;
    #a timeout for establishing a connection with a proxied server.
    proxy_connect_timeout 10;
    #If the proxied server does not transmit anything within this time, the connection is closed.
    proxy_read_timeout 300;
    #If the proxied server does not receive anything within this time, the connection is closed.
    proxy_send_timeout 60;
    #If a client does not transmit the entire header within this time, the request is terminated with the 408 (Request Time-out) error.
    client_header_timeout 60;
    #a timeout for reading client request body
    client_body_timeout 60;
    #If the size in a request(Content-Length) exceeds the configured value, the 413 (Request Entity Too Large) error is returned to the client.
    client_max_body_size 2M;

    #gzip  on; #是否启用压缩功能

    upstream test { #软负载均衡上下文
        ip_hash; #Session persistence based on client's IP
        # round-robin(默认)：轮询
        # least_conn：最小连接数者优先分配
        # ip_hash：根据调用方IP哈希分配
        server 10.10.20.101:9524;
        server 10.10.20.102:9525 max_fails=0;
        server 8.7.161.35:9526;
        keepalive 10; #每个worker的最大空闲连接数
    }

    server {
        listen       8080; #监听端口
        server_name  localhost; #域名

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /rest { #设置转发匹配的URI前缀
            root   /opt/service/apps; #设置根目录
            index  index.html index.htm; #设置默认主页
        }

        # redirect server error pages to the static page /50x.html
        error_page  404              /404.html;
        error_page  500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy
        location /api {
            proxy_pass   http://10.10.20.121;
        }
    }

    # HTTPS server
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*; #引用外部的.conf配置文件
}
```

#### 运维
- nginx -s stop/quit/reload/reopen 通过信号控制状态
- ps -afjx | grep nginx 通过父子进程查看master和worker的关系
