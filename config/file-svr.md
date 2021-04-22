### 静态文件服务

静态文件服务是我们最常用到的形式。

它有三种配置方式：root、alias、try_files。

#### root

该方式需要指定一个 root 文件根目录，后续访问这个服务时文件均在这个根目录查找，如：

```
server {
    listen       80;
    server_name  example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件，
    }
    
    location /i/ {
        root   /data/w3;
    }
}
```

##### 知识点

当我们对 `example.com` 进行访问时，nginx 会帮助我们在 `/usr/share/nginx/html` 这个根目录下寻找文件。如果找到返回文件内容，未找到返回 404。

而 `index` 的目的是指示 nginx 在我们没有指定文件的时候查找的文件，也就是当我们访问 `example.com/` 的时候展示的文件内容，可以指定多个（按顺序查找）。

例，1、当访问 `example.com/test/hello.txt` 时，查找的文件路径是什么？

> 答：/usr/share/nginx/html/test/hello.txt

2、当访问 `example.com/i/top.gif` 时，查找的文件路径是什么？

> 答案：/data/w3/i/top.gif

#### alias

root 和 alias 是在 nginx 配置中很容易混淆的，顾名思义 alias 就是别名，通过替换方式访问文件：

```
server {
    listen       80;
    server_name  example.com;
    
    location /i/ {
        alias /data/w3/images/;
    }

    location ~ ^/users/(.+\.(?:gif|jpe?g|png))$ {
        alias /data/w3/images/$1;
    }

    location /images/ {
        root /data/w3;
    }
}
```

##### 知识点

通常来说，alias 是因为我们要去改变 URI 的时候才会使用，根据 root 的规则不能满足我们需求时考虑。

例，1、当访问 `example.com/i/top.gif` 时，查找的文件路径是什么？

> 答：/data/w3/images/top.gif，注意这里 alias 最后以斜杠 / 结尾。

2、当访问 `example.com/users/avatar/top.gif` 时，查找的文件路径是什么？

> 答：/data/w3/images/test/avatar.jpg，这个是 location 的特性后面会说到

3、当访问 `example.com/images/top.gif` 时，查找的文件路径是什么？

> 答：/data/w3/images/top.gif，这种方式和 root 效果一样

推荐，实际使用的过程中，应该优先使用 root，其次在 root 满足不了的情况下使用 alias。

#### try_files

检查指定顺序的文件是否存在，使用第一个找到的文件进行响应。该参数需要和 root 或 alias 进行结合使用，`try_files` 查找的文件会根据这两个指定的地址进行相对路径访问。

文件的路径可以根据指令和参数来指定，同时可以在名称末尾指定斜杠来检查目录是否存在，如 `$uri`。如果没有找到任何文件，则对最后一个参数中指定的uri进行内部重定向：

```
server {
    listen       80;
    server_name  example.com;

    root /data/w3;
    
    location /images/ {
        try_files $uri/ /images/default.gif;
    }
    location = /images/default.gif {
        expires 30s;
    }
    
    location /index {
        try_files $uri $uri/index.html $uri.html =404;
    }
    
    location / {
        try_files /system/maintenance.html
                  $uri $uri/index.html $uri.html
                  @mongrel;
    }
    location @mongrel {
        proxy_pass http://mongrel;
    }
}
```

##### 知识点

try_files 是在不确定文件是否存在的时候的一种配置方法，比如某人的头像，如果没上传就使用默认头像。

例，1、尝试查找文件，如果文件都不存在会对最后一个参数中指定的 uri 进行内部重定向（访问者无感知）。当访问 `example.com/images/top.gif`，尝试的路径分别是：

```
/data/w3/images/top.gif
/data/w3/images/default.gif
```

如果都不存在，就会内部重定向到 `location = /images/default.gif`。这里可以做一些操作。

2、指定 code，当所有文件都未找到则返回指定的 code 码

3、结合代理使用，当 `try_files` 所有文件都未找到，可通过 `@mongrel` 这种形式代理到其它服务获取。



更多的 location 规则技巧，移步：[location如何配置](config/location-conf.md)。

