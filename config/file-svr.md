### 静态文件服务

作为使用nginx的第一份配置。

在使用nginx的过程中，我们可以通过如下方式启动静态文件服务：

#### Root

该方式是指定一个根目录，通过url路径来进行文件和文件夹的访问，如：

```
server {
    listen       80;
    server_name  example.com;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm; # 默认文件
    }
    
    location /i/ {
        root   /data/w3;
    }
}
```

稍微解释下：

* 当直接访问 `example.com` 时，nginx会帮我们依次查找index指定的文件。
* 当访问 `example.com/hello.txt` 时，nginx会帮我们在 `/usr/share/nginx/html` 找 `hello.txt` 文件。如果找到返回文件内容，未找到就是404。
* 同理，当访问 `example.com/test/hello.txt` 时，nginx会先查找到 `test` 文件夹，然后在文件夹里查找 `hello.txt` 文件。

思考：

那么当访问 `example.com/i/top.gif` 的时候实际上是去访问哪个文件呢？

> 答案：/data/w3/i/top.gif

#### Alias

很多人会在 root 和 alias 之间犹豫，因为很模糊两个概念。顾名思义 alias 就是别名，通过替换方式访问文件：

```
server {
    listen       80;
    server_name  example.com;
    
    # 1. 如访问 example.com/i/top.gif
    #    则查找文件 /data/w3/images/top.gif
    location /i/ {
        alias /data/w3/images/;
    }
    # 2. 正则匹配，将路径中的参数提取出来
    location ~ ^/users/(.+\.(?:gif|jpe?g|png))$ {
        alias /data/w3/images/$1;
    }
    # 3. 和root效果一样，如访问 example.com/images/top.gif
    #    则查找文件 /data/w3/images/top.gif
    location /images/ {
        root /data/w3;
    }
}
```

在我们实际使用的过程中，应该优先使用 root，其次在 root 满足不了的情况下使用 alias。

思考：

上面的 alias 换成 root 实际上会去访问什么文件？

#### Try_files

检查指定顺序的文件是否存在，使用第一个找到的文件进行响应。文件的路径可以根据指令和参数来指定，同时可以在名称末尾指定斜杠来检查目录是否存在，如 `$uri`。如果没有找到任何文件，则对最后一个参数中指定的uri进行内部重定向

```
server {
    listen       80;
    server_name  example.com;
    
    # 1. 如果未找到文件，对最后一个参数中指定的uri进行重定向
    location /images/ {
        try_files $uri /images/default.gif;
    }
    location = /images/default.gif {
        expires 30s;
    }
    
    # 2. 可以指定code，当所有文件都未找到则返回code码
    location /index {
        try_files $uri $uri/index.html $uri.html =404;
    }
    
    # 3. 可以结合代理，通过代理重定向
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

更多的 location 规则技巧，移步：[location如何配置](config/location-conf.md)。

