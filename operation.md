# Nginx 操作

启动或执行 nginx 需要 sudo 权限。

### 基本命令

1、`$ nginx -v | -V` 

查看版本：

```
$ nginx -v
```

查看版本和配置信息：

```
$ nginx -V
nginx version: nginx/1.15.7
built by gcc 6.3.0 20170516 (Debian 6.3.0-18+deb9u1) 
built with OpenSSL 1.1.0f  25 May 2017
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log ....
```

2、`$ nginx -t | -T`

检查配置，当我们改完配置后，最好先执行 nginx -t 确保配置语法正确：

```
$ nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

检查配置并显示，会将所有检查的配置打印出来：

```
$ nginx -T
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
# configuration file /etc/nginx/nginx.conf:
...
```

3、`$ nginx -q`

在检查配置期间禁止非错误消息：

```
$ nginx -q
```

4、`$ nginx -s signal`

向 nginx 主进程发送信号，包括（stop，quit，reopen，reload）。这里有个 master 与 worker 的概念：

* master，主进程，只有一个，用来管理 worker 进程，如给分发请求到 workder，fork 新的 worker 进程。
* worker，工作进程，可以被 master fork 多个，所有的任务处理全部是在 worker 中进行

```
# 查看进程
$ ps -aux | grep nginx
root        30  0.0  0.0  32624   828 ?        Ss   10:33   0:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
nginx       31  0.0  0.1  33076  2344 ?        S    10:33   0:00 nginx: worker process

$ 发送信号
$ nginx -s stop            # 直接停止nginx
$ nginx -s quit            # 优雅退出nginx，会服务现有请求
$ nginx -s reopen          # 重新打开日志文件，如使用logrotate更改了文件，通过该命令就可以重打开
$ nginx -s reload          # 重载配置
```

5、`$ nginx -p prefix`

指定前缀运行 nginx：

```
$ nginx -p /etc/nginx
```

6、`$ nginx -c filename`

指定启动 nginx 的配置文件：

```
$ nginx -c /etc/nginx/nginx.conf
```

7、`$ nginx -g directives`

在配置文件外设置全局指令：

```
$ nginx -g 'daemon off;'   # 关闭nginx后台运行
```

### 管理nginx

nginx 的具体操作也可由 service 或 sytemd 来管理。

* service，比较旧的启动方式，自身也是一个脚本，它会去执行 `/etc/init.d` 下的相关软件脚本。缺点是：
  * 缺点，启动时间长，init 过程是串行的。
  * 启动脚本复杂，脚本需要自己处理各种情况，很复杂。
* systemd，最新的一种启动方式，为系统的启动和管理提供了一套完整的解决方案。缺点是：
  * 功能强大，但体系庞大，非常复杂，与操作系统有强耦合。

启动 nginx：

```
$ service nginx start
$ systemctl nginx start

# 开机自启动
$ systemctl enable nginx
```

停止 nginx：

```
$ service nginx stop
$ systemctl nginx stop
```

重启 nginx：

```
$ service nginx restart
$ systemctl nginx restart
```

重载 nginx：

```
$ service nginx reload
$ systemctl nginx reload
```

