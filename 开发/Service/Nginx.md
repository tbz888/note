#### Overview
- nginx has one master process and several worker processes. The main purpose of the master process is to read and evaluate configuration, and maintain worker processes. Worker processes do actual processing of requests. nginx employs event-based model and OS-dependent mechanisms to efficiently distribute requests among worker processes. 采用事件回调机制，master进程负责管理，worker进程负责处理请求。

#### Configuration
- directive指示：A simple directive consists of the name and parameters separated by spaces and ends with a semicolon (;).
- context上下文：If a block directive can have other directives inside braces, it is called a context (examples: events, http, server, and location).
- http://nginx.org/en/docs/


```nginx.conf
#------------------------nginx_demo.conf------------------------

#user  nobody;
worker_processes  3; #工作进程数

error_log  logs/error.log  error; #设置错误日志的路径和最低级别error

pid        logs/nginx.pid; #记录master进程的PID


events { #连接配置上下文
    worker_connections  1024; #最大并发连接数
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

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    upstream SOP { #软负载均衡上下文
        ip_hash; #Session persistence based on client's IP
        # round-robin(默认)：轮询
        # least_conn：最小连接数者优先分配
        # ip_hash：根据调用方IP哈希分配
        server 10.10.20.101;
        server 10.10.20.102;
        server 8.7.161.35;
    }

    server {
        listen       8080; #监听端口
        server_name  localhost; #域名

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /rest { #设置转发匹配的URI前缀
            root   /var/share/manager/Deploy/pub; #设置根目录
            index  index.html index.htm; #设置默认主页
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
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
    include servers/*;
}
```

#### 运维
- nginx -s stop/quit/reload/reopen通过信号控制状态
