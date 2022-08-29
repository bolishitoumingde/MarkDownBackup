## 微服务入门：

### 微服务技术栈：

#### 基本框架

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.21g9ma37obcw.webp)

#### 监控和日志

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2abvq887focg.webp)

#### 自动化部署：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3ljyl97ue880.webp)

### 认识微服务：

#### 服务治理：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2ta8j0vuvx40.webp)

#### 微服务：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1zz70ufi3xi8.webp)

#### 微服务结构：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.tshi8md1oyo.webp)

#### 微服务技术对比：

|                | Dubbo               | Spring Cloud             | SpringCloudAlibaba       |
| -------------- | ------------------- | ------------------------ | ------------------------ |
| 注册中心       | zookeeper、Redis    | Eureka、Consul           | Nacos、Eureka            |
| 服务远程调用   | Dubbo协议           | Feign（http协议）        | Dubbo、Feign             |
| 配置中心       | 无                  | SpringCloudConfig        | SpringCloudConfig、Nacos |
| 服务网关       | 无                  | SpringCloudGateway、Zuul | SpringCloudGateway、Zuul |
| 服务监控和保护 | dubbo-admin，功能弱 | Hystrix                  | Sentinel                 |

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3j12ttwuxxk0.webp)

#### 企业需求：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3809o5hu4qg0.webp)

#### SpringCloud：

* SpringCloud是目前国内使用最广泛的微服务框架。官网地址: [Spring Cloud](https://spring.io/projects/spring-cloud)。

* SpringCloud集成了各种微服务功能组件，并基于SpringBoot实现了这些组件的自动装配，从而提供了良好的开箱即用体验:

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.25n0k75gmneo.webp)

#### SpringCloud和SpringBoot版本兼容：

最新兼容信息请前往官网查询

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1kru0gze6yg0.webp)

### 服务拆分及远程调用：

**服务拆分注意事项：**

1. 不同微服务，不要重复开发相同业务
2. 微服务数据独立，不要访问其它微服务的数据库

3. 微服务可以将自己的业务暴露为接口，供其它微服务调用