[微服务技术栈 - 乐心湖's Blog | 技术小白的技术博客 (xn2001.com)](https://www.xn2001.com/archives/663.html)

[02-Nacos配置管理-Nacos实现配置管理_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1LQ4y127n4?p=25&vd_source=c65d1a9fa31319a69b57c9ae59a0b0d9)

## 微服务入门

### 微服务技术栈

#### 基本框架

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.21g9ma37obcw.webp)

#### 监控和日志

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2abvq887focg.webp)

#### 自动化部署

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3ljyl97ue880.webp)

### 认识微服务

#### 服务治理

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2ta8j0vuvx40.webp)

#### 微服务

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1zz70ufi3xi8.webp)

#### 微服务结构

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.tshi8md1oyo.webp)

#### 微服务技术对比

|                | Dubbo               | Spring Cloud             | SpringCloudAlibaba       |
| -------------- | ------------------- | ------------------------ | ------------------------ |
| 注册中心       | zookeeper、Redis    | Eureka、Consul           | Nacos、Eureka            |
| 服务远程调用   | Dubbo协议           | Feign（http协议）        | Dubbo、Feign             |
| 配置中心       | 无                  | SpringCloudConfig        | SpringCloudConfig、Nacos |
| 服务网关       | 无                  | SpringCloudGateway、Zuul | SpringCloudGateway、Zuul |
| 服务监控和保护 | dubbo-admin，功能弱 | Hystrix                  | Sentinel                 |

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3j12ttwuxxk0.webp)

#### 企业需求

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.3809o5hu4qg0.webp)

#### SpringCloud

* SpringCloud是目前国内使用最广泛的微服务框架。官网地址: [Spring Cloud](https://spring.io/projects/spring-cloud)。

* SpringCloud集成了各种微服务功能组件，并基于SpringBoot实现了这些组件的自动装配，从而提供了良好的开箱即用体验:

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.25n0k75gmneo.webp)

#### SpringCloud和SpringBoot版本兼容

最新兼容信息请前往官网查询

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1kru0gze6yg0.webp)

### 服务拆分及远程调用

#### 服务拆分

**服务拆分注意事项：**

1. 不同微服务，不要重复开发相同业务
2. 微服务数据独立，不要访问其它微服务的数据库

3. 微服务可以将自己的业务暴露为接口，供其它微服务调用

#### 远程调用

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

#### 提供者和消费者

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
String url = "http://userservice/user/" + order.getUserId();
```

spring 会自动帮助我们从 eureka-server 中，根据 userservice 这个服务名称，获取实例列表后去完成负载均衡。

#### 总结

1. 搭建Eureka Server
   * 引入eureka-server依赖
   * 添加`@EnableEurekaServe`r注解
   * 在application.yml中配置eureka地址
2. 服务注册
   * 引入eureka-client依赖
   * 在application.yml中配置eureka地址
3. 服务发现
   * 引入eureka-client依赖
   * 在application.yml中配置eureka地址
   * 给RestTemplate添加`@LoadBalanced`注解
   * 用服务提供者的服务名称远程调用

### Ribbon负载均衡

我们添加了 `@LoadBalanced` 注解，即可实现负载均衡功能，这是什么原理呢？

**SpringCloud 底层提供了一个名为 Ribbon 的组件，来实现负载均衡功能。**

#### 负载均衡流程

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.63h2a8e0wn00.webp)

#### 源码跟踪

为什么我们只输入了 service 名称就可以访问了呢？为什么不需要获取ip和端口，这显然有人帮我们根据 service 名称，获取到了服务实例的ip和端口。它就是`LoadBalancerInterceptor`，这个类会在对 RestTemplate 的请求进行拦截，然后从 Eureka 根据服务 id 获取服务列表，随后利用负载均衡算法得到真实的服务地址信息，替换服务 id。

我们进行源码跟踪：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.6x9tu3fff0w0.webp)

首先该方法实现了`ClientHttpRequestInterceptor`接口，该接口可以拦截 HttpRequest 请求

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.5a7zo8o0dto0.webp)

这里的 `intercept()` 方法，拦截了用户的 HttpRequest 请求，然后做了几件事：

- `request.getURI()`：获取请求uri，即 http://user-service/user/{id}
- `originalUri.getHost()`：获取uri路径的主机名，其实就是服务id `user-service`
- `this.loadBalancer.execute()`：处理服务id，和用户请求

这里的 `this.loadBalancer` 是 `LoadBalancerClient` 类型

继续跟入 `execute()` 方法：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1m1puffu0usg.webp)

- `getLoadBalancer(serviceId)`：根据服务id获取 `ILoadBalancer`，而 `ILoadBalancer` 会拿着服务 id 去 eureka 中获取服务列表。
- `getServer(loadBalancer)`：利用内置的负载均衡算法，从服务列表中选择一个。

可以看到获取服务时，通过一个 `getServer()` 方法来做负载均衡:

继续跟踪源码 `chooseServer()` 方法，发现这么一段代码：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7iax2dsj1po0.webp)

我们看看这个 `rule` 是谁：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.4eu46yeu1f60.webp)

这里的 rule 默认值是一个 `RoundRobinRule` ，看类的介绍：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.31bmewi651a0.webp)

负载均衡默认使用了轮训算法，当然我们也可以自定义。

#### 流程总结

SpringCloud Ribbon 底层采用了一个拦截器，拦截了 RestTemplate 发出的请求，对地址做了修改。

基本流程如下：

- 拦截我们的 `RestTemplate` 请求 http://userservice/user/1
- `RibbonLoadBalancerClient` 会从请求url中获取服务名称，也就是 user-service
- `DynamicServerListLoadBalancer` 根据 user-service 到 eureka 拉取服务列表
- eureka 返回列表，localhost:8081、localhost:8082
- `IRule` 利用内置负载均衡规则，从列表中选择一个，例如 localhost:8081
- `RibbonLoadBalancerClient` 修改请求地址，用 localhost:8081 替代 userservice，得到 http://localhost:8081/user/1，发起真实请求

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.5z1251g4ccg0.webp)

#### 负载均衡策略

负载均衡的规则都定义在 IRule 接口中，而 IRule 有很多不同的实现类：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.5shfnv7mp140.webp)

不同规则的含义如下：

| **内置负载均衡规则类**    | **规则描述**                                                 |
| :------------------------ | :----------------------------------------------------------- |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则。 |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：（1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。 （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule 规则的客户端也会将其忽略。并发连接数的上限，可以由客户端设置。 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。 |
| **ZoneAvoidanceRule**     | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询。 |
| BestAvailableRule         | 忽略那些短路的服务器，并选择并发数较低的服务器。             |
| RandomRule                | 随机选择一个可用的服务器。                                   |
| RetryRule                 | 重试机制的选择逻辑                                           |

默认的实现就是 `ZoneAvoidanceRule`，**是一种轮询方案**。

#### 自定义策略

通过定义 IRule 实现可以修改负载均衡规则，有两种方式：

1. 代码方式在 order-service 中的 OrderApplication 类中，定义一个新的 IRule：

```java
@Bean
public IRule iRule() {
    return new RandomRule();
}
```

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.6mrbzr7rsi40.webp)

2. 配置文件方式：在 order-service 的 application.yml 文件中，添加新的配置也可以修改规则：

```yaml
userservice: # 给需要调用的微服务配置负载均衡规则，orderservice服务去调用userservice服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则 
```

**注意**：一般用默认的负载均衡规则，不做修改。

#### 饥饿加载

当我们启动 orderservice，第一次访问时，时间消耗会大很多，这是因为 Ribbon 懒加载的机制。

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.77fti1p7sbc0.webp)

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7816p9xs1yo0.webp)

Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true
    clients: userservice # 项目启动时直接去拉取userservice的集群，多个用","隔开
```

#### 总结

1. Ribbon负载均衡规则
   * 规则接口是IRule
   * 默认实现是ZoneAvoidanceRule，根据zone选择服务列表，然后轮询
2. 负载均衡自定义方式
   * 代码方式︰配置灵活，但修改时需要重新打包发布
   * 配置方式:直观，方便，无需重新打包发布，但是无法做全局配置。
3. 饥饿加载
   * 开启饥饿加载
   * 指定饥饿加载的微服务名称

## Nacos注册中心

### 下载安装

> 地址：[alibaba/nacos: an easy-to-use dynamic service discovery, configuration and service management platform for building cloud native applications. (github.com)](https://github.com/alibaba/nacos)

下载、解压、启动Nacos

启动命令：

```bash
startup.cmd -m standalone
```

> 访问：http://localhost:8848/nacos/

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7hflulyqbkk0.webp)

### 服务注册

这里上来就直接服务注册，很多东西可能有疑惑，其实 Nacos 本身就是一个 SprintBoot 项目，这点你从启动的控制台打印就可以看出来，所以就不再需要去额外搭建一个像 Eureka 的注册中心。

#### 引入依赖

1. 在 cloud-demo 父工程中引入 Spring Cloud Alibaba 的依赖：地址：[Maven Repository: com.alibaba.cloud » spring-cloud-alibaba-dependencies (mvnrepository.com)](https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-alibaba-dependencies)

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-alibaba-dependencies -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.7.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

2. 添加nacos的客户端依赖，地址：[Maven Repository: com.alibaba.cloud » spring-cloud-starter-alibaba-nacos-discovery (mvnrepository.com)](https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery)

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>2.2.7.RELEASE</version>
</dependency>
```

项目重新启动后，可以看到服务都被注册进了 Nacos

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7i85tbknnvk0.webp)

### 分级存储模型

一个**服务**可以有多个**实例**，例如我们的 user-service，可以有：

- 127.0.0.1:8081
- 127.0.0.1:8082
- 127.0.0.1:8083

假如这些实例分布于全国各地的不同机房，例如：

- 127.0.0.1:8081，在上海机房
- 127.0.0.1:8082，在上海机房
- 127.0.0.1:8083，在杭州机房

Nacos就将同一机房内的实例，划分为一个**集群**。

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.7jnglsj5mw40.webp)

微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。**当本集群内不可用时，才访问其它集群。**例如：杭州机房内的 order-service 应该优先访问同机房的 user-service。

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1qe2u2qg2n6o.webp)

### 配置集群

接下来我们给 user-service **配置集群**

修改 user-service 的 application.yml 文件，添加集群配置：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称 HZ杭州
```

重启两个 user-service 实例后，我们再去启动一个上海集群的实例。

```
-Dserver.port=8083 -Dspring.cloud.nacos.discovery.cluster-name=SH
```

查看 nacos 控制台

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.59ejt1mw4tk0.webp)

### NacosRule负载均衡

Ribbon的默认实现 `ZoneAvoidanceRule` 并不能实现根据同集群优先来实现负载均衡，我们把规则改成 **NacosRule** 即可。我们是用 orderservice 调用 userservice，所以在 orderservice 配置规则。

1. 代码形式

```java
@Bean
public IRule iRule(){
    //默认为轮询规则，这里自定义为随机规则
    return new NacosRule();
}
```

2. 配置形式

   * 另外，你同样可以使用配置的形式来完成，具体参考上面的 Ribbon 栏目。

   ```yaml
   userservice:
     ribbon:
       NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule #负载均衡规则 
   ```

   * 然后，再对 orderservice 配置集群。

   ```yaml
   spring:
     cloud:
       nacos:
         server-addr: localhost:8848
         discovery:
           cluster-name: HZ # 集群名称
   ```

   现在启动了四个服务，分别是：

   - orderservice - HZ
   - userservice - HZ
   - userservice1 - HZ
   - userservice2 - SH

现在访问地址：http://localhost:8080/order/101

在访问中我们发现，只有同在一个 HZ 集群下的 userservice、userservice1 会被调用，并且是随机的。

我们试着把 userservice、userservice1 停掉。依旧可以访问。

在 userservice2 控制台可以看到发出了一串的警告，因为 orderservice 本身是在 HZ 集群的，HZ 集群没有了 userservice，就会去别的集群找。

### 权重配置

实际部署中会出现这样的场景：

服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求。但默认情况下NacosRule 是同集群内随机挑选，不会考虑机器的性能问题。

因此，Nacos 提供了**权重配置来控制访问频率**，0~1 之间，权重越大则访问频率越高，权重修改为 0，则该实例永远不会被访问。

在 Nacos 控制台，找到 user-service 的实例列表，点击编辑，即可修改权重。

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.4ptqtblh7hk0.webp)

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.4dqfodz5ldw0.webp)

另外，在服务升级的时候，有一种较好的方案：我们也可以通过调整权重来进行平滑升级，例如：先把 userservice 权重调节为 0，让用户先流向 userservice2、userservice3，升级 userservice后，再把权重从 0 调到 0.1，让一部分用户先体验，用户体验稳定后就可以上调权重。

### 环境隔离-namespace

Nacos中服务存储和数据存储的最外层都是一个名为namespace的东西，用来做最外层隔离

- Nacos 中可以有多个 namespace
- namespace 下可以有 group、service 等
- 不同 namespace 之间**相互隔离**，例如不同 namespace 的服务互相不可见

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.62xdcd2wqiw0.webp)

#### 创建namespace

默认情况下，所有 service、data、group 都在同一个 namespace，名为 public(保留空间)：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2ox6q422wpg0.webp)

我们可以点击页面新增按钮，添加一个 namespace：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.69kjfh17lt80.webp)

然后，填写表单：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.6vmngvjubrg0.webp)

就能在页面看到一个新的 namespace：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.5b2bfx52aq00.webp)

#### 配置namespace

给微服务配置 namespace 只能通过修改配置来实现。

例如，修改 order-service 的 application.yml 文件：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ
        namespace: 492a7d5d-237b-46a1-a99a-fa8e98e4b0f9 # 命名空间ID
```

重启 order-service 后，访问控制台。

**public**

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1fljfkzoh4dc.webp)

**dev**

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2x6cn4nmkom0.webp)

此时访问 order-service，因为 namespace 不同，会导致找不到 userservice，控制台会报错：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.44kz3i6i19k0.webp)

#### 总结：

Nacos环境隔离

* namespace用来做环境隔离
* 每个namespace都有唯一id
* 不同namespace下的服务不可见

### 临时实例

Nacos 的服务实例分为两种类型：

- **临时实例**：如果实例宕机超过一定时间，会从服务列表剔除，**默认的类型**。
- 非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。

配置一个服务实例为永久实例：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例
```

另外，Nacos 集群**默认采用AP方式(可用性)**，当集群中存在非临时实例时，**采用CP模式(一致性)**；而 Eureka 采用AP方式，不可切换。（这里说的是 CAP 原理，后面会写到）

## Eureka和Nacos注册中心的区别

### Nacos注册中心原理

#### nacos注册中心细节分析

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.30kcj42umey0.webp)

### 总结

1. Nacos与eureka的共同点
  * 都支持服务注册和服务拉取
  * 都支持服务提供者心跳方式做健康检测

2. Nacos与Eureka的区别
   * Nacos支持服务端主动检测提供者状态:临时实例采用心跳模式，非临时实例采用主动检测模式
   * 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
   * Nacos支持服务列表变更的消息推送模式，服务列表更新更及时
   * Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式

## Nacos配置中心

![20220830094832](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/20220830094832.sgms3hdm0sw.gif)

### 统一配置管理

当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会让人抓狂，而且很容易出错。**我们需要一种统一配置管理方案，可以集中管理所有实例的配置。**

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.560gdgarsoo0.webp)

Nacos 一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，**实现配置的热更新。**

#### 配置获取步骤

![20220830100505](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/20220830100505.6pkjy61r5q80.gif)

### 创建配置

在 Nacos 控制面板中添加配置文件

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2e9qzviw7sw.webp)

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.2giiaz16r2ck.webp)

**配置内容：**

举例：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.6iedqgc3cxg0.webp)

**注意**：项目的核心配置，需要热更新的配置才有放到 nacos 管理的必要。基本不会变更的一些配置（例如数据库连接）还是保存在微服务本地比较好。

### 拉取配置

首先我们需要了解 Nacos 读取配置文件的环节是在哪一步，在没加入 Nacos 配置之前，获取配置是这样：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.20xk4ic4vajk.webp)

加入 Nacos 配置，它的读取是在 application.yml 之前的：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.qn93hldzxjk.webp)

这时候如果把 nacos 地址放在 application.yml 中，显然是不合适的，**Nacos 就无法根据地址去获取配置了。**

因此，nacos 地址必须放在优先级最高的 bootstrap.yml 文件。

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.f63inpd3yrk.webp)

**引入 nacos-config 依赖**

首先，在 user-service 服务中，引入 nacos-config 的客户端依赖：

```xml
<!--nacos配置管理依赖-->
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-config -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>2.2.7.RELEASE</version>
</dependency>
```

**添加 bootstrap.yml**

然后，在 user-service 中添加一个 bootstrap.yml 文件，内容如下：

```yaml
spring:
  application:
    name: userservice # 服务名称
  profiles:
    active: dev #开发环境，这里是dev
  cloud:
    nacos:
      server-addr: localhost:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
```

根据 spring.cloud.nacos.server-addr 获取 nacos地址，再根据`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`作为文件id，来读取配置。

在这个例子例中，就是去读取 `userservice-dev.yaml`

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.53nvh0rzxi80.webp)

与Nacos中的配置一致：

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.14cy8rltwcio.webp)

使用代码来验证是否拉取成功

在 user-service 中的 UserController 中添加业务逻辑，读取 pattern.dateformat 配置并使用：

```java
@Value("${pattern.dateformat}")
private String dateformat;

@GetMapping("now")
public String now(){
    //格式化时间
    return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
}
```

启动服务后，访问：http://localhost:8081/user/now

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.1eeieaq84i8w.webp)

### 配置热更新

我们最终的目的，是修改 nacos 中的配置后，微服务中无需重启即可让配置生效，也就是**配置热更新**。

有两种方式：1. 用 `@value` 读取配置时，搭配 `@RefreshScope`；2. 直接用 `@ConfigurationProperties` 读取配置

#### @RefreshScope

方式一：在 `@Value` 注入的变量所在类上添加注解 `@RefreshScope`

![image](https://cdn.staticaly.com/gh/bolishitoumingde/hexo_img@main/image.101o8tmbtfu8.webp)

#### @ConfigurationProperties

方式二：使用 `@ConfigurationProperties` 注解读取配置文件，就不需要加 `@RefreshScope` 注解。

在 user-service 服务中，添加一个 PatternProperties 类，读取 `patterrn.dateformat` 属性

```java
@Data
@Component
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    public String dateformat;
}
```

```java
@Autowired
private PatternProperties patternProperties;

@GetMapping("now2")
public String now2(){
    //格式化时间
    return LocalDateTime.now().format(DateTimeFormatter.ofPattern(patternProperties.dateformat));
}
```

### 配置共享

其实在服务启动时，nacos 会读取多个配置文件，例如：

- `[spring.application.name]-[spring.profiles.active].yaml`，例如：userservice-dev.yaml
- `[spring.application.name].yaml`，例如：userservice.yaml

这里的 `[spring.application.name].yaml` 不包含环境，**因此可以被多个环境共享**。