## 微服务入门

### 微服务技术栈

#### 基本框架

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.21g9ma37obcw.webp)

#### 监控和日志

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2abvq887focg.webp)

#### 自动化部署：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3ljyl97ue880.webp)

### 认识微服务

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

### 服务拆分及远程调用

#### 服务拆分：

**服务拆分注意事项：**

1. 不同微服务，不要重复开发相同业务
2. 微服务数据独立，不要访问其它微服务的数据库

3. 微服务可以将自己的业务暴露为接口，供其它微服务调用

#### 远程调用：

> 订单服务如果需要查询用户信息，**只能调用用户服务的 Restful 接口**，不能查询用户数据库

因此我们需要知道 Java 如何去发送 http 请求，Spring 提供了一个 RestTemplate 工具，只需要把它创建出来即可。（即注入 Bean）

```java
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private RestTemplate restTemplate;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2.利用RestTemplate发起http请求,查询用户
        String url = "http://localhost:8081/user/" + order.getUserId();
        User user = restTemplate.getForObject(url, User.class);
        // 3.封装user到Order
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```

启动并访问：http://localhost:8080/order/101

得到如下结果：

```json
{
  "id": 101,
  "price": 699900,
  "name": "Apple 苹果 iPhone 12 ",
  "num": 1,
  "userId": 1,
  "user": {
    "id": 1,
    "username": "柳岩",
    "address": "湖南省衡阳市"
  }
}
```

在上面代码的 url 中，我们可以发现调用服务的地址采用硬编码，这在后续的开发中肯定是不理想的，这就需要**服务注册中心**（Eureka）来帮我们解决这个事情。

#### 提供者和消费者：

* 服务提供者:一次业务中，被其它微服务调用的服务。（提供接口给其它微服务）
* 服务消费者:一次业务中，调用其它微服务的服务。（调用其它微服务提供的接口）

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.5xux08l1skw0.webp)

**服务调用关系**

* 服务提供者：暴露接口给其它微服务调用
* 服务消费者：调用其它微服务提供的接口
* 提供者与消费者角色其实是相对的

## Eureka注册中心

### **服务调用出现的问题**

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.207gs13voin4.webp)

* 服务消费者该如何获取服务提供者的地址信息?
* 如果有多个服务提供者，消费者该如何选择?
* 消费者如何得知服务提供者的健康状态?

### Eureka的作用

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.28166dnvq13w.webp)

以下两张为动图显示：

![20220829124032](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/20220829124032.mbs0bp5seps.gif)

心跳续约，检测服务提供者的健康状态：

![20220829124314](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/20220829124314.5vbn2ddikrc0.gif)

* 消费者该如何获取服务提供者具体信息?
  * 服务提供者启动时向eureka注册自己的信息
  * eureka保存这些信息
  * 消费者根据服务名称向eureka拉取提供者信息
* 如果有多个服务提供者，消费者该如何选择?
  * 服务消费者利用负载均衡算法，从服务列表中挑选一个
* 消费者如何感知服务提供者健康状态?
  * 服务提供者会每隔30秒向Eureka Server发送心跳请求，报告健康状态
  * eureka会更新记录服务列表信息，心跳不正常会被剔除，消费者就可以拉取到最新的信息

**总结：**

在Eureka架构中，微服务角色有两类：

* Eureka Server：服务端，注册中心
  * 记录服务信息
  * 心跳监控
* Eureka Client:客户端
  * Provider：服务提供者，例如案例中的user-service
    * 注册自己的信息到Eureka Server
    * 每隔30秒向Eureka Server发送心跳
  * consumer：服务消费者，例如案例中的order-service
    * 根据服务名称从Eureka Server拉取服务列表
    * 基于服务列表做负载均衡，选中一个微服务后发起远程调用

### 搭建注册中心

#### 搭建 eureka-server

搭建Eureka Server服务步骤如下:

1. 引入 SpringCloud 为 eureka 提供的 starter 依赖，注意这里是用 **server**，地址：[Maven Repository: org.springframework.cloud » spring-cloud-starter-netflix-eureka-server (mvnrepository.com)](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server)

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

2. 编写启动类，添加`@EnableEurekaServer`注解

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}

}
```

2. 添加application.yml文件，编写下面的配置:

> 其中 `default-zone` 是因为前面配置类开启了注册中心所需要配置的 eureka 的**地址信息**，因为 eureka 本身也是一个微服务，这里也要将自己注册进来，当后面 eureka **集群**时，这里就可以填写多个，使用 “,” 隔开。

```yaml
server:
  port: 8079 # 服务端口
spring:
  application:
    name: eurekaserver # eureka的服务名称
eureka:
  client:
    service-url: # eureka的地址信息，集群使用逗号分隔
      defaultZone: http://127.0.0.1:8079/eureka
```

启动完成后，访问即可

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.4k3d44ihrpq0.webp)

#### 服务注册

将 user-service、order-service 都注册到 eureka

1. 引入 SpringCloud 为 eureka 提供的 starter 依赖，注意这里是用 **client**，地址：[Maven Repository: org.springframework.cloud » spring-cloud-starter-netflix-eureka-client (mvnrepository.com)](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-client)

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-client -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

```

2. 在启动类上添加注解：`@EnableEurekaClient`

3. 在application.yml中添加配置

```yaml
spring:
    application:
        name: userservice # 服务名称
eureka:
  client:
    service-url: # eureka的地址信息，集群使用逗号分隔
      defaultZone: http://127.0.0.1:8079/eureka
```

启动后刷新eureka，成功注册

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7dsjukd80jc0.webp)

#### 服务拉取

在 order-service 中完成服务拉取，然后通过负载均衡挑选一个服务，实现远程调用

1. 首先给 `RestTemplate` 这个 Bean 添加一个 `@LoadBalanced` **注解**，用于开启**负载均衡**。（后面会讲）

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate(){
    return new RestTemplate();
}
```

2. 修改 OrderService 访问的url路径，用**服务名**代替ip、端口：

```java
```

spring 会自动帮助我们从 eureka-server 中，根据 userservice 这个服务名称，获取实例列表后去完成负载均衡。

### Ribbon负载均衡