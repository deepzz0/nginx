#### 重定向

web开发最常见的重定向HTTP code应该是：

* 301，永久重定向
* 302，临时重定向

针对这样的重定向 nginx 中有几种配置方式。如果有 nginx 内部文件重定向可参考：[静态文件服务器](config/file-svr.md#try_files)。



http服务器通过返回的 http code 指示浏览器这是一个重定向响应，同时服务器会在返回的响应头里加上：

```
Location: xxx
```

浏览器通过读取 location 进行二次访问，完成请求。

#### 原理

```
# 可选 flag
# last 停止当前进程，重新搜索新匹配的 location
# break 停止当前进程
# redirect 302重定向
# permanent 301重定向
Syntax:	rewrite regex replacement [flag];
Default:	—
Context:	server, location, if

Syntax:	return code [text];
return code URL;
return URL;
Default:	—
Context:	server, location, if
```



#### 配置

例一，http 重定向到 https，采用 301：

```
server {
    listen      80;
    server_name example.com;
    location / {
      # permanent: 301, redirect: 302
      rewrite ^/(.*)$ https://$host/$1 permanent;
    }
}

server {
  listen      80;
  server_name example.com;
  return      301 https://$host$request_uri;
}
```



例二，针对特定浏览器走不通版本：

```
server {
  listen      80;
  server_name example.com;
  
  if ($http_user_agent ~ MSIE) {
    rewrite ^(.*)$ /msie/$1 break;
  }
}
```



例三，url重定向：

```
server {
  listen      80;
  server_name example.com;
  
  location /download/ {
    if ($forbidden) {
      return 403;
    }
    if ($slow) {
      limit_rate 10k;
    }
    rewrite ^/(download/.*)/media/(.*)\..*$ /$1/mp3/$2.mp3 break;
  }
}
```

