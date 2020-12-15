### 多虚拟主机

我们都知道一台服务器只有一个端口能够对外提供（0-65535），对外提供标准web服务的也就一个80和一个443端口。

但实际上，我们可能会有多个站点需要对外服务，如果一个站点买一台服务器未免有点奢侈，多虚拟主机帮你解决这个问题。

#### 原理

HTTP协议：当流量进入到nginx，nginx通过解析HTTP Header的形式获取到需要访问的主机host，然后匹配本地的配置，最后将流量代理到相应的虚拟主机。

HTTPS协议：当流量进入到nginx，因为流量是加密的，不能解析到HTTP Header，此时SNI技术起到了作用，后续通过匹配配置，将流量代理到相应的虚拟主机。



#### 配置

```
# 虚拟主机一
server {
    listen       80 default_server;
    server_name  example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}

# 虚拟主机二
server {
    listen       80;
    server_name  a.example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}

# 虚拟主机三
server {
    listen       80;
    server_name  example.net;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

该配置同时支持了三个Host，且同时占用80端口：

```
example.com
a.example.com
example.net
```

当你通过不同的域名访问的时候，nginx会自动帮你代理到不同的主机，实现多虚拟注意的目的。

> 注意虚拟主机一: `default_server`，当你所访问的域名没有在nginx中配置的话会使用默认虚拟主机。

