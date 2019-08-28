# Nginx 安装

### Linux 安装

对于 Linux 系统，一般的，如果没有特殊需求，每个系统都管理着 nginx 相关 package，你只需要如 `sudo apt update && apt install nginx` 即可。

你也可以使用来自 nginx.org 的 nginx package 进行安装：

#### RHEL/CentOS

```
Version	            Supported Platforms
6.x                 x86_64, i386
7.4+                x86_64, ppc64le
8.x                 x86_64
```

前置条件：

```
$ sudo yum install yum-utils
```

然后，创建文件 `/etc/yum.repos.d/nginx.repo` 并填入以下内容：

```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```

默认情况，repository 使用 stable 版 package，如果想使用 mainline 版需执行：

```
$ sudo yum-config-manager --enable nginx-mainline
```

最后，执行：

```
$ sudo yum install nginx
```

当提示接受 GPG 密钥时，请验证指纹是否匹配 `573B FD6B 3D8F BC64 1079 A6AB ABF5 BD82 7BD9 BF62`。

#### Debian

```
Version	            Supported Platforms
9.x “stretch”       x86_64, i386
10.x “buster”       x86_64, i386
```

前置条件：

```
$ sudo apt install curl gnupg2 ca-certificates lsb-release
```

然后，添加 nginx package 到 repository：

```
# stable 稳定版
$ echo "deb http://nginx.org/packages/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

# mainline 主线版
$ echo "deb http://nginx.org/packages/mainline/debian `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

下一步，导入 nginx 官方签名密钥，以便 apt 验证 package 的真实性：

```
$ curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
```

验证是否拥有正确的密钥：

```
$ sudo apt-key fingerprint ABF5BD827BD9BF62
pub   rsa2048 2011-08-19 [SC] [expires: 2024-06-14]
      573B FD6B 3D8F BC64 1079  A6AB ABF5 BD82 7BD9 BF62
uid   [ unknown] nginx signing key <signing-key@nginx.com>
```

最后，执行：

```
$ sudo apt update
$ sudo apt install nginx
```

#### Ubuntu

```
Version	            Supported Platforms
16.04 “xenial”      x86_64, i386, ppc64el, aarch64/arm64
18.04 “bionic”      x86_64, aarch64/arm64
18.10 “cosmic”      x86_64
19.04 “disco”       x86_64
```

前置条件：

```
$ sudo apt install curl gnupg2 ca-certificates lsb-release
```

然后，添加 nginx package 到 repository：

```
# stable 稳定版
$ echo "deb http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

# mainline 主线版
$ echo "deb http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

下一步，导入 nginx 官方签名密钥，以便 apt 验证 package 的真实性：

```
$ curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
```

验证是否拥有正确的密钥：

```
$ sudo apt-key fingerprint ABF5BD827BD9BF62
pub   rsa2048 2011-08-19 [SC] [expires: 2024-06-14]
      573B FD6B 3D8F BC64 1079  A6AB ABF5 BD82 7BD9 BF62
uid   [ unknown] nginx signing key <signing-key@nginx.com>
```

最后，执行：

```
$ sudo apt update
$ sudo apt install nginx
```

#### SLES

```
Version	            Supported Platforms
12                  x86_64
15                  x86_64
```

前置条件：

```
$ sudo zypper install curl ca-certificates gpg2
```

然后，添加 nginx package 到 repository：

```
# stable 稳定版
$ sudo zypper addrepo --gpgcheck --type yum --refresh --check \
    'http://nginx.org/packages/sles/$releasever' nginx-stable

# mainline 主线版
$ sudo zypper addrepo --gpgcheck --type yum --refresh --check \
    'http://nginx.org/packages/mainline/sles/$releasever' nginx-mainline
```

下一步，导入 nginx 官方签名密钥，以便 zypper/rpm 验证 package 的真实性：

```
$ curl -o /tmp/nginx_signing.key https://nginx.org/keys/nginx_signing.key
```

验证是否拥有正确的密钥：

```
$ gpg --with-fingerprint /tmp/nginx_signing.key
pub  2048R/7BD9BF62 2011-08-19 [expires: 2024-06-14]
      Key fingerprint = 573B FD6B 3D8F BC64 1079  A6AB ABF5 BD82 7BD9 BF62
uid nginx signing key <signing-key@nginx.com>
```

最后，导入该密钥到 rpm 数据库：

```
$ sudo rpmkeys --import /tmp/nginx_signing.key
```

安装 nginx：

```
$ sudo zypper install nginx
```

#### Alpine

```
Version	           Supported platforms
3.8                x86_64
3.9                x86_64
3.10               x86_64
```

前置条件：

```
$ sudo apk add openssl curl ca-certificates
```

然后，添加 nginx package 到 repository：

```
# stable 稳定版
$ printf "%s%s%s\n" \
    "http://nginx.org/packages/alpine/v" \
    `egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release` \
    "/main" \
    | sudo tee -a /etc/apk/repositories

# mainline 主线版
$ printf "%s%s%s\n" \
    "http://nginx.org/packages/mainline/alpine/v" \
    `egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release` \
    "/main" \
    | sudo tee -a /etc/apk/repositories
```

下一步，导入 nginx 官方签名密钥，以便 apk 验证 package 的真实性：

```
$ curl -o /tmp/nginx_signing.rsa.pub https://nginx.org/keys/nginx_signing.rsa.pub
```

验证下载文件是否包含正确的密钥：

```
$ openssl rsa -pubin -in /tmp/nginx_signing.rsa.pub -text -noout
Public-Key: (2048 bit)
Modulus:
    00:fe:14:f6:0a:1a:b8:86:19:fe:cd:ab:02:9f:58:
    2f:37:70:15:74:d6:06:9b:81:55:90:99:96:cc:70:
    5c:de:5b:e8:4c:b2:0c:47:5b:a8:a2:98:3d:11:b1:
    f6:7d:a0:46:df:24:23:c6:d0:24:52:67:ba:69:ab:
    9a:4a:6a:66:2c:db:e1:09:f1:0d:b2:b0:e1:47:1f:
    0a:46:ac:0d:82:f3:3c:8d:02:ce:08:43:19:d9:64:
    86:c4:4e:07:12:c0:5b:43:ba:7d:17:8a:a3:f0:3d:
    98:32:b9:75:66:f4:f0:1b:2d:94:5b:7c:1c:e6:f3:
    04:7f:dd:25:b2:82:a6:41:04:b7:50:93:94:c4:7c:
    34:7e:12:7c:bf:33:54:55:47:8c:42:94:40:8e:34:
    5f:54:04:1d:9e:8c:57:48:d4:b0:f8:e4:03:db:3f:
    68:6c:37:fa:62:14:1c:94:d6:de:f2:2b:68:29:17:
    24:6d:f7:b5:b3:18:79:fd:31:5e:7f:4c:be:c0:99:
    13:cc:e2:97:2b:dc:96:9c:9a:d0:a7:c5:77:82:67:
    c9:cb:a9:e7:68:4a:e1:c5:ba:1c:32:0e:79:40:6e:
    ef:08:d7:a3:b9:5d:1a:df:ce:1a:c7:44:91:4c:d4:
    99:c8:88:69:b3:66:2e:b3:06:f1:f4:22:d7:f2:5f:
    ab:6d
Exponent: 65537 (0x10001)
```

最后，将密钥移动的可信存储：

```
$ sudo mv /tmp/nginx_signing.rsa.pub /etc/apk/keys/
```

安装 nginx：

```
$ sudo apk add nginx
```

### FreeBSD 安装

请参考 [https://www.freebsd.org/doc/handbook/pkgng-intro.html](https://www.freebsd.org/doc/handbook/pkgng-intro.html)。

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
