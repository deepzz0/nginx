### Basic Auth

在实际工作中，有时会遇到各种权限问题，通过 Basic Auth 是简单的方式。Basic Auth全称HTTP Basic Authentication，是最常见的认证方案（用户名+口令）。它的步骤是：

1. 将用户名和口令通过`:` 拼接成字符串s：username:password
2. 将拼接好的字符串s进行base64得到auth：base64(s)
3. 将得到的auth通过http header发送到服务端进行认证：Authorization: Basic auth

即 `Authorization: Basic Base64(username:password)`。



#### 配置

nginx 包含两个相关的参数：`auth_basic` 与 `auth_basic_user_file` （认证端）。

```
# auth_basic 有一个特殊值 off，取消从上一个级别继承的 auth_basic 指令
Syntax:	auth_basic string | off;
Default:	
auth_basic off;
Context:	http, server, location, limit_except

Syntax:	auth_basic_user_file file;
Default:	—
Context:	http, server, location, limit_except
```



例一，配置认证访问后端需要 Basic Auth 认证的能力：

```
server {
  listen       80 default_server;
  server_name  _;
  
  location /api {
    # base64(username:password)
    add_header Authorization "Basic dXNlcm5hbWU6cGFzc3dvcmQ=";
    
    proxy_pass http://192.168.1.113:9000;
  }
}
```



例二，通过 `auth_basic_user_file` 要求 Basic Auth 认证：

```
# 在我们不想直接暴露我们服务或者某个API的情况下，加上 Basic Auth 认证非常方便
server {
  listen       80 default_server;
  server_name  _;
  
  # 开启basic auth认证
  auth_basic "Administrator’s Area";
  # 用户认证数据
  auth_basic_user_file /path/htpasswd;
  location /api {
    proxy_pass http://192.168.1.113:9000;
  }
}
```

文件 `/path/htpasswd` 文件内容格式可以为：

1. 明文格式：

   ```
   # comment
   name1:password1
   name2:password2:comment
   name3:password3
   ```

2. 通过 `htpasswd` 或 `openssl passwd` 加密生成的格式。