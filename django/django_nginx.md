## django + nginx + supervisor

### 1 uwsgi部署及配置

安装uwsgi

```
pip install uwsgi
```

uwsgi配置文件

```
; uwsgi.ini
[uwsgi]
; 通过HTTP访问的接口
http = :8080
; 通过unix访问
socket = /tmp/site.sock
; django项目的目录
chdir = /path/to/project
; 虚拟环境目录
home = /path/to/virtualenv
; django项目的wsgi文件
wsgi-file = wsgi.py
; 进程数
processes = 2
; 每个进程的线程数
threads = 4
; 当服务器退出时自动清理环境，删除unix socket文件和pid文件
vacuum = true
; 是否作为守护进程，如果要用supervisor管理的话，可以不要该配置
daemonize = true

; 设置socket文件的权限和属主
chmod-socket = 666
uid = nginx
gid = nginx
```

测试：

```
uwsgi --init uwsgi.ini
```

然后可以在前台访问下8080端口

### 2 supervisor

```
[program:uwsgi]
command=uwsgi --ini uwsgi.ini
directory=/path/to/project
stdout_logfile=/path/to/logfile
autorestart=true
; 错误日志是否重定向到stdout日志文件
redirect_stderr=true
; 如果被管理的进程还会产生子进程则需要该配置
stopasgroup=true
```

### 3 nginx配置

```
server {
    listen         80;
    server_name    127.0.0.1
    access_log      /var/log/nginx/access.log;
    error_log       /var/log/nginx/error.log;

    location / {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:8080;
    }   
    location /static {
        expires 30d;
        autoindex on;
        alias /data/www/static/;
     }
 }
```

```
log_format myformat    '$status|$scheme://$http_host|$remote_addr|$upstream_addr|nginxProxy|$uri|$time_local|$request_time|$http_Tencent_LeakScan|$arg_offer_id|url="$request",body="$request_body"|s=$bytes_sent|"$http_user_agent"';
```

