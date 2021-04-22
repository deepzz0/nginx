### 多虚拟主机

我们都知道一台服务器只有一个端口能够对外提供 [0-65535]，对外提供标准web服务的也就一个 80 和一个 443 端口。

但实际上，我们可能会有多个站点需要对外服务，如果一个站点买一台服务器未免有点奢侈，多虚拟主机帮你解决这个问题。

#### 原理

首先我们需要在 nginx 的配置中配置虚拟主机，nginx 会帮助我们缓存这些信息，当流量进入到 nginx：

* HTTP 协议：nginx 通过解析 HTTP Header 的形式获取到需要访问的主机 host，然后匹配本地的配置，最后将流量代理到相应的虚拟主机。
* HTTPS 协议：因为流量是加密的，不能解析到 HTTP Header，此时 SNI 技术起到了作用，后续通过匹配配置，将流量代理到相应的虚拟主机。

```
server {
    listen       80 default_server;
    server_name  example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}

server {
    listen       80;
    server_name  a.example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}

server {
    listen       80;
    server_name  example.net;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

该配置同时支持了三个 Host，且同时占用 80 端口：

```
example.com
a.example.com
example.net
```

当你通过不同的域名访问的时候，nginx 会自动帮你代理到不同的主机，实现多虚拟主机的目的。

> 注意：`default_server`，当你所访问的域名没有在 nginx 中配置的话会使用默认虚拟主机。

