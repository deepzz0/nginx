### 前端跨域

现在大多数环境下均是前后端分离开发的情况，首要解决的问题就是跨域的问题。目前跨域有两种解决方式：

1. 通过后端 API 返回允许跨域请求头

   ```
   Access-Control-Allow-Origin
   Access-Control-Allow-Headers
   Access-Control-Allow-Methods
   Access-Control-Allow-Credentials
   ```

2. 通过代理的方式（正向或反向代理）

#### 原理

这里介绍通过 nginx 代理方式解决跨域。其根本目的是将前端服务和后端服务放置在同一域名下访问。



#### 配置

```
server {
  listen       80 default_server;
  server_name  _;
  
  # 前端代理
  location / {
    proxy_pass 192.168.1.112:3000;
  }
  
  # 后端代理
  location /api {
    proxy_ignore_headers         Set-Cookie;
    proxy_hide_header            Vary;
    proxy_set_header             Connection        "";
    proxy_set_header             Host              $host;
    proxy_set_header             X-Real-IP         $remote_addr;
    proxy_set_header             X-Forwarded-Proto $scheme;
    proxy_set_header             X-Forwarded-For   $proxy_add_x_forwarded_for;
    
    proxy_pass                   http://192.168.1.113:9000;
  }
}
```

其中：

* `proxy_pass 192.168.1.112:3000;` 代理到你本地开发机器的IP地址。
* `proxy_pass http://192.168.1.113:9000;` 代理到后端服务机器的IP地址。

然后通过访问 nginx 所在的 IP 就可以愉快的玩耍了。