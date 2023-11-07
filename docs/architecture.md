# 架构

## 后端 @裴雷

- 数据库：MySQL
- 缓存：Redis
- 服务发现与配置中心：Nacos
- 网关：SpringCloud Gateway
- 微服务：SpringCloud Alibaba
- 认证与授权：Spring Security
- 远程调用：Feign
- 存储：Qiniu
- 部署：Docker-Compose
- 接口文档：Swagger

### 微服务

- 网关微服务：gateway-service
    - 请求统一拦截与管理
- 认证与授权微服务：auth-service
    - 注册、登陆
    - jwt 认证与授权
- 缓存微服务：redis-service
    - 缓存统一处理
- 用户数据微服务：user-service
- 视频数据微服务：video-service
    - 对接七牛云存储服务，实现视频上传
    - 视频拉取
- 交互数据微服务：interactive-service
    - 观看、点赞、收藏、分享