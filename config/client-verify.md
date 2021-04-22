### 客户端认证

客户端证书认证，在我们访问 https 网站的时候涵盖了服务器证书认证的过程。但在某些场景下，为了保证安全又不想通过其它方式进行认证（如账户密码形式），那么你可以采用客户端认证的方式。

该方式由服务器开启，在客户端进行访问的时候发起一个证书认证的挑战：需要客户端（浏览器）提供证书，加解密等（密码学原理）。然后由服务器进行认证。认证通过才能够进行访问，也就是常说的 SSL 双向认证。

#### 配置

```
server {
  listen       443 ssl;
  server_name  example.com;
 
  ssl_certificate      /usr/local/nginx/conf/cert-ecc.pem;
  ssl_certificate_key  /usr/local/nginx/conf/cert-ecc.key;
  # 开启客户端证书认证
  ssl_verify_client on;
  ssl_verify_depth  2;
  ssl_client_certificate /usr/local/nginx/conf/client-ca.pem;

  location / {
    root   html;
    index  index.html index.htm;
  }
}
```

配置很简单，较普通的 https 站点配置外，增加了：

```
ssl_verify_client on;
ssl_verify_depth  2;
ssl_client_certificate /usr/local/nginx/conf/client-ca.pem;
```

1、`ssl_verify_client on` 开启客户端证书认证。

2、`ssl_verify_depth` 验证深度，验证过程是一个组链过程，如果不能组成功即验证失败：

>  如可信客户端证书 A，是由中间 CA 证书 B 签发，证书 B 又由根证书 C 签发，那么深度为 2：A -> B -> C。
>
> 自签名证书如果也有层级关系参考上方，否则默认为 1 即可。

3、`ssl_client_certificate` 客户端 CA 证书，用来验证客户端证书。如果客户端证书为可信证书可不填（因为已经内置）。

#### 证书安装

除了配置服务器之外，还需要我们客户端 OS 安装客户端证书，否则无法认证。

不同系统安装证书的过程不同，这里拿 MacOS 简单举例：

1、将我们获取到的 PEM 证书转换为 pfx 格式：

* 方式一：https://myssl.com/cert_convert_wasm.html
* 方式二：openssl pkcs12 -export -out test.pfx -inkey test.key -in test.crt
* 方式三：https://keymanager.org/

2、双击安装，导入到系统

3、访问认证服务站点

具体的安装方式可以百度或谷歌，这里不做过多说明。

