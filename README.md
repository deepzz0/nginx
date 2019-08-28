# Nginx Example

一份从零梳理的 nginx 配置指南。目前正在 **快速迭代期**，目录随时可能变动。



本文档旨在快速定位 nginx 如何配置以及其中配置的磕磕绊绊。

### 文档目录

- [x] [nginx 介绍](introduce.md)
- [x] [nginx 安装](install.md)
- [ ] nginx 操作
- [ ] 相关概念
  - [ ] nginx main配置
  - [ ] http server 结构
  - [ ] listen 如何配置（udp）
  - [ ] server_name 配置
  - [ ] location 如何配置
  - [ ] upstream 如何配置
  - [ ] ssl 如何配置
  - [ ] log 如何配置（rotate）
- [ ] 基础配置
  - [ ] `静态文件服务器`：第一份nginx配置（root、try_files）
  - [ ] `多虚拟主机`：如何配置多虚拟主机，配置多个server
  - [ ] `站点防盗链`：如何为我们的站点启用防盗链
  - [ ] `HTTPS站点`：如何配置HTTPS服务器及为一个站点配置双证书（http2）
  - [ ] `客户端认证`：如何实现客户端双向认证
  - [ ] `前后端代理`：如何实现前端本地开发后端代理，防止跨域
  - [ ] `Basic Auth`：如何让 nginx 访问需要 basic auth 权限的后端
  - [ ] `重定向`：如何配置对站点的重定向，对 uri 的重定向
  - [ ] `location练习`：如何配置 location，location 知识点练习
  - [ ] `邮件服务器`：如何配置邮件服务器
  - [ ] `健康检查`：如何对 nginx 实现健康检查（stub_status）
- [ ] 访问控制
  - [ ] `请求速率限制`：如何对请求速率进行限制
  - [ ] `链接限制`：如何限制每个 IP 所打开的链接数
  - [ ] `IP访问控制`：如何阻止或允许指定IP访问虚拟主机
- [ ] 代理配置
  - [ ] `反向代理`：如何为我们的站点设置反向代理
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
