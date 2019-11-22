# Nginx 安装

### 直接安装

对于 Linux 系统，一般的，如果没有特殊需求，每个系统都管理着 nginx 相关 package，你只需要执行：

```
# RHEL/CentOS
$ yum install -y nginx

# Debian
$ apt update && apt install -y nginx

# Ubuntu
$ apt update && apt install -y nginx

# Alpine
$ apk add nginx
```

### 指定安装

如果你需要安装指定版本，可以通过 [https://nginx.org/packages/](https://nginx.org/packages/) 找到对应平台的 package，方法如下：

1、查看自己服务器操作系统版本系统：

```
$ cat /etc/os-release
ID="centos"             # 操作系统
VERSION_ID="8"          # 系统版本

$ uname -a
x86_64                  # 系统架构
```

2、确定服务器操作系统所能安装的版本：

```
# RHEL/CentOS
Version	            Supported Platforms
6.x                 x86_64, i386
7.4+                x86_64, ppc64le
8.x                 x86_64

# Debian
Version	            Supported Platforms
9.x “stretch”       x86_64, i386
10.x “buster”       x86_64, i386

# Ubuntu
Version	            Supported Platforms
16.04 “xenial”      x86_64, i386, ppc64el, aarch64/arm64
18.04 “bionic”      x86_64, aarch64/arm64
18.10 “cosmic”      x86_64
19.04 “disco”       x86_64

# Alpine
Version	           Supported platforms
3.8                x86_64
3.9                x86_64
3.10               x86_64
```

3、在 [https://nginx.org/packages/](https://nginx.org/packages/) 找到所对应的 package：

如 `Centos 7 x86_64` 的 rpm 地址是：[https://nginx.org/packages/centos/7/x86_64/RPMS/ ](https://nginx.org/packages/centos/7/x86_64/RPMS/)。



然后确定想要的版本，如：[https://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.16.1-1.el7.ngx.x86_64.rpm](https://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.16.1-1.el7.ngx.x86_64.rpm)。

4、进行安装

```
# RHEL/CentOS
# 如 https://nginx.org/packages/centos/7/x86_64/RPMS/nginx-1.16.1-1.el7.ngx.x86_64.rpm
$ yum install -y <xxxxxx.rpm>

# Debian
# 如 https://nginx.org/packages/debian/pool/nginx/n/nginx/nginx_1.14.0-1~stretch_amd64.deb
$ wget <xxxxxx.deb>
$ dpkg -i <nginx_xxx.deb>

# Ubuntu
# 如 https://nginx.org/packages/ubuntu/pool/nginx/n/nginx/nginx_1.14.2-1~bionic_amd64.deb
$ wget <xxxxx.deb>
$ dpkg -i <nginx_xxx.deb>

# Alpine
# 如 https://nginx.org/packages/alpine/v3.9/main/x86_64/nginx-1.16.1-r1.apk
$ wget <xxxxxx.apk>
$ apk add --allow-untrusted <nginx-xxx.apk>
```



### 源码安装

源码安装有以下步骤：

1. 下载源码文件 [https://github.com/nginx/nginx/releases](https://github.com/nginx/nginx/releases)
2. 执行 `$ configure ...`，成功后会生成一个 `Makefile` 文件
3. 执行 `$ make`

相关参数：

* `--help`，打印帮助消息
* `--prefix=path`，默认 `/usr/local/nginx`，保存服务器文件的目录，这个目录也将用于执行 `$ ./configure` 命令时和 `nginx.conf` 配置文件中的相对目录。
* `--sbin-path=path`，默认 `prefix/sbin/nginx`，设置可执行文件的名称。
* `--modules-path=path`，默认 `prefix/modules`，定义 nginx 动态模块的目录。
* `--conf-path=path`，默认 `prefix/conf/nginx.conf`，设置配置文件的名称。
* ....
* `--with-openssl=path`，设置 OpenSSL 库源码路径。
* `--with-debug`，开启 debug 日志。

更多参数请参考 [Building nginx from Sources](http://nginx.org/en/docs/configure.html)，参数使用例子：

```
$ ./configure
    --sbin-path=/usr/local/nginx/nginx
    --conf-path=/usr/local/nginx/nginx.conf
    --pid-path=/usr/local/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-8.43
    --with-zlib=../zlib-1.2.11

$ make
```

### 动态模块

默认情况 nginx 是由许多核心模块构建的，那需要实现额外的功能就需要重新构建。但版本 1.9.11 之后，nginx 支持了 [动态模块](http://nginx.org/en/docs/ngx_core_module.html#load_module)，以下是 nginx 动态构建的模块：

```
nginx-module-geoip
nginx-module-image-filter
nginx-module-njs
nginx-module-perl
nginx-module-xslt
```

它可以通过单独的包进行发布，在使用时只需 nginx 加载即可：

```
# 作用域 main
load_module modules/ngx_mail_module.so;
```
