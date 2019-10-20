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

#### Source Code
```c
// $ sed -n "194,386p" nginx.c 
int ngx_cdecl // ngx_cdecl: 为了跨平台预留的宏定义
main(int argc, char *const *argv) // 启动入口
{
    ngx_buf_t        *b;
    ngx_log_t        *log;
    ngx_uint_t        i;
    ngx_cycle_t      *cycle, init_cycle;
    ngx_conf_dump_t  *cd;
    ngx_core_conf_t  *ccf;

    ngx_debug_init();

    if (ngx_strerror_init() != NGX_OK) {
        return 1;
    }

    if (ngx_get_options(argc, argv) != NGX_OK) {
        return 1;
    }

    if (ngx_show_version) {
        ngx_show_version_info();

        if (!ngx_test_config) {
            return 0;
        }
    }

    /* TODO */ ngx_max_sockets = -1;

    ngx_time_init();

#if (NGX_PCRE)
    ngx_regex_init();
#endif

    ngx_pid = ngx_getpid();
    ngx_parent = ngx_getppid();

    log = ngx_log_init(ngx_prefix);
    if (log == NULL) {
        return 1;
    }

    /* STUB */
#if (NGX_OPENSSL)
    ngx_ssl_init(log);
#endif

    /*
     * init_cycle->log is required for signal handlers and
     * ngx_process_options()
     */

    ngx_memzero(&init_cycle, sizeof(ngx_cycle_t));
    init_cycle.log = log;
    ngx_cycle = &init_cycle;

    init_cycle.pool = ngx_create_pool(1024, log);
    if (init_cycle.pool == NULL) {
        return 1;
    }

    if (ngx_save_argv(&init_cycle, argc, argv) != NGX_OK) {
        return 1;
    }

    if (ngx_process_options(&init_cycle) != NGX_OK) {
        return 1;
    }

    if (ngx_os_init(log) != NGX_OK) {
        return 1;
    }

    /*
     * ngx_crc32_table_init() requires ngx_cacheline_size set in ngx_os_init()
     */

    if (ngx_crc32_table_init() != NGX_OK) {
        return 1;
    }

    /*
     * ngx_slab_sizes_init() requires ngx_pagesize set in ngx_os_init()
     */

    ngx_slab_sizes_init();

    if (ngx_add_inherited_sockets(&init_cycle) != NGX_OK) {
        return 1;
    }

    if (ngx_preinit_modules() != NGX_OK) {
        return 1;
    }

    cycle = ngx_init_cycle(&init_cycle);
    if (cycle == NULL) {
        if (ngx_test_config) {
            ngx_log_stderr(0, "configuration file %s test failed",
                           init_cycle.conf_file.data);
        }

        return 1;
    }

    if (ngx_test_config) {
        if (!ngx_quiet_mode) {
            ngx_log_stderr(0, "configuration file %s test is successful",
                           cycle->conf_file.data);
        }

        if (ngx_dump_config) {
            cd = cycle->config_dump.elts;

            for (i = 0; i < cycle->config_dump.nelts; i++) {

                ngx_write_stdout("# configuration file ");
                (void) ngx_write_fd(ngx_stdout, cd[i].name.data,
                                    cd[i].name.len);
                ngx_write_stdout(":" NGX_LINEFEED);

                b = cd[i].buffer;

                (void) ngx_write_fd(ngx_stdout, b->pos, b->last - b->pos);
                ngx_write_stdout(NGX_LINEFEED);
            }
        }

        return 0;
    }

    if (ngx_signal) {
        return ngx_signal_process(cycle, ngx_signal);
    }

    ngx_os_status(cycle->log);

    ngx_cycle = cycle;

    ccf = (ngx_core_conf_t *) ngx_get_conf(cycle->conf_ctx, ngx_core_module);

    if (ccf->master && ngx_process == NGX_PROCESS_SINGLE) {
        ngx_process = NGX_PROCESS_MASTER;
    }

#if !(NGX_WIN32)

    if (ngx_init_signals(cycle->log) != NGX_OK) {
        return 1;
    }

    if (!ngx_inherited && ccf->daemon) {
        if (ngx_daemon(cycle->log) != NGX_OK) {
            return 1;
        }

        ngx_daemonized = 1;
    }

    if (ngx_inherited) {
        ngx_daemonized = 1;
    }

#endif

    if (ngx_create_pidfile(&ccf->pid, cycle->log) != NGX_OK) {
        return 1;
    }

    if (ngx_log_redirect_stderr(cycle) != NGX_OK) {
        return 1;
    }

    if (log->file->fd != ngx_stderr) {
        if (ngx_close_file(log->file->fd) == NGX_FILE_ERROR) {
            ngx_log_error(NGX_LOG_ALERT, cycle->log, ngx_errno,
                          ngx_close_file_n " built-in log failed");
        }
    }

    ngx_use_stderr = 0;

    if (ngx_process == NGX_PROCESS_SINGLE) {
        ngx_single_process_cycle(cycle);

    } else {
        ngx_master_process_cycle(cycle);
    }

    return 0;
}
```