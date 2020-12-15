随着免费 SSL 证书的出现和浏览器对非 HTTPS 网站不安全的提示，越来越多的朋友意识到需要为自己的网站上 HTTPS。

如何成功配置 HTTPS 呢？在 Nginx 中，我们实际上配置的是 SSL（套接字安全层），也就是 HTTPS 底层使用到的技术，所以要上 HTTPS 却配置 SSL 是不矛盾的。最简配置：

```
server {
	listen 443 ssl;
	server_name example.com;
	
	ssl_certificate /usr/local/nginx/conf/cert.pem;
	ssl_certificate_key /usr/local/nginx/conf/cert.key;
}
```

更多模块信息：[ngx_http_ssl_module](http://nginx.org/en/docs/http/ngx_http_ssl_module.html)。

### 内置变量

罗列该模块内置变量：

```
# 已建立连接的密码套件
$ssl_cipher
# 客户端支持的密码套件
$ssl_ciphers
# PEM格式客户端证书（urlencoded）
$ssl_client_escaped_cert
# 过时，请使用 ssl_client_escaped_cert
$ssl_client_cert
# 客户端证书SHA1指纹
$ssl_client_fingerprint
# 客户端证书的issuer DN，符合 RFC2253
$ssl_client_i_dn
# 客户端证书的issuer DN，如 Issuer: C=US, ST=Arizona, L=Scottsdale, O=GoDaddy.com, Inc., OU=http://certs.godaddy.com/repository/, CN=Go Daddy Secure Certificate Authority - G2
$ssl_client_i_dn_legacy
# PEM格式客户端证书（原始数据）
$ssl_client_raw_cert
# 客户端证书的subject DN，符合 RFC2253
$ssl_client_s_dn
# 客户端证书的subject DN，如 Subject: OU=Domain Control Validated, CN=*.qima-inc.com
$ssl_client_s_dn_legacy
# 客户端证书序列号
$ssl_client_serial
# 客户端证书结束时间
$ssl_client_v_end
# 客户端证书剩余天数
$ssl_client_v_remain
# 客户端证书开始时间
$ssl_client_v_start
# 客户端证书验证结果，SUCCESS｜FAILED:reason｜NONE
$ssl_client_verify
# 客户端支持的曲线
$ssl_curves
# 在TLS1.3使用 early data 情况下还未握手完成返回1，否则空
$ssl_early_data
# 当前连接使用的协议
$ssl_protocol
# SNI 使用的服务名
$ssl_server_name
# 当前连接session标识
$ssl_session_id
# 如果为 r 代表重用，否则为 .
$ssl_session_reused
```

> 说明：客户端是相对于服务器来说，有时候我们需要双向认证，需要客户端提交一张证书。

### 配置说明

我们来看一份比较完整的 SSL 配置，我们将从多方面理解如何配置 SSL：

```
server {
	listen 443 ssl;
	server_name example.com;
	
	# [可选] 指定协议，TLS1.1 TLS1.2即将被停用
	ssl_protocols TLSv1.2 TLSv1.3;
	# [可选] 指定相关加密套件，更加安全（去掉不安全的套件）
	ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
	
	# RSA证书
	ssl_certificate /usr/local/nginx/conf/domain.rsa.pem;
	ssl_certificate_key /usr/local/nginx/conf/domain.rsa.key;
	# [可选] ECC证书，计算更快，减小服务器压力
	ssl_certificate /usr/local/nginx/conf/domain.ecc.pem;
	ssl_certificate_key /usr/local/nginx/conf/domain.ecc.key;
	
	# [可选] Diffie–Hellman密钥交换参数，防止Logjam攻击
	# openssl dhparam -out dhparams.pem 2048
	# https://weakdh.org/sysadmin.html
	ssl_dhparam /usr/local/nginx/conf/ssl/dhparams.pem;
	
	# [可选] 设置ssl session，减小服务压力
	ssl_session_timeout 1d;
	ssl_session_cache shared:SSL:10m;
	ssl_session_tickets off;
	
	# [可选] OCSP装订，降低延迟
	ssl_stapling         on;
	ssl_stapling_verify  on;
}
```

