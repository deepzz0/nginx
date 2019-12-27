# 配置概念

非常喜欢 nginx 中采用的块状配置的形式。这样可以让用户更加清晰的管理配置。

`master` 进程启动时会首先读取并验证配置 `nginx.conf` 及扩展配置文件，然后以只读形式由 `worker` 进行相关配置实施。nginx 的配置遵循一下规则：

* 遵循 `C` 语言风格，配置需以 `;` 结尾

* 以 `#` 开头被视为注释

* 每条具体的配置由指令和参数构成: `server_name example.com;`

* 每行配置是一个字符串，可以用 `'` 或 `"` 括起来，也可以不用。但是如果配置指令包含空格，一定要引起来。

  ```
  log_format main '$remote_addr - remote_user';
  ```

下面这种形式就是抽象出来的块状配置，块与块之间是包含的关系，也就形成了上下文（Context）：

```
├── events
│   ├── multi_accept
│   └── worker_connections
├── http
│   ├── charset
│   ├── client_max_body_size
│   ├── include
│   ├── resolver
│   └── server
│       ├── listen
│       ├── location
│       │   ├── add_header
│       │   ├── proxy_pass
│       │   └── return
│       └── server_name
├── mail
├── pid
├── stream
├── user
├── worker_processes
└── worker_rlimit_nofile
```

当我们去看 nginx 文档时，就会有提示可以配置上下文的位置：

![read-config](asserts/read-config.jpg)



一份默认 `nginx.conf` 配置：

```
$ cat /etc/nginx/nginx.conf

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```



### 大小单位

文件大小单位可以指定 bytes，kilobytes（以 k 或 K 结尾）或者 megabytes（以 m 或 M 结尾），如：

```
1024  -> 1024 bytes
8     -> 8 kilobytes
1m    -> 1 megabytes
```

偏移量也可以使用 g 或 G 来代表 gigabytes。



### 时间单位

时间单位可以指定 milliseconds（毫秒），seconds（秒），minutes（分钟），hours（小时），days（天）等等，具体如下：

```
ms -> milliseconds
s  -> seconds
m  -> minutes
h  -> hours
d  -> days
w  -> weeks
M  -> months, 30 days
y  -> years, 365 days
```

单位也可以进行组合，但需要从大 -> 小的方式，如果某个数字没有以单位结尾，将视作秒 s：

```
# 一下三种表示是相同的时间
1h 30m = 90m = 5400s

# 不以单位结尾，代表秒
500 -> 500 seconds
```

不过官方建议，我们填写时间的时候最好指定单位后缀。

