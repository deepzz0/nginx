# Nginx Example

一份从零梳理的 nginx 配置指南。目前正在 **快速迭代期**，目录随时可能变动。



本文档旨在快速定位 nginx 如何配置以及其中配置的磕磕绊绊。

### 文档目录

- [x] [Nginx 介绍](introduce.md)
- [x] [Nginx 安装](install.md)
- [x] [Nginx 操作](operation.md)
- [ ] 相关概念
  - [ ] 事件机制
  - [ ] 信号机制
- [x] [配置概念](config/conf-concept.md)
  - [ ] nginx main配置
  - [ ] http server 结构
  - [ ] listen 如何配置（udp）
  - [ ] server_name 配置
  - [ ] location 如何配置
  - [ ] upstream 如何配置
  - [x] [ssl 如何配置](config/ssl-conf.md)
  - [ ] log 如何配置（rotate）
- [x] 基础配置
  - [x] [静态文件服务器](config/file-svr.md)：第一份nginx配置（root、try_files）
  - [x] [多虚拟主机](config/multi-svr.md)：如何配置多虚拟主机，配置多个server
  - [x] [站点防盗链](config/valid-referer.md)：如何为我们的站点启用防盗链
  - [x] [HTTPS站点](config/https-site.md)：如何配置HTTPS服务器及为一个站点配置双证书（http2）
  - [x] [客户端认证](config/client-verify.md)：如何实现客户端 SSL 双向认证
  - [x] [前端跨域cors](config/fe-cors.md)：如何实现前端本地开发后端代理，防止跨域
  - [x] [Basic Auth](config/basic-auth.md)：basic auth配置，如何让 nginx 遵循 Basic Auth 认证协议
  - [ ] `重定向`：如何配置对站点的重定向，对 uri 的重定向
  - [ ] `location练习`：如何配置 location，location 知识点练习
  - [ ] `邮件服务器`：如何配置邮件服务器
  - [ ] `健康检查`：如何对 nginx 实现健康检查（stub_status）
- [ ] 访问控制
  - [ ] `请求速率限制`：如何对请求速率进行限制
  - [ ] `链接限制`：如何限制每个 IP 所打开的链接数
  - [ ] `IP访问控制`：如何阻止或允许指定IP访问虚拟主机
- [ ] 代理配置
  - [ ] `反向代理`：如何为我们的站点设置反向代理（反向客户端认证HTTPS）
  - [ ] `GRPC代理`：如何配置 grpc 代理及 grpc ssl 代理
  - [ ] `WebSocket`：如何配置 websocket 代理
  - [ ] `Memcached`：如何使用 nginx 对 memcached 代理
- [ ] 负载均衡
  - [ ] `负载均衡`：如何为我们的多服务配置负载均衡
  - [ ] `地域GeoIP`：如何根据地域负载我们的流量
- [ ] 综合应用
  - [ ] 基于反向代理的负载均衡API的访问控制的 nginx 配置
- [ ] 高级应用
- [ ] nginx 安全

### 参考文档

http://nginx.org/en/docs/



### 贡献成员



### 授权许可

本项目采用 MIT 开源授权许可证，完整的授权说明已放置在 [LICENSE](LICENSE) 文件中。