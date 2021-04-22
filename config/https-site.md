### HTTPS站点

随着浏览器对安全的重视，没有上 https 的站点会在被标注为不安全。首先给用户的感觉就是这个站点不靠谱，其次国内和国际的搜索引擎对 https 站点也会优先收录，做 SEO 必须要上https。

nginx 作为一个代理服务器，可以帮助我们卸载 SSL（可以理解为卸载 https），让用户到 nginx 之间采用安全的 https 连接，nginx到后端服务采用 http 连接。当然也可以 nginx 到后端服务也采用https。

#### 单证书

一般情况下，我们没有特殊要求只需要部署一张证书即可。

RSA证书：

```
server {
    listen       443 ssl;
    server_name  example.com;
    
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_certificate     /usr/local/nginx/conf/cert-rsa.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert-rsa.key;
    
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

ECDSA证书：

```
server {
    listen       443 ssl;
    server_name  example.com;
    
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_certificate     /usr/local/nginx/conf/cert-ecc.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert-ecc.key;
    
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

这里已经不推荐协议中使用 TLSv1 和 TLSv1.1 了，安全性保障。上面的 ciphers 协议套件不知道怎么修改的可以参考 https://github.com/cloudflare/sslconfig/blob/master/conf。

#### 双证书

首先要问为什么配置双证书？在 RSA 和 ECC 同一安全性的情况下：

1. RSA 运算速度慢，消耗资源高，但由于历史原因出现时间早，兼容性好（CA 组链）
2. ECC 运算速度快，消耗资源少，比较适合最求性能的服务
3. 国密证书的支持

为了做到非常好的兼容性，一些大厂一般会部署双证书，在客户端支持 ECC 证书的情况优先使用 ECC 证书，不支持则使用 RSA 证书。这样既保证了性能又保证了兼容性。现在有些厂商为了支持国密证书，可能已经上了三证书部署，这里不讨论。

```
server {
    listen       443 ssl;
    server_name  example.com;
    
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    
    # rsa证书
    ssl_certificate     /usr/local/nginx/conf/cert-rsa.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert-rsa.key;
    
    # ecc证书
    ssl_certificate     /usr/local/nginx/conf/cert-ecc.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert-ecc.key;
    
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

即使部署上了 SSL 证书，我们也有很多可以优化的点，这里不做过多说明。

#### 重定向

nginx 重定向 http 到 https：

```
# HTTP redirect
server {
    listen      80;
    server_name example.com;

    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    # 如何nginx版本支持 http2：listen 443 ssl http2;
    listen       443 ssl;
    server_name  example.com;
    
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_certificate     /usr/local/nginx/conf/cert-rsa.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert-rsa.key;
    
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
}
```

更多配置参数参考：[ssl 如何配置](config/ssl-conf.md)。





