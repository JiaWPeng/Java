# SpringCloud

### 单体架构

将业务的所有功能集中在一个项目中开发，打成一个包部署

#### 优点

- 架构简单

- 部署成本低

#### 缺点

- 耦合度高

### 分布式架构

- 降低服务耦合
- 有利于服务升级拓展

|                | **Dubbo**           | **SpringCloud**          | **SpringCloudAlibaba**   |
| -------------- | ------------------- | ------------------------ | ------------------------ |
| 注册中心       | zookeeper、Redis    | Eureka、Consul           | Nacos、Eureka            |
| 服务远程调用   | Dubbo协议           | Feign（http协议）        | Dubbo、Feign             |
| 配置中心       | 无                  | SpringCloudConfig        | SpringCloudConfig、Nacos |
| 服务网关       | 无                  | SpringCloudGateway、Zuul | SpringCloudGateway、Zuul |
| 服务监控和保护 | dubbo-admin，功能弱 | Hystix                   | Sentinel                 |

![image-20221209205040440](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209205040440.png)

## SpringCloud

SpringCloud是目前国内使用最广泛的微服务框架。官网地址：https://spring.io/projects/spring-cloud。

SpringCloud集成了各种微服务功能组件，并基于SpringBoot实现了这些组件的自动装配，从而提供了良好的开箱即用体验：

![image-20221209205152622](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209205152622.png)

#### 服务拆分注意事项

1.单一职责：不同微服务，不要重复开发相同业务

2.数据独立：不要访问其它微服务的数据库

3.面向服务：将自己的业务暴露为接口，供其它微服务调用

### 微服务调用方式

基于RestTemplate发起的http请求实现远程调用

http请求做远程调用是与语言无关的调用，只要知道对方的ip、端口、接口路径、请求参数即可。

#### 服务调用关系

- 服务提供者：暴露接口给其它微服务调用、
- 服务消费者：调用其它微服务提供的接口
- 提供者与消费者角色其实是**相对**的
- 一个服务可以同时是服务提供者和服务消费者

### Eureke

#### 作用

- 消费者该如何获取服务提供者具体信息？
  - 服务提供者启动时向eureka注册自己的信息
  - eureka保存这些信息
  - 消费者根据服务名称向eureka拉取提供者信息

- 如果有多个服务提供者，消费者该如何选择？
  - 服务消费者利用负载均衡算法，从服务列表中挑选一个

- 消费者如何感知服务提供者健康状态？
  - 服务提供者会每隔30秒向EurekaServer发送心跳请求，报告健康状态
  - eureka会更新记录服务列表信息，心跳不正常会被剔除
  - 消费者就可以拉取到最新的信息

#### 在Eureka架构中，微服务角色有两类：

EurekaServer：服务端，注册中心

- 记录服务信息

- 心跳监控

EurekaClient：客户端

- Provider：服务提供者，例如案例中的 user-service
  - 注册自己的信息到EurekaServer
  - 每隔30秒向EurekaServer发送心跳
- consumer：服务消费者，例如案例中的 order-service
  - 根据服务名称从EurekaServer拉取服务列表
  - 基于服务列表做负载均衡，选中一个微服务后发起远程调用

![image-20221210095538652](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210095538652.png)

#### EurekaServer使用搭建

1. 导入依赖

   ```xml
   <dependency>    
       <groupId>org.springframework.cloud</groupId>    
       <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

2. 在Springboot启动类中添加**@EnableEurekaServer注解**

3. 添加application.yml

   ```yaml
   server:
     port: 10086
   spring:
     application:
       name: eurekaserver
   eureka:
     client:
       service-url:
         defaultZone: http://127.0.0.1:10086/eureka/
   
   ```

   

##### 注册user-service

1. 在user-service项目引入spring-cloud-starter-netflix-erueka-client依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   
   ```

2. 在application.yml添加配置

   ```yaml
   spring:
     application:
       name: userservice
   eureka:
     client:
       service-url:
         defaultZone: http://127.0.0.1:10086/eureka/
   
   ```

   

   #### 注册order-service

1. 在order-service项目引入spring-cloud-starter-netflix-erueka-client依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   
   ```

2. 在application.yml添加配置

   ```yaml
   spring:
     application:
       name: orderservice
   eureka:
     client:
       service-url:
         defaultZone: http://127.0.0.1:10086/eureka/
   
   ```

   

#### 在order-service完成服务拉取

服务拉取是基于服务名称获取服务列表，然后在对服务列表做负载均衡

1. 修改OrderService的代码，修改访问的url路径，用服务名代替ip、端口：

   ```java
   String url = "http://userservice/user/" + order.getUserId();
   ```

2. 在order-service项目的启动类OrderApplication中的RestTemplate添加**负载均衡**注解

   ```java
   @Bean
   @LoadBalanced
   public RestTemplate restTemplate() {
       return new RestTemplate();
   }
   ```

   

#### 总结

1. 搭建EurekaServer
   1. 引入eureka-server依赖
   2. 添加@EnableEurekaServer注解
   3. 在application.yml中配置eureka地址
2. 服务注册
   1. 引入eureka-client依赖
   2. 在application.yml中配置服务名称
   3. 在application.yml中配置eureka地址
3. 服务发现
   1. 引入eureka-client依赖
   2. 在application.yml中配置eureka地址
   3. 给RestTemplate添加@LoadBalanced注解
   4. 用服务提供者的服务名称远程调用

### Ribbon负载均衡

、![image-20221209212355893](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212355893.png)

![image-20221209212407311](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212407311.png)

![image-20221209212416319](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212416319.png)

| **内置负载均衡规则类**    | **规则描述**                                                 |
| ------------------------- | ------------------------------------------------------------ |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则。 |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：   （1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。  （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的<clientName>.<clientConfigNameSpace>.ActiveConnectionsLimit属性进行配置。 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。 |
| ZoneAvoidanceRule         | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询。 |
| BestAvailableRule         | 忽略那些短路的服务器，并选择并发数较低的服务器。             |
| RandomRule                | 随机选择一个可用的服务器。                                   |
| RetryRule                 | 重试机制的选择逻辑                                           |

通过定义IRule实现可以修改负载均衡规则，有两种方式：

1.代码方式：在order-service中的OrderApplication类中，定义一个新的IRule：

```java
@Bean
public IRule randomRule(){
    return new RandomRule();
}
```

2.配置文件方式：在order-service的application.yml文件中，添加新的配置也可以修改规则：

```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则 

```

#### 饿汉加载

Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。

而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true # 开启饥饿加载 
    clients: userservice # 指定对userservice这个服务饥饿加载

```

#### 总结

1. Ribbon负载均衡规则
   1. 规则接口是IRule
   2. 默认实现是ZoneAvoidanceRule，根据zone选择服务列表，然后轮询
2. 负载均衡自定义方式
   1. 代码方式：配置灵活，但修改时需要重新打包发布
   2. 配置方式：直观，方便，无需重新打包发布，但是无法做全局配置
3. 饥饿加载
   1. 开启饥饿加载
   2. 指定饥饿加载的微服务名称

### Nacos

[Nacos](https://nacos.io/)是阿里巴巴的产品，现在是[SpringCloud](https://spring.io/projects/spring-cloud)中的一个组件。相比[Eureka](https://github.com/Netflix/eureka)功能更加丰富，在国内受欢迎程度较高。

#### 使用

看**Nacos安装指南.md**

#### 服务注册注册到Nacos

1.在cloud-demo父工程中添加spring-cloud-alilbaba的管理依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

2.注释掉order-service和user-service中原有的eureka依赖。

3.添加nacos的客户端依赖：

```xml
<!-- nacos客户端依赖 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

```

4.修改user-service&order-service中的application.yml文件，注释eureka地址，添加nacos地址：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848 #nacos服务端地址
```

5.启动并试：

![](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212437242.png)

#### 总结

1. Nacos服务搭建
   1. 下载安装包
   2. 解压
   3. 在bin目录下运行指令：startup.cmd -m standalone
2. Nacos服务注册或发现
   1. 引入nacos.discovery依赖
   2. 配置nacos地址spring.cloud.nacos.server-addr

![image-20221209212549705](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212549705.png)

![image-20221209212601627](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212601627.png)

#### 配置服务集群

1.修改application.yml，添加如下内容：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos 服务端地址
      discovery:
        cluster-name: HZ # 配置集群名称，也就是机房位置，例如：HZ，杭州
```

2.在Nacos控制台可以看到集群变化：

![image-20221209212713672](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209212713672.png)

我们再次复制一个user-service启动配置，添加属性：

```sh
-Dserver.port=8083 -Dspring.cloud.nacos.discovery.cluster-name=SH
```

配置如图所示：

![image-20221210190549672](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210190549672.png)

启动UserApplication3后再次查看nacos控制台：

![image-20221210190603434](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210190603434.png)

#### 总结

1.Nacos服务分级存储模型

1. 一级是服务，例如userservice
2. 二级是集群，例如杭州或上海
3. 三级是实例，例如杭州机房的某台部署了userservice的服务器

2.如何设置实例的集群属性

1. 修改application.yml文件，添加spring.cloud.nacos.discovery.cluster-name属性即可

#### 同集群优先的负载均衡

默认的`ZoneAvoidanceRule`并不能实现根据同集群优先来实现负载均衡。

因此Nacos中提供了一个`NacosRule`的实现，可以优先从同集群中挑选实例。

1）给order-service配置集群信息

修改order-service的application.yml文件，添加集群配置：

```sh
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```



2）修改负载均衡规则

修改order-service的application.yml文件，修改负载均衡规则：

```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则 
```

1.**NacosRule负载均衡策略**

①优先选择同集群服务实例列表

②本地集群找不到提供者，才去其它集群寻找，并且会报警告

③确定了可用实例列表后，再采用随机负载均衡挑选实例

#### 根据权重负载均衡

实际部署中会出现这样的场景：

- 服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求

Nacos提供了权重配置来控制访问频率，权重越大则访问频率越高

1.在Nacos控制台可以设置实例的权重值，首先选中实例后面的编辑按钮

![image-20221209213429876](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209213429876.png)

2.将权重设置为0.1，测试可以发现8081被访问到的频率大大降低

![image-20221209213433818](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221209213433818.png)

1.**实例的权重控制**

①Nacos控制台可以设置实例的权重值，0~1之间

②同集群内的多个实例，权重越高被访问的频率越高

**③权重设置为0则完全不会被访问**

## 环境隔离

Nacos提供了namespace来实现环境隔离功能。

- nacos中可以有多个namespace
- namespace下可以有group、service等
- 不同namespace之间相互隔离，例如不同namespace的服务互相不可见

### 创建namespace（命名空间）

默认情况下，所有service、data、group都在同一个namespace，名为public：

![image-20221210185401648](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185401648.png)

我们可以点击页面新增按钮，添加一个namespace：

![image-20221210185409976](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185409976.png)

然后，填写表单：

![image-20221210185425422](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185425422.png)

就能在页面看到一个新的namespace：

![image-20221210185433397](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185433397.png)



### 给微服务配置namespace

给微服务配置namespace只能通过修改配置来实现。

例如，修改order-service的application.yml文件：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ
        namespace: 492a7d5d-237b-46a1-a99a-fa8e98e4b0f9 # 命名空间，填ID
```



重启order-service后，访问控制台，可以看到下面的结果：

![image-20221210185513236](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185513236.png)

![image-20221210185524131](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185524131.png)

此时访问order-service，因为namespace不同，会导致找不到userservice，控制台会报错：

![image-20221210185541541](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210185541541.png)

## Nacos与Eureka的区别

#### Nacos的服务类型

Nacos的服务实例分为两种类型：

- 临时实例：如果实例宕机超过一定时间，会从服务列表剔除，默认的类型。

- 非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。



配置一个服务实例为永久实例：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例 也就是永久实例
```

Nacos和Eureka整体结构类似，服务注册、服务拉取、心跳等待，但是也存在一些差异：![image-20221210182010353](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221210182010353.png)

- Nacos与eureka的共同点
  - 都支持服务注册和服务拉取
  - 都支持服务提供者心跳方式做健康检测

- Nacos与Eureka的区别
  - Nacos支持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式
  - 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
  - Nacos支持服务列表变更的消息推送模式，服务列表更新更及时
  - Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式

## Nacos配置管理

### 统一配置管理

#### 配置热更新

在弹出表单中填写配置信息：

![image-20221211093350718](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211093350718.png)

![image-20221211093410558](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211093410558.png)

1. 引入Nacos的配置管理客户端依赖：

   ```xml
   <!--nacos配置管理依赖-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
   </dependency>
   
   ```

2. 在userservice中的resource目录添加一个bootstrap.yml文件，这个文件是引导文件，优先级高于application.yml

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
           file-extension: yaml
   
   ```

3. 我们在user-service中将pattern.dateformat这个属性注入到UserController中做测试：

   ```java
   @RestController
   @RequestMapping("/user")
   public class UserController {
       
       // 注入nacos中的配置属性
       @Value("${pattern.dateformat}")
       private String dateformat;
       
       // 编写controller，通过日期格式化器来格式化现在时间并返回
       @GetMapping("now")
       public String now(){
           return LocalDate.now().format(
               DateTimeFormatter.ofPattern(dateformat, Locale.CHINA)
           );
       }
       // ... 略
   }
   
   ```

   

将配置交给Nacos管理的步骤

①在Nacos中添加配置文件

②在微服务中引入nacos的config依赖

③在微服务中添加bootstrap.yml，配置nacos地址、当前环境、服务名称、文件后缀名。这些决定了程序启动时去nacos读取哪个文件

### 配置自动刷新

Nacos中的配置文件变更后，微服务无需重启就可以感知。不过需要通过下面两种配置实现：

- 方式一：在@Value注入的变量所在类上添加注解@RefreshScope

  ![image-20221211105014428](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211105014428.png)

- 方式二：使用@ConfigurationProperties注解

  ![](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211094014501.png)

Nacos配置更改后，微服务可以实现热更新，方式：

①通过@Value注解注入，结合@RefreshScope来刷新

②通过@ConfigurationProperties注入，自动刷新

注意事项：

- 不是所有的配置都适合放到配置中心，维护起来比较麻烦
- 建议将一些关键参数，需要运行时调整的参数放到nacos配置中心，一般都是自定义配置

### 多环境配置共享

微服务启动时会从nacos读取多个配置文件：

- **[spring.application.name]-[spring.profiles.active].yaml**，例如：**userservice-dev.yaml** **同dev环境的读取此配置文件**
- **[spring.application.name].yaml**，例如：**userservice.yaml**  **所有服务共享配置文件**
- 无论profile如何变化，[spring.application.name].yaml这个文件一定会加载，因此多环境共享配置可以写入这个文件

![image-20221211115247877](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211115247877.png)

#### 多种配置的优先级

服务名-profile.yaml 》服务名称.yaml 》本地配置

![image-20221211115357370](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211115357370.png)

#### 总结

微服务会从nacos读取的配置文件：

①[服务名]-[spring.profile.active].yaml，环境配置

②[服务名].yaml，默认配置，多环境共享

优先级：

①[服务名]-[环境].yaml >[服务名].yaml > 本地配置

## Feign远程调用

先来看我们以前利用RestTemplate发起远程调用的代码：

![image-20221211153311618](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211153311618.png)

存在下面的问题：

- 代码可读性差，编程体验不统一
- 参数复杂URL难以维护



Feign是一个声明式的http客户端，官方地址：https://github.com/OpenFeign/feign

其作用就是帮助我们优雅的实现http请求的发送，解决上面提到的问题。

#### Feign替代RestTemplate

Fegin的使用步骤如下：

##### 1）引入依赖

我们在order-service服务的pom文件中引入feign的依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

##### 2）添加注解

在order-service的启动类添加注解开启Feign的功能：

![image-20221211153456467](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211153456467.png)

##### 3）编写Feign的客户端

在order-service中新建一个接口，内容如下：

```java
package cn.itcast.order.client;

import cn.itcast.order.pojo.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient("userservice")
public interface UserClient {
    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

这个客户端主要是基于SpringMVC的注解来声明远程调用的信息，比如：

- 服务名称：userservice
- 请求方式：GET
- 请求路径：/user/{id}
- 请求参数：Long id
- 返回值类型：User

这样，Feign就可以帮助我们发送http请求，无需自己使用RestTemplate来发送了。

##### 4）测试

修改order-service中的OrderService类中的queryOrderById方法，使用Feign客户端代替RestTemplate：

![image-20221211153521054](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211153521054.png)

##### 5）总结

使用Feign的步骤：

① 引入依赖

② 添加@EnableFeignClients注解

③ 编写FeignClient接口

④ 使用FeignClient中定义的方法代替RestTemplate

#### 自定义配置

Feign可以支持很多的自定义配置，如下表所示：

| 类型                   | 作用             | 说明                                                   |
| ---------------------- | ---------------- | ------------------------------------------------------ |
| **feign.Logger.Level** | 修改日志级别     | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL         |
| feign.codec.Decoder    | 响应结果的解析器 | http远程调用的结果做解析，例如解析json字符串为java对象 |
| feign.codec.Encoder    | 请求参数编码     | 将请求参数编码，便于通过http请求发送                   |
| feign. Contract        | 支持的注解格式   | 默认是SpringMVC的注解                                  |
| feign. Retryer         | 失败重试机制     | 请求失败的重试机制，默认是没有，不过会使用Ribbon的重试 |

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的@Bean覆盖默认Bean即可。



下面以日志为例来演示如何自定义配置。

##### 配置文件方式

基于配置文件修改feign的日志级别可以针对单个服务：

```yaml
feign:  
  client:
    config: 
      userservice: # 针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```

也可以针对所有服务：

```yaml
feign:  
  client:
    config: 
      default: # 这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```



而日志的级别分为四种：

- **NONE：不记录任何日志信息，这是默认值。**
- **BASIC：仅记录请求的方法，URL以及响应状态码和执行时间**
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。



##### Java代码方式

也可以基于Java代码来修改日志级别，先声明一个类，然后声明一个Logger.Level的对象：

```java
public class DefaultFeignConfiguration  {
    @Bean
    public Logger.Level feignLogLevel(){
        return Logger.Level.BASIC; // 日志级别为BASIC
    }
}
```



如果要**全局生效**，将其放到启动类的@EnableFeignClients这个注解中：

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration .class) 
```



如果是**局部生效**，则把它放到对应的@FeignClient这个注解中：

```java
@FeignClient(value = "userservice", configuration = DefaultFeignConfiguration .class) 
```

#### Feign使用优化

Feign底层发起http请求，依赖于其它的框架。其底层客户端实现包括：

•URLConnection：默认实现，不支持连接池

•Apache HttpClient ：支持连接池

•OKHttp：支持连接池



因此提高Feign的性能主要手段就是使用**连接池**代替默认的URLConnection。



这里我们用Apache的HttpClient来演示。

1）引入依赖

在order-service的pom文件中引入Apache的HttpClient依赖：

```xml
<!--httpClient的依赖 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```



2）配置连接池

在order-service的application.yml中添加配置：

```yaml
feign:
  client:
    config:
      default: # default全局的配置
        loggerLevel: BASIC # 日志级别，BASIC就是基本的请求和响应信息
  httpclient:
    enabled: true # 开启feign对HttpClient的支持
    max-connections: 200 # 最大的连接数
    max-connections-per-route: 50 # 每个路径的最大连接数
```



接下来，在FeignClientFactoryBean中的loadBalance方法中打断点：

![image-20221211162203537](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162203537.png)

Debug方式启动order-service服务，可以看到这里的client，底层就是Apache HttpClient：

![image-20221211162215557](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162215557.png)

总结，Feign的优化：

1.日志级别尽量用basic

2.使用HttpClient或OKHttp代替URLConnection

①  引入feign-httpClient依赖

②  配置文件开启httpClient功能，设置连接池参数

#### 最佳实践

所谓最近实践，就是使用过程中总结的经验，最好的一种使用方式。

自习观察可以发现，Feign的客户端与服务提供者的controller代码非常相似：

feign客户端：

![image-20221211162250812](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162250812.png)

UserController：

![image-20221211162304234](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162304234.png)

##### 继承方式

一样的代码可以通过继承来共享：

1）定义一个API接口，利用定义方法，并基于SpringMVC注解做声明。

2）Feign客户端和Controller都集成改接口

![image-20221211162325433](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162325433.png)

优点：

- 简单
- 实现了代码共享

缺点：

- 服务提供方、服务消费方紧耦合

- 参数列表中的注解映射并不会继承，因此Controller中必须再次声明方法、参数列表、注解

##### 抽取方式

将Feign的Client抽取为独立模块，并且把接口有关的POJO、默认的Feign配置都放到这个模块中，提供给所有消费者使用。

例如，将UserClient、User、Feign的默认配置都抽取到一个feign-api包中，所有微服务引用该依赖包，即可直接使用。

![image-20221211162346631](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162346631.png)

##### 实现基于抽取的最佳实践

###### 1）抽取

首先创建一个module，命名为feign-api：

![image-20221211162405610](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162405610.png)

项目结构：

![image-20221211162430680](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162430680.png)

在feign-api中然后引入feign的starter依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

然后，order-service中编写的UserClient、User、DefaultFeignConfiguration都复制到feign-api项目中

![image-20221211162441637](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162441637.png)

###### 2）在order-service中使用feign-api

首先，删除order-service中的UserClient、User、DefaultFeignConfiguration等类或接口。

在order-service的pom文件中中引入feign-api的依赖：

```xml
<dependency>
    <groupId>cn.itcast.demo</groupId>
    <artifactId>feign-api</artifactId>
    <version>1.0</version>
</dependency>
```

修改order-service中的所有与上述三个组件有关的导包部分，改成导入feign-api中的包

###### 3）重启测试

重启后，发现服务报错了：

![image-20221211162500029](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211162500029.png)

这是因为UserClient现在在cn.itcast.feign.clients包下，

而order-service的@EnableFeignClients注解是在cn.itcast.order包下，不在同一个包，无法扫描到UserClient。

###### 4）解决扫描包问题

方式一：

指定Feign应该扫描的包：

```java
@EnableFeignClients(basePackages = "cn.itcast.feign.clients")
```

方式二：

指定需要加载的Client接口：

```java
@EnableFeignClients(clients = {UserClient.class})
```

## Gateway服务网关

Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。



为什么需要网关

Gateway网关是我们服务的守门神，所有微服务的统一入口。

网关的**核心功能特性**：

- 请求路由
- 权限控制
- 限流

架构图：

![image-20221211204140376](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211204140376.png)

**权限控制**：网关作为微服务入口，需要校验用户是是否有请求资格，如果没有则进行拦截。

**路由和负载均衡**：一切请求都必须先经过gateway，但网关不处理业务，而是根据某种规则，把请求转发到某个微服务，这个过程叫做路由。当然路由的目标服务有多个时，还需要做负载均衡。

**限流**：当请求流量过高时，在网关中按照下流的微服务能够接受的速度来放行请求，避免服务压力过大。



在SpringCloud中网关的实现包括两种：

- gateway
- zuul

Zuul是基于Servlet的实现，属于阻塞式编程。而SpringCloudGateway则是基于Spring5中提供的WebFlux，属于响应式编程的实现，具备更好的性能。





#### gateway快速入门

下面，我们就演示下网关的基本路由功能。基本步骤如下：

1. 创建SpringBoot工程gateway，引入网关依赖
2. 编写启动类
3. 编写基础配置和路由规则
4. 启动网关服务进行测试



##### 1）创建gateway服务，引入依赖

创建服务：

![image-20221211204234192](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211204234192.png)

引入依赖：

```xml
<!--网关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!--nacos服务发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```



##### 2）编写启动类

```java
package cn.itcast.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GatewayApplication {

	public static void main(String[] args) {
		SpringApplication.run(GatewayApplication.class, args);
	}
}
```



##### 3）编写基础配置和路由规则

创建application.yml文件，内容如下：

```yaml
server:
  port: 10010 # 网关端口
spring:
  application:
    name: gateway # 服务名称
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos地址
    gateway:
      routes: # 网关路由配置
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
```



我们将符合`Path` 规则的一切请求，都代理到 `uri`参数指定的地址。

本例中，我们将 `/user/**`开头的请求，代理到`lb://userservice`，lb是负载均衡，根据服务名拉取服务列表，实现负载均衡。



##### 4）重启测试

重启网关，访问http://localhost:10010/user/1时，符合`/user/**`规则，请求转发到uri：http://userservice/user/1，得到了结果：

![image-20221211204803397](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211204803397.png)

##### 5）网关路由的流程图

整个访问的流程如下：

![image-20221211204814018](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211204814018.png)

总结：

网关搭建步骤：

1. 创建项目，引入nacos服务发现和gateway依赖

2. 配置application.yml，包括服务基本信息、nacos地址、路由

路由配置包括：

1. 路由id：路由的唯一标示

2. 路由目标（uri）：路由的目标地址，http代表固定地址，lb代表根据服务名负载均衡

3. 路由断言（predicates）：判断路由的规则，

4. 路由过滤器（filters）：对请求或响应做处理

#### 断言工厂

我们在配置文件中写的断言规则只是字符串，这些字符串会被Predicate Factory读取并处理，转变为路由判断的条件

例如Path=/user/**是按照路径匹配，这个规则是由

`org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory`类来

处理的，像这样的断言工厂在SpringCloudGateway还有十几个:

| **名称**   | **说明**                       | **示例**                                                     |
| ---------- | ------------------------------ | ------------------------------------------------------------ |
| After      | 是某个时间点后的请求           | -  After=2037-01-20T17:42:47.789-07:00[America/Denver]       |
| Before     | 是某个时间点之前的请求         | -  Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]       |
| Between    | 是某两个时间点之前的请求       | -  Between=2037-01-20T17:42:47.789-07:00[America/Denver],  2037-01-21T17:42:47.789-07:00[America/Denver] |
| Cookie     | 请求必须包含某些cookie         | - Cookie=chocolate, ch.p                                     |
| Header     | 请求必须包含某些header         | - Header=X-Request-Id, \d+                                   |
| Host       | 请求必须是访问某个host（域名） | -  Host=**.somehost.org,**.anotherhost.org                   |
| Method     | 请求方式必须是指定方式         | - Method=GET,POST                                            |
| Path       | 请求路径必须符合指定规则       | - Path=/red/{segment},/blue/**                               |
| Query      | 请求参数必须包含指定参数       | - Query=name, Jack或者-  Query=name                          |
| RemoteAddr | 请求者的ip必须是指定范围       | - RemoteAddr=192.168.1.1/24                                  |
| Weight     | 权重处理                       |                                                              |



我们只需要掌握Path这种路由工程就可以了。



#### 过滤器工厂

GatewayFilter是网关中提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理![image-20221211204845319](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211204845319.png)

#### 路由过滤器的种类

Spring提供了31种不同的路由过滤器工厂。例如：

| **名称**             | **说明**                     |
| -------------------- | ---------------------------- |
| AddRequestHeader     | 给当前请求添加一个请求头     |
| RemoveRequestHeader  | 移除请求中的一个请求头       |
| AddResponseHeader    | 给响应结果中添加一个响应头   |
| RemoveResponseHeader | 从响应结果中移除有一个响应头 |
| RequestRateLimiter   | 限制请求的流量               |



#### 请求头过滤器

下面我们以AddRequestHeader 为例来讲解。

> **需求**：给所有进入userservice的请求添加一个请求头：Truth=itcast is freaking awesome!



只需要修改gateway服务的application.yml文件，添加路由过滤即可：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/** 
        filters: # 过滤器
        - AddRequestHeader=Truth, Itcast is freaking awesome! # 添加请求头
```

当前过滤器写在userservice路由下，因此仅仅对访问userservice的请求有效。





#### 默认过滤器

如果要对所有的路由都生效，则可以将过滤器工厂写到default下。格式如下：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/**
      default-filters: # 默认过滤项
      - AddRequestHeader=Truth, Itcast is freaking awesome! 
```



##### 总结

过滤器的作用是什么？

① 对路由的请求或响应做加工处理，比如添加请求头

② 配置在路由下的过滤器只对当前路由的请求生效

defaultFilters的作用是什么？

① 对所有路由都生效的过滤器



#### 全局过滤器

上一节学习的过滤器，网关提供了31种，但每一种过滤器的作用都是固定的。如果我们希望拦截请求，做自己的业务逻辑则没办法实现。

#### 全局过滤器作用

全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与GatewayFilter的作用一样。区别在于GatewayFilter通过配置定义，处理逻辑是固定的；而GlobalFilter的逻辑需要自己写代码实现。

定义方式是实现GlobalFilter接口。

```java
public interface GlobalFilter {
    /**
     *  处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
     *
     * @param exchange 请求上下文，里面可以获取Request、Response等信息
     * @param chain 用来把请求委托给下一个过滤器 
     * @return {@code Mono<Void>} 返回标示当前过滤器业务结束
     */
    Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```



在filter中编写自定义逻辑，可以实现下列功能：

- 登录状态判断
- 权限校验
- 请求限流等





#### 自定义全局过滤器

需求：定义全局过滤器，拦截请求，判断请求的参数是否满足下面条件：

- 参数中是否有authorization，

- authorization参数值是否为admin

如果同时满足则放行，否则拦截



实现：

在gateway中定义一个过滤器：

```java
package cn.itcast.gateway.filters;

import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Order(-1)
@Component
public class AuthorizeFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 1.获取请求参数
        MultiValueMap<String, String> params = exchange.getRequest().getQueryParams();
        // 2.获取authorization参数
        String auth = params.getFirst("authorization");
        // 3.校验
        if ("admin".equals(auth)) {
            // 放行
            return chain.filter(exchange);
        }
        // 4.拦截
        // 4.1.禁止访问，设置状态码
        exchange.getResponse().setStatusCode(HttpStatus.FORBIDDEN);
        // 4.2.结束处理
        return exchange.getResponse().setComplete();
    }
}
```





#### 过滤器执行顺序

请求进入网关会碰到三类过滤器：当前路由的过滤器、DefaultFilter、GlobalFilter

请求路由后，会将当前路由过滤器和DefaultFilter、GlobalFilter，合并到一个过滤器链（集合）中，排序后依次执行每个过滤器：

![image-20221211205015627](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221211205015627.png)

排序的规则是什么呢？

- 每一个过滤器都必须指定一个int类型的order值，**order值越小，优先级越高，执行顺序越靠前**。
- GlobalFilter通过实现Ordered接口，或者添加@Order注解来指定order值，由我们自己指定
- 路由过滤器和defaultFilter的order由Spring指定，默认是按照声明顺序从1递增。
- 当过滤器的order值一样时，会按照 defaultFilter > 路由过滤器 > GlobalFilter的顺序执行。



详细内容，可以查看源码：

`org.springframework.cloud.gateway.route.RouteDefinitionRouteLocator#getFilters()`方法是先加载defaultFilters，然后再加载某个route的filters，然后合并。



`org.springframework.cloud.gateway.handler.FilteringWebHandler#handle()`方法会加载全局过滤器，与前面的过滤器合并后根据order排序，组织过滤器链



#### 跨域问题



##### 什么是跨域问题

跨域：域名不一致就是跨域，主要包括：

- 域名不同： www.taobao.com 和 www.taobao.org 和 www.jd.com 和 miaosha.jd.com

- 域名相同，端口不同：localhost:8080和localhost8081

跨域问题：浏览器禁止请求的发起者与服务端发生跨域ajax请求，请求被浏览器拦截的问题



##### 解决跨域问题

在gateway服务的application.yml文件中，添加下面的配置：

```yaml
spring:
  cloud:
    gateway:
      # 。。。
      globalcors: # 全局的跨域处理
        add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题
        corsConfigurations:
          '[/**]':
            allowedOrigins: # 允许哪些网站的跨域请求 
              - "http://localhost:8090"
            allowedMethods: # 允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允许在请求中携带的头信息
            allowCredentials: true # 是否允许携带cookie
            maxAge: 360000 # 这次跨域检测的有效期
```

## RabbitMQ

#### 使用方法

##### 生产者

1.导入依赖

```xml
<!-- rabbitMQ -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
```

2.修改配置

```yaml
spring:
  application:
    name: itemservice
  rabbitmq:
    host: 192.168.60.128
    port: 5672
    virtual-host: /
    username: itcast
    password: 123321
```

3.生产消息（发送消息）

```java
@Autowired
private RabbitTemplate rabbitTemplate;
rabbitTemplate.convertAndSend(交换机名称, 队列key, 数据);
```



##### 消费者

同生产者1 2 

3.消费消息（处理消息）

```java
	@RabbitListener(bindings = @QueueBinding(
            value = @Queue(队列名称),
            exchange = @Exchange(name = 交换机名称,type = ExchangeTypes.TOPIC（交换机类型）),
            key = 队列key
    ))
    public void insertESItem(Long id){
        esHmallService.insertByItem(id);
    }

	
	@Autowired
    private ItemClient itemClient;

    @Autowired
    private RestHighLevelClient client;

    @Override
    public void insertByItem(Long id) {
        try {
            log.info("监听到添加："+id);
            
            Item item = itemClient.getItemById(id);
            
			ItemDTO itemDTO = new ItemDTO(item);
            
            IndexRequest request = new IndexRequest("item");
            request.id(id.toString()).source(JSON.toJSONString(itemDTO), XContentType.JSON);
            
            client.index(request, RequestOptions.DEFAULT);
            
            log.info("已添加："+id);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

#### 消息可靠性问题

- 发送时丢失：
  - 生产者发送的消息未送达exchange
  - 消息到达exchange后未到达queue

- MQ宕机，queue将消息丢失

- consumer接收到消息后未消费就宕机

##### 生产者确认机制

RabbitMQ提供了publisher confirm机制来避免消息发送到MQ过程中丢失。消息发送到MQ以后，会返回一个结果给发送者，表示消息是否处理成功。结果有两种请求：

- publisher-confirm，发送者确认
  - 消息成功投递到交换机，返回ack
  - 消息未投递到交换机，返回nack

- publisher-return，发送者回执
  - 消息投递到交换机了，但是没有路由到队列。返回ACK，及路由失败原因。

![](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226203150918.png)

**确认机制发送消息时，需要给每个消息设置一个全局唯一id，以区分不同消息，避免ack冲突**

##### SpringAMQP实现生产者确认

1.修改配置

```yaml
spring:
  rabbitmq:
    publisher-confirm-type: correlate
    publisher-returns: true 
    template:
      mandatory: true

配置说明：
publish-confirm-type：开启publisher-confirm，这里支持两种类型：
simple：同步等待confirm结果，直到超时
correlated：异步回调，定义ConfirmCallback，MQ返回结果时会回调这个ConfirmCallback
publish-returns：开启publish-return功能，同样是基于callback机制，不过是定义ReturnCallback
template.mandatory：定义消息路由失败时的策略。true，则调用ReturnCallback；false：则直接丢弃消息

```

2.每个RabbitTemplate只能配置一个ReturnCallback，因此需要在项目启动过程中配置：

```java
@Slf4j
@Configuration
public class CommonConfig implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        // 获取RabbitTemplate
        RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
        // 设置ReturnCallback
        rabbitTemplate.setReturnCallback((message, replyCode, replyText, exchange, routingKey) -> {
            log.info("消息发送失败，应答码{}，原因{}，交换机{}，路由键{},消息{}", 
                     replyCode, replyText, exchange, routingKey, message.toString());
        });
    }
}

```

3.发送消息，指定消息ID、消息ConfirmCallback

```java
@Test
public void testSendMessage2SimpleQueue() throws InterruptedException {
    // 消息体
    String message = "hello, spring amqp!";
    // 消息ID，需要封装到CorrelationData中
    CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
    // 添加callback
    correlationData.getFuture().addCallback(
        result -> {
            if(result.isAck()){ 
                // ack，消息成功
                log.debug("消息发送成功, ID:{}", correlationData.getId());
            }else{
                // nack，消息失败
                log.error("消息发送失败, ID:{}, 原因{}",correlationData.getId(), result.getReason());
            }
        },
        ex -> log.error("消息发送异常, ID:{}, 原因{}",correlationData.getId(),ex.getMessage())
    );
    // 发送消息
    rabbitTemplate.convertAndSend("amq.direct", "simple", message, correlationData);
}

```

#### 消息持久化

1.交换机持久化

```java
@Bean
public DirectExchange simpleExchange(){
    // 三个参数：交换机名称、是否持久化、当没有queue与其绑定时是否自动删除 
    return new DirectExchange("simple.direct", true, false);
}

```

2.队列持久化

```java
@Bean
public Queue simpleQueue(){
    //return new Queue(“simple.queue”,true);   
   //使用QueueBuilder构建队列，durable就是持久化的
    return QueueBuilder.durable("simple.queue").build();
}


```

3.消息持久化，SpringAMQP中的的消息默认是持久的，可以通过MessageProperties中的DeliveryMode来指

```java
Message msg = MessageBuilder
    .withBody(message.getBytes(StandardCharsets.UTF_8)) // 消息体
    .setDeliveryMode(MessageDeliveryMode.PERSISTENT) // 持久化 
    .build();

```

3.1消息持久化注解方式：可以通过指定注解属性，实现交换机、队列的持久化配置

![image-20221226204252348](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226204252348.png)

#### 消费者确认

RabbitMQ支持消费者确认机制，即：消费者处理消息后可以向MQ发送ack回执，MQ收到ack回执后才会删除该消息。而SpringAMQP则允许配置三种确认模式：

- manual：手动ack，需要在业务代码结束后，调用api发送ack。
- auto：自动ack，由spring监测listener代码是否出现异常，没有异常则返回ack；抛出异常则返回nack
- none：关闭ack，MQ假定消费者获取消息后会成功处理，因此消息投递后立即被删除

配置方式是修改application.yml文件，添加下面配置：

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1
        acknowledge-mode: none # none，关闭ack；manual，手动ack；auto：自动ack

```

#### 消费者失败重试

![image-20221226204854366](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226204854366.png)

##### 失败消息处理策略

在开启重试模式后，重试次数耗尽，如果消息依然失败，则需要有MessageRecoverer接口来处理，它包含三种不同的实现：

- RejectAndDontRequeueRecoverer：重试耗尽后，直接reject，丢弃消息。默认就是这种方式
- ImmediateRequeueMessageRecoverer：重试耗尽后，返回nack，消息重新入队
- RepublishMessageRecoverer：重试耗尽后，将失败消息投递到指定的交换机

```java
//测试下RepublishMessageRecoverer处理模式：
//首先，定义接收失败消息的交换机、队列及其绑定关系：
@Bean
public DirectExchange errorMessageExchange(){
    return new DirectExchange("error.direct");
}
@Bean
public Queue errorQueue(){
    return new Queue("error.queue", true); 
}
@Bean
public Binding errorBinding(){
    return BindingBuilder.bind(errorQueue()).to(errorMessageExchange()).with("error");
}
//然后，定义RepublishMessageRecoverer：
@Bean
public MessageRecoverer republishMessageRecoverer(RabbitTemplate rabbitTemplate){
    return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error"); 
}


```

#### 如何确保RabbitMQ消息的可靠性？

- 开启生产者确认机制，确保生产者的消息能到达队列
- 开启持久化功能，确保消息未消费前在队列中不会丢失
- 开启消费者确认机制为auto，由spring确认消息处理成功后完成ack
- 开启消费者失败重试机制，并设置MessageRecoverer，多次重试失败后将消息投递到异常交换机，交由人工处理

#### 死信交换机

当一个队列中的消息满足下列情况之一时，可以成为死信（dead letter）：

- 消费者使用basic.reject或 basic.nack声明消费失败，并且消息的requeue参数设置为false
- 消息是一个过期消息，超时无人消费
- 要投递的队列消息堆积满了，最早的消息可能成为死信 （最早接收到的消息就会变成死信，被丢弃）

如果该队列配置了dead-letter-exchange属性，指定了一个交换机，那么队列中的死信就会投递到这个交换机中，而这个交换机称为死信交换机（Dead Letter Exchange，简称DLX）。

**如何给队列绑定死信交换机**？

- 给队列设置dead-letter-exchange属性，指定一个交换机
- 给队列设置dead-letter-routing-key属性，设置死信交换机与死信队列的RoutingKey

##### TTL

TTL，也就是Time-To-Live。如果一个队列中的消息TTL结束仍未消费，则会变为死信，ttl超时分为两种情况：

- 消息所在的队列设置了存活时间
- 消息本身设置了存活时间

![image-20221226205604855](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226205604855.png)

我们声明一组死信交换机和队列，基于注解方式

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "dl.queue", durable = "true"),
    exchange = @Exchange(name = "dl.direct"),
    key = "dl" 
))
public void listenDlQueue(String msg){
    log.info("接收到 dl.queue的延迟消息：{}", msg);
}

//要给队列设置超时时间，需要在声明队列时配置x-message-ttl属性：
@Bean
public DirectExchange ttlExchange(){
    return new DirectExchange("ttl.direct");
}
@Bean
public Queue ttlQueue(){
    return QueueBuilder.durable("ttl.queue") // 指定队列名称，并持久化 
        .ttl(10000) // 设置队列的超时时间，10秒
        .deadLetterExchange("dl.direct") // 指定死信交换机
            .deadLetterRoutingKey("dl") // 指定死信RoutingKey
        .build();
}
@Bean
public Binding simpleBinding(){
    return BindingBuilder.bind(ttlQueue()).to(ttlExchange()).with("ttl");
}

//发送消息时，给消息本身设置超时时间
@Test
public void testTTLMsg() {
    // 创建消息
    Message message = MessageBuilder
        .withBody("hello, ttl message".getBytes(StandardCharsets.UTF_8))
        .setExpiration("5000") 
        .build();
    // 消息ID，需要封装到CorrelationData中
    CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
    // 发送消息
    rabbitTemplate.convertAndSend("ttl.direct", "ttl", message, correlationData);
}
```

**死信加TTL就是这个消息没有在规定的时间内被消费掉，就会转发到处理死信的监听器上**

**配置消息超时的两种方式是**？

- 给队列设置ttl属性，进入队列后超过ttl时间的消息变为死信
- 给消息设置ttl属性，队列接收到消息超过ttl时间后变为死信
- 两者共存时，以时间短的ttl为准

**如何实现发送一个消息20秒后消费者才收到消息？**

- 给消息的目标队列指定死信交换机
- 消费者监听与死信交换机绑定的队列
- 发送消息时给消息设置ttl为20秒

#### 延迟队列

利用TTL结合死信交换机，我们实现了消息发出后，消费者延迟收到消息的效果。这种消息模式就称为延迟队列（Delay Queue）模式。

延迟队列的使延迟用场景包括：

- 延迟发送短信
- 用户下单，如果用户在15 分钟内未支付，则自动取消
- 预约工作会议，20分钟后自动通知所有参会人员

##### 延迟交换机插件

E:\Java课程\Chapter44-项目实战\资料\mq延迟队列\延迟队列.md

##### SpringAMQP使用延迟交换机插件

1.监听延迟队列

```java
	@RabbitListener(bindings = @QueueBinding(
            value = @Queue(MqConstant.ORDER_STATUS_QUEUE_NAME),
            exchange = @Exchange(name = MqConstant.ORDER_EXCHANGE, delayed = "true"),
            key = MqConstant.ORDER_STATUS_QUEUE_KEY
    ))
    public void mateOrderStatus(String id) {
        log.info("监听到延迟消息，已消费{}",id);
    }
```

![image-20221226210439226](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226210439226.png)

2.向延迟队列发送消息

![image-20221226210606315](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226210606315.png)

#### 消息堆积问题

当生产者发送消息的速度超过了消费者处理消息的速度，就会导致队列中的消息堆积，直到队列存储消息达到上限。最早接收到的消息，可能就会成为死信，会被丢弃，这就是消息堆积问题。

**解决消息堆积有三种种思路**：

- 增加更多消费者，提高消费速度
- 在消费者内开启线程池加快消息处理速度
- 扩大队列容积，提高堆积上限

#### 惰性队列

从RabbitMQ的3.6.0版本开始，就增加了Lazy Queues的概念，也就是惰性队列。

惰性队列的特征如下：

- 接收到消息后直接存入磁盘而非内存
- 消费者要消费消息时才会从磁盘中读取并加载到内存
- 支持数百万条的消息存储

而要设置一个队列为惰性队列，只需要在声明队列时，指定x-queue-mode属性为lazy即可。可以通过命令行将一个运行中的队列修改为惰性队列：

```
rabbitmqctl set_policy Lazy "^lazy-queue$" '{"queue-mode":"lazy"}' --apply-to queues  
```

![image-20221226210757048](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226210757048.png)

#### 小结

惰性队列的消息直接存到硬盘上，适用于高并发

普通队列会先在内存中存储，然后在硬盘上持久化

![image-20221227185826300](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221227185826300.png)

**消息堆积问题的解决方案**？

- 队列上绑定多个消费者，提高消费速度
- 给消费者开启线程池，提高消费速度
- 使用惰性队列，可以再mq中保存更多消息

**惰性队列的优点有哪些**？

- 基于磁盘存储，消息上限高
- 没有间歇性的page-out（没有从内存转到硬盘），性能比较稳定

**惰性队列的缺点有哪些**？

- 基于磁盘存储，消息时效性会降低
- 性能受限于磁盘的IO

#### 集群分类

RabbitMQ的是基于Erlang语言编写，而Erlang又是一个面向并发的语言，天然支持集群模式。RabbitMQ的集群有两种模式：

- **普通集群**：是一种分布式集群，将队列分散到集群的各个节点，从而提高整个集群的并发能力。
- **镜像集群**：是一种主从集群，普通集群的基础上，添加了主从备份功能，提高集群的数据可用性。

镜像集群虽然支持主从，但主从同步并不是强一致的，某些情况下可能有数据丢失的风险。因此在RabbitMQ的3.8版本以后，推出了新的功能：**仲裁队列**来代替镜像集群，底层采用Raft协议确保主从的数据一致性。

##### 普通集群

普通集群，或者叫标准集群（classic cluster），具备下列特征：

- 会在集群的各个节点间共享部分数据，包括：**交换机、队列元信息**。**不包含队列中的消息**。
- 当访问集群某节点时，如果队列不在该节点，会从数据所在节点传递到当前节点并返回
- **队列所在节点宕机，队列中的消息就会丢失**（消息数据不会同步到子节点，只有在运行时子节点才可以访问到数据）

##### 镜像集群

镜像集群：本质是主从模式，具备下面的特征：

- 交换机、队列、队列中的消息会在各个mq的镜像节点之间同步备份。
- 创建队列的节点被称为该队列的**主节点，**备份到的其它节点叫做该队列的**镜像**节点。
- 一个队列的主节点可能是另一个队列的镜像节点
- **所有操作都是主节点完成，然后同步给镜像节点（但不保证数据一致，可能数据丢失一部分）**
- 主宕机后，镜像节点会替代成新的主

详细的搭建步骤可以参考课前资料：RabbitMQ部署指南.md

#### 仲裁队列

仲裁队列：仲裁队列是3.8版本以后才有的新功能，用来替代镜像队列，具备下列特征：

- 与镜像队列一样，都是主从模式，支持主从数据同步
- 使用非常简单，没有复杂的配置
- 主从同步基于Raft协议，**强一致（主从数据一定一致）**

SpringAMQP创建仲裁队列

```java
@Bean
public Queue quorumQueue() {
    return QueueBuilder
        .durable("quorum.queue") // 持久化 
        .quorum() // 仲裁队列
        .build();
}

```

SpringAMQP连接集群

```yaml
spring:
  rabbitmq:
    addresses: 192.168.150.105:8071, 192.168.150.105:8072, 192.168.150.105:8073
    username: itcast 
    password: 123321
    virtual-host: /
# 消费者和生产者的ip地址都要换成addresses不能使用host和port来配置了
```



## Redis

#### RDB

Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把数据记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据

快照文件称为RDB文件，默认是保存在**当前运行目录**

**Redis停机时会执行一次RDB，由Redis主进程来执行RDB，会阻塞所有命令**

##### RDB触发机制

在redis.conf文件中找到以下

```shell
# 900秒内，如果至少有1个key被修改，则执行bgsave ， 如果是save "" 则表示禁用RDB
save 900 1  
save 300 10  
save 60 10000 
```

其他配置在redis.conf文件中设置

```
# 是否压缩 ,建议不开启，压缩也会消耗cpu，磁盘的话不值钱
rdbcompression yes

# RDB文件名称
dbfilename dump.rdb  

# 文件保存的路径目录
dir ./ 

```

bgsave开始时会fork主进程得到的子进程，子进程共享主进程的内存数据。完成fork后读取内存数据并写入RDB文件

fork采用的是copy-on-write技术：

- 当主进程执行读操作时，访问共享数据
- 当主进程执行写操作时，则会拷贝一份数据，执行写操作

##### RDB方式bgsave的基本流程？

- fork主进程得到一个子进程，共享内存空间
- 子进程读取内存数据并写入新的RDB文件
- 用新RDB文件替换旧的RDB文件。

##### RDB会在什么时候执行？save 60 1000代表什么含义？

- 默认是服务停止时。
- 代表60秒内至少执行1000次修改则触发RDB

##### RDB的缺点？

- RDB执行间隔时间长，两次RDB之间写入数据有丢失的风险
- fork子进程、压缩、写出RDB文件都比较耗时

#### AOF

Append Only File（追加文件）Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

![image-20221225220753331](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225220753331.png)

AOF默认关闭，需要修改redis。conf配置文件来开启AOF

```shell
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"

#记录的频率
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no

```

| **配置项** | **刷盘时机** | **优点**               | **缺点**                     |
| ---------- | ------------ | ---------------------- | ---------------------------- |
| Always     | 同步刷盘     | 可靠性高，几乎不丢数据 | 性能影响大                   |
| everysec   | 每秒刷盘     | 性能适中               | 最多丢失1秒数据              |
| no         | 操作系统控制 | 性能最好               | 可靠性较差，可能丢失大量数据 |

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过**执行bgrewriteaof命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。**

Redis也会在触发阈值是自动去重AOF文件。阈值也可以在redis.conf中配置

```shell
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写 
auto-aof-rewrite-min-size 64mb 

```

|                | **RDB**                                      | **AOF**                                                  |
| -------------- | -------------------------------------------- | -------------------------------------------------------- |
| 持久化方式     | 定时对整个内存做快照                         | 记录每一次执行的命令                                     |
| 数据完整性     | 不完整，两次备份之间会丢失                   | 相对完整，取决于刷盘策略                                 |
| 文件大小       | 会有压缩，文件体积小                         | 记录命令，文件体积很大                                   |
| 宕机恢复速度   | 很快                                         | 慢                                                       |
| 数据恢复优先级 | 低，因为数据完整性不如AOF                    | 高，因为数据完整性更高                                   |
| 系统资源占用   | 高，大量CPU和内存消耗                        | 低，主要是磁盘IO资源  但AOF重写时会占用大量CPU和内存资源 |
| 使用场景       | 可以容忍数分钟的数据丢失，追求更快的启动速度 | 对数据安全性要求较高的场景                               |

#### Redis集群

搭建主从架构

单节点Redis的并发能力是有上限的，要进一步提高Redis的并发能力，就需要搭建主从集群，实现读写分离

##### 搭建参考 

Redis集群.md文档

![image-20221225221940144](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225221940144.png)

##### 数据同步原理

主从第一次同步是全量同步

![image-20221225222153050](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225222153050.png)

master如何判断slave是不是第一次来同步数据？

- Replication Id：简称replid，是数据集的标记，id一致则说明是同一数据集。每一个master都有唯一的replid，slave则会继承master节点的replid
- offset：偏移量，随着记录在repl_baklog中的数据增多而逐渐增大。slave完成同步时也会记录当前同步的offset。如果slave的offset小于master的offset，说明slave数据落后于master，需要更新。

因此**slave做数据同步，必须向master声明自己的replication id 和offset**，master才可以判断到底需要同步哪些数据

![image-20221225222449178](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225222449178.png)

**简述全量同步的流程**？

- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将完整内存数据生成RDB，发送RDB到slave
- slave清空本地数据，加载master的RDB
- master将RDB期间的命令记录在repl_baklog，并持续将log中的命令发送给slave
- slave执行接收到的命令，保持与master之间的同步

repl_baklog大小有上限，写满后会覆盖最早的数据。如果slave断开时间过久，导致尚未备份的数据被覆盖，则无法基于log做增量同步，只能再次全量同步。

**可以从以下几个方面来优化Redis主从就集群：**

- 在master中配置repl-diskless-sync yes启用无磁盘复制，避免全量同步时的磁盘IO。
- Redis单节点上的内存占用不要太大，减少RDB导致的过多磁盘IO
- 适当提高repl_baklog的大小，发现slave宕机时尽快实现故障恢复，尽可能避免全量同步
- 限制一个master上的slave节点数量，如果实在是太多slave，则可以采用主-从-从链式结构，减少master压力
  - ![image-20221226113451909](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226113451909.png)

**简述全量同步和增量同步区别**？

- 全量同步：master将完整内存数据生成RDB，发送RDB到slave。后续命令则记录在repl_baklog，逐个发送给slave。
- 增量同步：slave提交自己的offset到master，master获取repl_baklog中从offset之后的命令给slave

**什么时候执行全量同步**？

- slave节点第一次连接master节点时
- slave节点断开时间太久，repl_baklog中的offset已经被覆盖时

**什么时候执行增量同步**？

- slave节点断开又恢复，并且在repl_baklog中能找到offset时

#### Redis哨兵

**来实现主从集群的自动故障恢复**：在redis主从集群上添加监听（哨兵）来判断集群的节点有没有宕机，主节点宕机会从子节点重新选出新的主节点

**监控**：Sentinel 会不断检查您的master和slave是否按预期工作

**自动故障恢复**：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主

**通知**：Sentinel充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端

##### 服务状态监控

Sentinel基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送ping命令：

- 主观下线：如果某sentinel节点发现某实例未在规定时间响应，则认为该实例**主观下线****(subjective down)**。**
- **客观下线：若超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例**客观下线(objective down)**。quorum值最好超过Sentinel实例数量的一半。

###### 选举新的master

一旦发现master故障，sentinel需要在salve中选择一个作为新的master，选择依据是这样的：

- 首先会判断slave节点与master节点断开时间长短，如果超过指定值（down-after-milliseconds * 10）则会排除该slave节点
- 然后判断slave节点的slave-priority值，越小优先级越高，如果是0则永不参与选举
- 如果slave-prority一样，则判断slave节点的offset值，越大说明数据越新，优先级越高
- 最后是判断slave节点的运行id（端口号）大小，越小优先级越高。

###### 故障转移

- sentinel给备选的slave1节点发送slaveof no one命令，让该节点成为master
- sentinel给所有其它slave发送slaveof 192.168.150.101 7002 命令，让这些slave成为新master的从节点，开始从新的master上同步数据。
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后会自动成为新的master的slave节点

##### Liunx搭建哨兵集群

参考 / 分布式缓存下 / Redis集群.md

##### RedisTemplate的哨兵模式

Spring的RedisTemplate底层利用lettuce实现了节点的感知和自动切换。

```
1.导入依赖
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-redis</artifactId> 
</dependency>

2.修改配置文件
spring:
  redis:
    sentinel:
      master: mymaster # 指定master名称
      nodes: # 指定redis-sentinel集群信息 
          - 192.168.150.101:27001
          - 192.168.150.101:27002
          - 192.168.150.101:27003

3.配置主从读写分离
@Bean
public LettuceClientConfigurationBuilderCustomizer configurationBuilderCustomizer(){
	return configBuilder -> configBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
这里的ReadFrom是配置Redis的读取策略，是一个枚举，包括下面选择：
MASTER：从主节点读取
MASTER_PREFERRED：优先从master节点读取，master不可用才读取replica
REPLICA：从slave（replica）节点读取
REPLICA _PREFERRED：优先从slave（replica）节点读取，所有的slave都不可用才读取master

```

##### 分片集群结构

###### 搭建分片集群

参考 / 分布式缓存下 / Redis集群.md

**主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：**

- 海量数据存储问题
- 高并发写的问题

**使用分片集群可以解决上述问题，分片集群特征：**

- 集群中有多个master，每个master保存不同数据
- 每个master都可以有多个slave节点
- master之间通过ping监测彼此健康状态
- 客户端请求可以访问集群任意节点，最终都会被转发到正确节点.

##### 散列插槽

![image-20221225224357312](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225224357312.png)

Redis如何判断某个key**应该在哪个实例**？

- 将16384个插槽分配到不同的实例
- 根据key的有效部分计算哈希值，对16384取余
- 余数作为插槽，寻找插槽所在实例即可

**如何将同一类数据固定的保存在同一个**Redis实例？

- 这一类数据使用相同的有效部分，例如key都以{typeId}为前缀

##### 集群伸缩

![image-20221225224452341](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225224452341.png)

###### 需求：向集群中添加一个新的master节点，并向其中存储 num = 10

- 启动一个新的redis实例，端口为7004
- 添加7004到之前的集群，并作为一个master节点
- 给7004节点分配插槽，使得num这个key可以存储到7004实例

这里需要两个新的功能：

- 添加一个节点到集群中
- 将部分插槽分配到新插槽

**解决方案**

创建一个文件夹：

```sh
mkdir 7004
```

拷贝配置文件：

```sh
cp redis.conf 7004
```

修改配置文件：

```sh
sed -i -e 's/6379/7004/g' 7004/redis.conf
```

启动

```sh
redis-server 7004/redis.conf
```

**添加新节点到redis集群**，执行命令：

```sh
redis-cli --cluster add-node  192.168.100.101:7004 192.168.100.101:7001

#说明：
#192.168.100.101:7004 要向集群中添加的新节点
#192.168.100.101:7001 原集群中的任意主节点
```

通过命令查看集群状态：

```sh
redis-cli -p 7001 cluster nodes
```

**转移插槽**

我们要将num存储到7004节点，因此需要先看看num的插槽是多少：

![image-20221226194923798](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226194923798.png)

我们可以将0~3000的插槽从7001转移到7004，命令格式如下：

```sh
redis-cli --cluster reshard 192.168.100.101:7001
```

具体命令如下：

建立连接：

![image-20221226195009802](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195009802.png)

![image-20221226195018925](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195018925.png)

![image-20221226195034148](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195034148.png)

![image-20221226195048876](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195048876.png)

![image-20221226195111884](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195111884.png)

###### 删除节点

假设要从集群中删除一个redis节点（7004节点），可以先将该实例的slot转移到其它节点，再删除本节点

```sh
#命令帮助
redis-cli --cluster help

#将7004节点的插槽转移到7001
redis-cli --cluster reshard 192.168.100.101:7001

#删除7004节点(注：末尾的参数为 7004实例的id)
redis-cli --cluster del-node 192.168.100.101:7001 624fc3f8da14910d84a8f307165581e56049a0ba
```



##### 故障转移

![image-20221225224706283](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225224706283.png)



###### 自动故障转移

当集群中有一个master宕机会发生什么呢？

(1) 集群原状态

```
redis-cli -p 7001 cluster nodes
```

(2) 监控集群状态

```sh
watch redis-cli -p 7001 cluster nodes
```

(3) 直接停止一个redis实例，例如7002：

```sh
redis-cli -p 7002 shutdown
```

(4) 再次查看集群状态（此时7002的slave会被提升为master节点）：

```sh
redis-cli -p 7001 cluster nodes
```

(5) 再次启动7002节点：

```sh
# 启动7002实例
redis-server 7002/redis.conf
# 查看集群状态
redis-cli -p 7001 cluster nodes
```

(6) 日志截图

1）首先是该实例与其它实例失去连接

2）然后是疑似宕机：

![image-20221226195231928](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195231928.png)

###### 手动故障转移

利用cluster failover命令可以手动让集群中的某个master宕机，切换到执行cluster failover命令的这个slave节点，实现无感知的数据迁移。其流程如下：

![image-20221225224724790](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221225224724790.png)

这种failover命令可以指定三种模式：

- 缺省：默认的流程，如图1~6歩
- force：省略了对offset的一致性校验
- takeover：直接执行第5歩，忽略数据一致性、忽略master状态和其它master的意见

**案例需求**：在7002这个slave节点执行手动故障转移，重新夺回master地位

步骤如下：

1）利用redis-cli连接7002这个节点

2）执行cluster failover命令

![image-20221226195355469](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221226195355469.png)

##### RedisTemplate访问分片集群

RedisTemplate底层同样基于lettuce实现了分片集群的支持，而使用的步骤与哨兵模式基本一致：

1.引入redis的starter依赖

2.配置分片集群地址

3.配置读写分离

与哨兵模式相比，其中只有分片集群的配置方式略有差异，如下：

```yaml
spring:
  redis:
    cluster:
      nodes: # 指定分片集群的每一个节点信息
        - 192.168.150.101:7001
        - 192.168.150.101:7002
        - 192.168.150.101:7003
        - 192.168.150.101:8001
        - 192.168.150.101:8002
        - 192.168.150.101:8003
```

# Redis延迟队列的实现

## 1.两个队列

- 当前队列：时间到的任务消息
- 未来队列：时间未到的任务消息

## 2.解决redis宕机数据丢失

1. 添加redis缓存时在数据库中同样添加一份
2. 监听当前队列时：消费掉一条数据将数据库备份的状态消息改为已消费状态
3. 同步时只同步未消费的数据

## 3.添加队列

判断是否大于当前系统时间，来区分是向未来队列添加数据还是向当前队列添加消息

## 4.定时刷新未来队列  和  redis管道优化

如果未来队列的中的数据符合添加到当前队列的条件，就将数据添加到当前队列，并删除符合条件的数据

```java
    /**
     * 定时同步未来任务队列数据到当前任务队列中
     */
    @Scheduled(cron = "*/10 * * * * ?")
    public void refreshCache(){

        //1.查询全部未来数据zset的key
        Set<String> keySet = cacheService.scan(ScheduleConstants.FUTURE + "*");

        
        if(!keySet.isEmpty()){
            // 2.遍历key的集合
            for (String zsetKey : keySet) {
                
               //3.根据当前系统时间尝试获取key对应的zset中的任务
                Set<String> taskSet = cacheService.zRangeByScore(zsetKey, 0, DateTime.now().getMillis());

                 //基于pipeline进行更新操作
            List<Object> objectList = cacheService.getstringRedisTemplate().executePipelined(new RedisCallback<Object>() {
                @Override
                public Object doInRedis(RedisConnection redisConnection) throws DataAccessException {

                    if (!taskSets.isEmpty()) {
                        //3.如果任务拉取到不为空，说明任务时间已到，则将任务从未来队列ZSET删除且添加到当前队列LIST

                        //将任务集合从未来队列删除
                        cacheService.zRemove(futureZsetKey, taskSets);

                        //通过未来队列的redisKey获取当前队列的redisKey（他们区别在于前缀不同，后缀相同）

                        //futureZsetKey未来队列的KEY格式：   topic:101:1
                        String[] arr = futureZsetKey.split(ScheduleConstants.FUTURE);
                        String redisPostFix = arr[1]; //获取redisKey共同的后缀

                        //topicListKey当前队列的KEY格式：   topic:101:1
                        String topicListKey = ScheduleConstants.TOPIC + redisPostFix;

                        //将任务集合添加到未来队列
                        cacheService.lLeftPushAll(topicListKey, taskSets);

                    }
                    cacheService.delete("cache:task:refresh:lock");
                    return null;
                }
            });
            log.info("[刷新任务]执行完毕..........");
            }
        }
    }
```



## 5.线程池优化

开启异步调用

添加配置类

```java
package com.heima.schedule.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.ThreadPoolExecutor;

/**
 * @author https://github.com/JiaWPeng
 * @version 1.0
 * @description:
 * @date 2023/1/4 08:13
 */

@Configuration
@EnableAsync//开启异步调用
public class ThreadPoolConfig {

    /**
     * 核心线程数（默认线程数）
     */
    private static final int corePoolSize = 10;
    /**
     * 最大线程数
     */
    private static final int maxPoolSize = 100;
    /**
     * 允许线程空闲时间（单位：默认为秒）
     */
    private static final int keepAliveTime = 10;
    /**
     * 缓冲队列数
     */
    private static final int queueCapacity = 500;

    /**
     * 默认异步线程池
     * @return
     */
    @Bean("taskExecutor")
    public ThreadPoolTaskExecutor taskExecutor(){
        ThreadPoolTaskExecutor pool = new ThreadPoolTaskExecutor();
        pool.setThreadNamePrefix(".............全局线程池..............");
        pool.setCorePoolSize(corePoolSize);
        pool.setMaxPoolSize(maxPoolSize);
        pool.setKeepAliveSeconds(keepAliveTime);
        pool.setQueueCapacity(queueCapacity);
        // 直接在execute方法的调用线程中运行
        pool.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        pool.initialize();//初始化线程池
        return pool;
    }
}


在方法上添加@Async("taskExecutor")就开启了异步
```

## 6.分布式锁

分布式锁：控制分布式系统有序的去对共享资源进行操作，通过互斥来保证数据的一致性。

![image-20230106214004301](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_21_40_4_image-20230106214004301.png)

![image-20230106214036888](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_21_40_36_image-20230106214036888.png)

### redis分布式锁

sexnx （SET if Not eXists） 命令在指定的 key 不存在时，为 key 设置指定的值。

这种加锁的思路是，如果 key 不存在则为 key 设置 value，如果 key 已存在则 SETNX 命令不做任何操作

- 客户端A请求服务器设置key的值，如果设置成功就表示加锁成功

- 客户端B也去请求服务器设置key的值，如果返回失败，那么就代表加锁失败

- 客户端A执行代码完成，删除锁

- 客户端B在等待一段时间后再去请求设置key的值，设置成功

- 客户端B执行代码完成，删除锁

- ```java
  /**
   * 加锁的封装方法
   *
   * @param name
   * @param expire
   * @return
   */
  public String tryLock(String name, long expire) {
      name = name + "_lock";
      String token = UUID.randomUUID().toString();
      RedisConnectionFactory factory = stringRedisTemplate.getConnectionFactory();
      RedisConnection conn = factory.getConnection();
      try {
  
          //参考redis命令：
          //set key value [EX seconds] [PX milliseconds] [NX|XX]
          Boolean result = conn.set(
                  name.getBytes(),
                  token.getBytes(),
                  Expiration.from(expire, TimeUnit.MILLISECONDS),
                  RedisStringCommands.SetOption.SET_IF_ABSENT //NX
          );
          if (result != null && result)
              return token;
      } finally {
          RedisConnectionUtils.releaseConnection(conn, factory,false);
      }
      return null;
  }
  ```

## 7.消费队列

在访问中创建根据特征来删除以符合条件的消息（数据）

使用定时任务来监听

```java
 /**
     * 从REDIS的当前任务队列拉取任务
     */
    @Scheduled(cron = "*/5 * * * * ?")
    @Override
    public void listenerPublishWmNews() {

        String lockName = "lock:poll:publish:news";
        String lock = cacheService.tryLock(lockName, 3 * 1000);

        if(StringUtils.isNotBlank(lock)){
            log.info("[listenerPublishWmNews]获取到分布式锁，开始执行拉取任务");

            //1.根据类型和优先级从Redis当前任务队列拉取任务
            Task task = scheduleClient.pollTask(TaskTypeEnum.WM_NEWS.getTaskType(), TaskTypeEnum.WM_NEWS.getPriority());

            if(task!=null){
                //2.从任务中获取wmNews
                byte[] wmNewsBytes = task.getParameters();
                WmNews wmNews = ProtostuffUtil.deserialize(wmNewsBytes, WmNews.class);

                int count = wmNewsService.count(Wrappers.<WmNews>lambdaQuery()
                        .eq(WmNews::getId, wmNews.getId())
                        .in(WmNews::getStatus, WmNews.Status.ADMIN_SUCCESS.getCode(), WmNews.Status.SUCCESS.getCode())
                );
                if(count>0){

                    //3.创建远程APP文章数据
                    wmNewsAuditService.saveOrUpdateApArticle(wmNews);


                    //4.修改自媒体文章状态为已发布
                    WmNews wmNewsDB = new WmNews();
                    wmNewsDB.setId(wmNews.getId());
                    wmNewsDB.setStatus(WmNews.Status.PUBLISHED.getCode());
                    wmNewsDB.setReason("已发布");
                    wmNewsService.updateById(wmNewsDB);
                }
            }
        } else {
            log.error("[listenerPublishWmNews]未获取到分布式锁"  );
        }


    }
}
```



## 微服务之间调用发送用户信息

模拟用户登录可以在Gateway网关中配置一个请求头

```yaml
	default-filters:  #默认过滤器(对所有请求都生效)
        - AddRequestHeader=authorization,2
```

#### 1.各个服务间都需要添加这三个类

1.定义线程类来保存用户信息

```java
// 单例模式之饿汉式加载
public class UserHolder {
    private static final ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setUser(Long userId){
        threadLocal.set(userId);
    }

    public static Long getUser(){
        return threadLocal.get();
    }

    public static void removeUser(){
        threadLocal.remove();
    }
}
```

2.自定义一个拦截器类

```java
public class UserInterceptor implements HandlerInterceptor {
    /**
     * 在业务处理器处理请求之前被调用。
     * 预处理，可以进行编码，安全控制，权限校验等处理
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("user用户拦截器......");
        String authorization = request.getHeader("authorization");
        if (StringUtils.isBlank(authorization)){
            response.setStatus(403);
            return false;
        }
        UserHandler.setUserId(Long.valueOf(authorization));
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHandler.removeUserId();
    }
}

```

3.自定义拦截器交给Spring管理（定义一个配置类实现WebMvcConfigurer）

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new UserInterceptor()).addPathPatterns("/**");
    }
}
```

#### 2.配置在FeignAPI模块

1.设置一个自定义拦截器（实现RequestInterceptor）

```java
// HttpServletRequest的类是在tomcat-embed-core
//		<dependency>
//          <groupId>org.apache.tomcat.embed</groupId>
//          <artifactId>tomcat-embed-core</artifactId>
//       /dependency>

import feign.RequestInterceptor;
import feign.RequestTemplate;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;
import javax.servlet.http.HttpServletRequest;

public class MyFeignInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate requestTemplate) {
//        log.info("feign拦截器,设置请求头authorization=2");
//        requestTemplate.header("authorization","2");

        //动态获取请求数据(注意:测试时需要通过浏览器访问订单接口 http://localhost:10010/order/123865420)
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        if (attributes == null) {
            return;
        }
        HttpServletRequest request = attributes.getRequest();
        String authorization = request.getHeader("authorization");
        System.out.println("authorization=" + authorization);
        log.info("feign拦截器,设置请求头authorization:{}", authorization);
        requestTemplate.header("authorization", authorization);
    }
}
```

2.交给Spring管理

```java
@Configuration
public class FeignConfig {
    @Bean
    public MyFeignInterceptor myFeignInterceptor(){
        return new MyFeignInterceptor();
    }
}
```

3.在resources目录下新建META-INF目录并新建spring.factories文件并添加以下内容

```json
// 实现自动装配
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.hmall.config.FeignConfig
```

到此完成各服务间的用户信息传递（**只要调用有拦截器的微服务就会获得用户信息，必须有用户信息，否则403，调用失败**）

## 分布式事务（seata）

**当在用一个微服务一个方法中调用本服务的另一个方法**；  

**另一个方法中调用了其他微服务的方法** ；

**开启了全局事务后** ；

**其他微服务报错了 在另一个方法中和其他微服务的数据会回滚**；

**另一个方法的调用方法数据不会回滚**。

#### 事务的ACID原则

- 原子性：事务中的所有操作，要么全部成功，要么全部失败
- 一致性：要保证数据库内部完整性约束、声明性约束
- 隔离性：：对同一资源操作的事务不能同时发生
- 持久性：对数据库做的一切修改将永久保存，不管是否出现故障

#### CAP定理

- Consistency（一致性）：用户访问分布式系统中的热议节点，得到的数据必须一致
- Availability（可用性）：用户访问集群中的任意健康节点，必须能得到响应，而不是超时或拒绝
- Partition tolerance（分区容错性）：Partition因为网络故障或其他原因导致分布式系统中的部分节点与其他节点失去连接，形成独立分区；Tolerance在集群出现分区是，整个系统也要持续对外提供服务

##### 简述CAP定理内容

- 分布式系统节点通过网络连接一定会出现分区问题（P）
- 当分区出现时，系统的一致性（C）和可用性（A）就无法同时满足

ES集群出现分区时，故障节点会被剔除集群，数据分片会重新分配到其它节点，保证数据一致。因此是低可用性，高一致性，属于CP.

#### BASE理论

- **Basically Available** **（基本可用）**：分布式系统在出现故障时，允许损失部分可用性，即保证核心可用。
- **Soft State（软状态）**：在一定时间内，允许出现中间状态，比如临时的不一致状态。
- **Eventually Consistent（最终一致性）**：虽然无法保证强一致性，但是在软状态结束后，最终达到数据一致。

**解决分布式事务的思想和模型**：

- 全局事务：整个分布式事务
- 分支事务：分布式事务中包含的每个子系统的事务
- 最终一致思想：各分支事务分别执行并提交，如果有不一致的情况，再想办法恢复数据
- 强一致思想：各分支事务执行完业务不要提交，等待彼此结果。而后统一提交或回滚.

#### Seata架构

Seata事务管理中三个重要角色：

- **TC（Transaction Coordinator）- 事务协调者**最终决定事务的成功与失败：维护全局和分支事务的状态，协调全局事务提交或回滚。
- **TM (Transaction Manager) -** **事务管理器：**定义全局事务的范围、开始全局事务、提交或回滚全局事务。
- **RM (Resource Manager) -** **资源管理器：**管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

![image-20221228103902701](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228103902701.png)

##### 部署TC访问

参考 seata的部署和集成.md

#### **Seata提供了四种不同的分布式事务解决方案：**

- XA模式：强一致性分阶段事务模式，牺牲了一定的可用性，无业务侵入
- TCC模式：最终一致的分阶段事务模式，有业务侵入
- AT模式：最终一致的分阶段事务模式，无业务侵入，也是Seata的默认模式
- SAGA模式：长事务模式，有业务侵入

##### 微服务注册seata

引入依赖

```xml
<!--seata-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <exclusions>
        <!--版本较低，1.3.0，因此排除-->
        <exclusion>
            <artifactId>seata-spring-boot-starter</artifactId>
            <groupId>io.seata</groupId>
        </exclusion>
    </exclusions>
</dependency>
<!--seata starter 采用1.4.2版本-->
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <version>${seata.version}</version>
</dependency>
```

修改配置

```yaml
seata:
  registry: # TC服务注册中心的配置，微服务根据这些信息去注册中心获取tc服务地址,参考tc服务registry.conf中的配置
    type: nacos
    nacos: # tc
      server-addr: 127.0.0.1:8848
      namespace: ""
      group: DEFAULT_GROUP
      application: seata-tc-server # tc服务在nacos中的服务名称
      username: nacos
      password: nacos
  tx-service-group: seata-demo # 事务组，根据这个获取tc服务的cluster名称
  service:
    vgroup-mapping: # 事务组与TC服务cluster的映射关系
      seata-demo: SH
```

开启全局事务

在调用微服务的处理方法上添加（微服务调用其他微服务的方法上）

```
@GlobalTransactional
```



##### XA

第一阶段：各个服务执行各自代码，执行完成后等待其他服务执行完成，运行状态交给TC，本条数据阻塞

第二阶段：由TC（事务协调者）来通知各服务事务**提交还是回滚**

![image-20221228144604350](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228144604350.png)

##### AT

![image-20221228152442129](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228152442129.png)

![image-20221228152510095](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228152510095.png)

各服务可以直接提交数据，会保存一个提交之前的快照，如果出现错误，则会按照快照恢复数据

如果出现脏写（上一个数据提交后，马上又有一个新的的数据来提交，但是上一个数据报错了需要恢复快照，现在就变成了一个脏数据），有一个全局锁来处理

###### 脏写问题

![image-20221228152535944](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228152535944.png)

###### 写隔离

![image-20221228152608742](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228152608742.png)

![image-20221228152632172](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228152632172.png)

###### AT模式的优点：

- 一阶段完成直接提交事务，释放数据库资源，性能比较好
- 利用全局锁实现读写隔离
- 没有代码侵入，框架自动完成回滚和提交

###### AT模式的缺点：

- 两阶段之间属于软状态，属于最终一致

- 框架的快照功能会影响性能，但比XA模式要好很多.

##### TCC

###### TCC模式原理

TCC模式与AT模式非常相似，每阶段都是独立事务，不同的是TCC通过人工编码来实现数据恢复。需要实现三个方法：

- Try：资源的检测和预留； 
- Confirm：完成资源操作业务；要求 Try 成功 且全局事务要提交，Confirm 一定要能成功。
- Cancel：预留资源释放，可以理解为try的反向操作。

![image-20221228163208866](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228163208866.png)

###### TCC工作模型图

![image-20221228163332458](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228163332458.png)

###### TCC的空回滚和业务悬挂

![image-20221228163524296](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228163524296.png)

###### TCC幂等

在 commit/cancel 阶段，因为 TC 没有收到分支事务的响应，需要进行重试，这就要分支事务支持幂等

判断记录事务的信息表中是否有该xid的信息，有信息并且状态为cancel状态，说明已经调用并且响应过，所有不用重试了

###### TCC业务分析

![image-20221228163623101](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228163623101.png)

###### 声明TCC接口

![image-20221228163838662](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/15699/image-20221228163838662.png)

###### TCC实现步骤

1.创建一个TccService

```java
@LocalTCC
public interface TccAccountService {

    /**
     * try资源预留
     * @param userId
     * @param money
     */
    @TwoPhaseBusinessAction(name = "deduct",commitMethod = "commit",rollbackMethod = "rollback")
    void deduct(@BusinessActionContextParameter(paramName = "userId")String userId,
                @BusinessActionContextParameter(paramName = "money")Integer money);

    /**
     * 业务确认
     * @param context
     * @return
     */
    boolean commit(BusinessActionContext context);

    /**
     * 业务回滚
     * @param context
     * @return
     */
    boolean rollback(BusinessActionContext context);
}

```

2.实现TccService

```java
@Slf4j
@Service
public class TccAccountServiceImpl implements TccAccountService {

    @Autowired
    private AccountMapper accountMapper;

    @Autowired
    private AccountFreezeMapper accountFreezeMapper;

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deduct(String userId, Integer money) {
        log.info("try....");
        String xid = RootContext.getXID();
        // 防止业务悬挂（业务阻塞了，已经超过等待时间了，业务已经回滚了，但是业务已经不阻塞了，所以这里判断一下）
        AccountFreeze accountFreeze1 = accountFreezeMapper.selectById(xid);
        if (accountFreeze1 != null && accountFreeze1.getState() == AccountFreeze.State.CANCEL) {
            return;
        }

        // 记录冻结金额和事务状态
        AccountFreeze accountFreeze = new AccountFreeze();
        accountFreeze.setXid(xid);
        accountFreeze.setUserId(userId);
        accountFreeze.setFreezeMoney(money);
        accountFreeze.setState(AccountFreeze.State.TRY);
        accountFreezeMapper.insert(accountFreeze);

        // 提交修改金额
        accountMapper.deduct(userId, money);
    }

    @Override
    public boolean commit(BusinessActionContext context) {
        log.info("commit....");
        String xid = context.getXid();

        int i = accountFreezeMapper.deleteById(xid);
        return i == 1;
    }

    @Override
    public boolean rollback(BusinessActionContext context) {
        log.info("rollback....");
        // 获取Xid
        String xid = context.getXid();
        AccountFreeze accountFreeze = accountFreezeMapper.selectById(xid);
        // 设置空回滚（在未执行try操作时先执行了cancel操作，这时cancel不能做回滚，就是空回滚）
        if (accountFreeze == null) {
            accountFreeze = new AccountFreeze();
            accountFreeze.setXid(xid);
            accountFreeze.setFreezeMoney(0);
            accountFreeze.setUserId(context.getActionContext("userId").toString());
            accountFreeze.setState(AccountFreeze.State.CANCEL);
            accountFreezeMapper.insert(accountFreeze);
            return true;
        }
        // 实现幂等
        if (accountFreeze.getState() == AccountFreeze.State.CANCEL) {
            return true;
        }

        // 恢复数据
        Integer freezeMoney = accountFreeze.getFreezeMoney();
        String userId = accountFreeze.getUserId();
        accountMapper.refund(userId, freezeMoney);

        // 修改try预留数据
        accountFreeze.setState(AccountFreeze.State.CANCEL);
        accountFreeze.setFreezeMoney(0);
        int count = accountFreezeMapper.updateById(accountFreeze);
        return count == 1;
    }
}
```

3.controller修改为TccService的业务实现逻辑（就会自动成为TCC）

```java
@RestController
@RequestMapping("/account")
public class AccountController {

    @Autowired
    private AccountService accountService;

    @Autowired
    private TccAccountService tccAccountService;

    @PutMapping("/{userId}/{money}")
    public ResponseEntity<Void> deduct(@PathVariable("userId") String userId, @PathVariable("money") Integer money){
        tccAccountService.deduct(userId, money);
        return ResponseEntity.noContent().build();
    }
}
```



###### TCC模式的每个阶段是做什么的？

- Try：资源检查和预留
- Confirm：业务执行和提交
- Cancel：预留资源的释放

###### TCC的优点是什么？

- 一阶段完成直接提交事务，释放数据库资源，性能好
- 相比AT模型，无需生成快照，无需使用全局锁，性能最强
- 不依赖数据库事务，而是依赖补偿操作，可以用于非事务型数据库

###### TCC的缺点是什么？

- 有代码侵入，需要人为编写try、Confirm和Cancel接口，太麻烦
- 软状态，事务是最终一致
- 需要考虑Confirm和Cancel的失败情况，做好幂等处理.

##### Saga

一阶段：直接提交本地事务

二阶段：成功则什么都不做；失败则通过编写补偿业务来回滚

##### 四种模式对比

|              | **XA**                         | **AT**                                       | **TCC**                                                      | **SAGA**                                                     |
| ------------ | ------------------------------ | -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **一致性**   | 强一致                         | 弱一致                                       | 弱一致                                                       | 最终一致                                                     |
| **隔离性**   | 完全隔离                       | 基于全局锁隔离                               | 基于资源预留隔离                                             | 无隔离                                                       |
| **代码侵入** | 无                             | 无                                           | 有，要编写三个接口                                           | 有，要编写状态机和补偿业务                                   |
| **性能**     | 差                             | 好                                           | 非常好                                                       | 非常好                                                       |
| **场景**     | 对一致性、隔离性有高要求的业务 | 基于关系型数据库的大多数分布式事务场景都可以 | •对性能要求较高的事务。  •有非关系型数据库(Redis)要参与的事务。 | •业务流程长、业务流程多  •参与者包含其它公司或遗留系统服务，无法提供  TCC  模式要求的三个接口 |

#### TC集群

使用nacos的配置：创建client.properties内容为以下

```shell
# 事务组映射关系
service.vgroupMapping.seata-demo=HZ

service.enableDegrade=false
service.disableGlobalTransaction=false
# 与TC服务的通信配置
transport.type=TCP
transport.server=NIO
transport.heartbeat=true
transport.enableClientBatchSendRequest=false
transport.threadFactory.bossThreadPrefix=NettyBoss
transport.threadFactory.workerThreadPrefix=NettyServerNIOWorker
transport.threadFactory.serverExecutorThreadPrefix=NettyServerBizHandler
transport.threadFactory.shareBossWorker=false
transport.threadFactory.clientSelectorThreadPrefix=NettyClientSelector
transport.threadFactory.clientSelectorThreadSize=1
transport.threadFactory.clientWorkerThreadPrefix=NettyClientWorkerThread
transport.threadFactory.bossThreadSize=1
transport.threadFactory.workerThreadSize=default
transport.shutdown.wait=3
# RM配置
client.rm.asyncCommitBufferLimit=10000
client.rm.lock.retryInterval=10
client.rm.lock.retryTimes=30
client.rm.lock.retryPolicyBranchRollbackOnConflict=true
client.rm.reportRetryCount=5
client.rm.tableMetaCheckEnable=false
client.rm.tableMetaCheckerInterval=60000
client.rm.sqlParserType=druid
client.rm.reportSuccessEnable=false
client.rm.sagaBranchRegisterEnable=false
# TM配置
client.tm.commitRetryCount=5
client.tm.rollbackRetryCount=5
client.tm.defaultGlobalTransactionTimeout=60000
client.tm.degradeCheck=false
client.tm.degradeCheckAllowTimes=10
client.tm.degradeCheckPeriod=2000

# undo日志配置
client.undo.dataValidation=true
client.undo.logSerialization=jackson
client.undo.onlyCareUpdateColumns=true
client.undo.logTable=undo_log
client.undo.compress.enable=true
client.undo.compress.type=zip
client.undo.compress.threshold=64k
client.log.exceptionRate=100
```

将seata.conf的配置文件中的cluster修改为HZ

```json
registry {
  # tc服务的注册中心类型，这里选择nacos，也可以是eureka、zookeeper等
  type = "nacos"

  nacos {
    # seata tc 服务注册到 nacos的服务名称，可以自定义
    application = "seata-tc-server"
    serverAddr = "127.0.0.1:8848"
    group = "DEFAULT_GROUP"
    namespace = ""
    cluster = "HZ"
    username = "nacos"
    password = "nacos"
  }
}

config {
  # 读取tc服务端的配置文件的方式，这里是从nacos配置中心读取，这样如果tc是集群，可以共享配置
  type = "nacos"
  # 配置nacos地址等信息
  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "DEFAULT_GROUP"
    username = "nacos"
    password = "nacos"
    dataId = "seataServer.properties"
  }
}
```

启动命令

```
seata-server.bat -p 8092  默认是端口号8091
```

将各微服务的配置文件读取nacos的client.properties，添加以下配置

```yaml
seata:
  config:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      namespace: ""
      group: DEFAULT_GROUP #也可以自定义其它名称，如: SEATA_GROUP (需要在创建client.properties时指定)
      data-id: client.properties # 读取nacos配置文件
      username: nacos
      password: nacos
```

# Kafka消息队列

![image-20230106090525602](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_9_6_32_6_9_5_25_image-20230106090525602.png)



Kafka对于zookeeper是强依赖，保存kafka相关的节点数据，所以安装Kafka之前必须先安装zookeeper

先启动zookeeper

``````yacas
docker start zookeeper
``````

再启动kafka

``````yacas
docker start kafka
``````

以下安装命令仅供参考：

- Docker安装zookeeper

下载镜像：

```shell
docker pull zookeeper:3.4.14
```

创建容器

```shell
docker run -d --name zookeeper -p 2181:2181 zookeeper:3.4.14
```

- Docker安装kafka

下载镜像：

```shell
docker pull wurstmeister/kafka:2.12-2.3.1
```

创建容器

```shell
docker run -d --name kafka \
--env KAFKA_ADVERTISED_HOST_NAME=192.168.200.130 \
--env KAFKA_ZOOKEEPER_CONNECT=192.168.200.130:2181 \
--env KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.200.130:9092 \
--env KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
--env KAFKA_HEAP_OPTS="-Xmx256M -Xms256M" \
--net=host wurstmeister/kafka:2.12-2.3.1
```

关键概念：

- producer：主题生产者
- topic：每一类的消息称为一个主题（topic）
- broker：已生产的消息保存在一组服务器中，称之为Kafka集群，集群中的每一个服务器都是一个代理（broker），消费者可以订阅一个或多个主题
- consumer：主题消费者

## springboot快速集成

![image-20230106075553408](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_7_55_53_image-20230106075553408.png)

- 生产者发送消息，多个消费者只能有一个消费者接收到消息
- 生产者发送消息，多个消费者都可以接收到消息

（1）创建kafka-demo项目，导入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- kafkfa -->
    <dependency>
        <groupId>org.springframework.kafka</groupId>
        <artifactId>spring-kafka</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.apache.kafka</groupId>
                <artifactId>kafka-clients</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
    </dependency>
</dependencies>
```

（2）在resources下创建文件application.yml

```yaml
server:
  port: 9991
spring:
  application:
    name: kafka-demo
  kafka:
    bootstrap-servers: 192.168.200.130:9092
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      group-id: ${spring.application.name}-test
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
```

3.生产者代码

```java
package com.heima.kafka.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

/**
 * 生产者controller
 */
@RestController
public class ProducerController {

    @Autowired
    private KafkaTemplate kafkaTemplate;

    /**
     * 生产消息的方法
     */
    @GetMapping("/send/{key}/{value}")
    public String send(@PathVariable("key") String key,@PathVariable("value") String value){
        kafkaTemplate.send("testTopic",key, value);
        return "ok";
    }
}
```

4.费者代码

```java
package com.heima.kafka.listener;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

import java.util.Optional;

/**
 * kafka消费者监听器
 */
@Component
public class ConsumerListener {


    /**
     * 消费消息的方法
     * @param consumerRecord
     */
    @KafkaListener(topics = "testTopic")
    public void receiveMsg(ConsumerRecord<String,String> consumerRecord){
//        if(consumerRecord!=null){
//            System.out.println(consumerRecord.key() + "==>" + consumerRecord.value());
//        }
        Optional<ConsumerRecord<String, String>> optional = Optional.ofNullable(consumerRecord);
        optional.ifPresent(x->{
            System.out.println(x.key() +"==>" + x.value());
        });
    }
}
```

#### 消费组测试

- 同一个消费组下消费者，只能有一个消费者收到消息（一对一）
- 不同消费组下消费者，每个组内起码一个消费者能收到消息（1对多，广播效果）

## Kafka高可用

![image-20230106075919862](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_7_59_19_image-20230106075919862.png)

![image-20230106075950847](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_7_59_50_image-20230106075950847.png)

![image-20230106080022447](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_0_22_image-20230106080022447.png)

- 同组中的消费者：只能一个消费者能消费
- 不同组中的消费者：所有消费者都可以消费

## Kafka分区和顺序消费问题

### 分区机制（Partition设计）

![image-20230106080228921](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_2_29_image-20230106080228921.png)

- 同一个Topic包含不同的Partition分区 存储在不同的机器，一个分区就是一个提交日志
-  消息以追加的方式写入分区，然后以先进先出的顺序读取。
- Partition分区的好处是可以并行读和写，保证kafka高吞吐、高性能、高可用
- 每个Partition针对每一个消费组设计了独立的**偏移量(offset)**

**在kafka容器内/opt/kafka_2.12-2.3.1/config/server.properties中通过配置项num.partitions来指定新建Topic的默认Partition数量，默认是1**

### 不同消费组可以重复消费原因？

分区针对消费组具有独立的偏移量(offset)设计

![image-20230106080529921](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_5_30_image-20230106080529921.png)

**offset**：任何发布到partition的消息会被追加到log日志文件尾部（ 由partition的leader记录，并由partition的follower同步），每条消息在文件中的位置就被称为offset（偏移量），offset是一个long型数字，是按照1自增的，它唯一标记一条消息。消费者通过（offset、partition、topic）跟踪记录。可在配置文件server.properties配置log.dirs指定log日志文件目录。

offset（偏移量）指的是存在消息日志文件中消息的唯一标识；唯一的长整形数值从0开始按照1自增



**topic_consumer_offsets** : 消费者组的位移提交到自带的topicconsumer_offsets里面,当有消费者第一次消费kafka数据时就会自动创建，它的副本数不受集群配置的topic副本数限制，分区数默认50（可以配置），默认压缩策略为compact。 当 Consumer 重启后，自动读取topic__consumer_offsets中位移数据，从而在上次消费截止的地方继续消费

![image-20230106080614901](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_6_15_image-20230106080614901.png)

![image-20230106080637972](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_6_38_image-20230106080637972.png)

### Kafka的消费者偏移量查看

```
/opt/kafka_2.12-2.3.1/bin/kafka-consumer-groups.sh --bootstrap-server 192.168.200.130:9092 --describe --group kafka-demo-test2  注意最后一个值是消费者群组名
```

![image-20230106080754986](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_7_55_image-20230106080754986.png)

### Zookeeper上查看Kafka状态信息

```shell
登录到zookeeper容器：docker exec –it zookeeper /bin/bash
ZK客户端命令连接2181端口：/zookeeper-3.4.14/bin/zkCli.sh

```

![image-20230106080841451](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_8_41_image-20230106080841451.png)

ls命令查看broker、topic、consumer等状态信息

![image-20230106080854250](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_8_54_image-20230106080854250.png)

![image-20230106080904480](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_9_4_image-20230106080904480.png)

### kafka能否保证顺序消费？

**单机环境**：一个主题对应一个分区。单个分区内的消息写入有有序的，所以针对单个分区的消费是可以保证消费顺序的。  

**集群环境**：一个主题对应多个分区。例如连续进行如下5个操作，通过Kafka生产5条消息，由于Kafka是并行写消息并已追加的方式写入到多个不同分区，所以在整个主题范围内无法保证写的顺序，最后消费端数据消费也是无法保证顺序的。

![image-20230106080954975](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_9_55_image-20230106080954975.png)

![image-20230106081004130](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_10_4_image-20230106081004130.png)

![image-20230106081013605](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_10_13_image-20230106081013605.png)

### 分区策略：决定生产者将消息发送到哪个分区的算法

![image-20230106081044404](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_10_44_image-20230106081044404.png)

![image-20230106081133814](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_11_33_image-20230106081133814.png)

![image-20230106081142286](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_11_42_image-20230106081142286.png)

测试环境就一台机器，默认是分区0，设置分区0可以，但设置其他分区测试会失败。

### 小结：

高可用，高吞吐，高性能

![image-20230106081203532](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_12_3_image-20230106081203532.png)

## Kafka高可用设计

### 生产端发送消息类型

**发送并忘记**：发送并忘记(不关心消息是否正常到达，对返回结果不做任何判断处理)，效率高，无法保证消息可靠性。



**同步发送**：使用send()方法发送，它会返回一个Future对象，调用get()方法进行等待kafka响应，就可以知道消息是否发送成功。



**异步发送**：调用send()方法，并指定一个回调函数，服务器在返回响应时调用函数。没有阻塞等待效果，效率更高。

### 生产端acks（Acknowledgments）确认应答机制

![image-20230106081419229](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_14_19_image-20230106081419229.png)

| **确认机制**     | **说明**                                                     |
| ---------------- | ------------------------------------------------------------ |
| acks=0           | 生产者不需要等待服务器任务响应，不保证数据可靠性（可能丢失数据），性能吞吐最高 |
| acks=1（默认值） | 当leader节点持久化消息后，生产者才会得到服务器响应，可靠性和性能吞吐适中 |
| acks=all         | 所有ISR节点持久完消息后，生产者才会得到服务器响应，数据可靠性最高，性能吞吐会降低 |

![image-20230106081453151](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_14_53_image-20230106081453151.png)

### 生产者重试机制

![image-20230106081511634](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_15_11_image-20230106081511634.png)

![image-20230106081516845](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_15_16_image-20230106081516845.png)

生产者从服务器收到的错误有可能是临时性错误，在这种情况下，retries参数的值决定了生产者可以重发消息的次数，如果达到这个次数，生产者会放弃重试返回错误，默认情况下，**生产者会在每次重试之间等待100ms**

### 服务端：主从备份机制(Replication）

 kafka 为了提高 partition 的可靠性而提供了副本的概念（Replica）,通过副本机制来实现冗余备份。每个分区可以有多个副本，并且在副本集合中会存在一个leader 的副本，所有的读写请求都是由 leader 副本来进行处理。

Kafka 定义了两类副本：

- 领导者副本（Leader Replica）
- 追随者副本（Follower Replica）

![image-20230106081739918](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_17_40_image-20230106081739918.png)

#### 备份机制(Replication)-ISR

![image-20230106081825212](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_18_25_image-20230106081825212.png)

- 我们可以认为，副本集会存在一主多从的关系。一般情况下，同一个分区的多个副本会被均匀分配到集群中的不同 broker 上，当 leader 副本所在的 broker 出现故障后，可以重新选举新的 leader 副本继续对外提供服务。通过这样的副本机制来提高 kafka 集群的可用性。
- 所有与leader保持一定程度同步的副本（包括Leader）组成ISR（In-Sync Replicas）
- 如果leader失效后，需要选出新的leader，选举的原则如下：
  -    第一：选举时优先从ISR中选定，因为这个列表中follower的数据是与leader同步的
  - 第二：如果ISR列表中的follower都不行了，就只能从其他follower中选取（极端情况）

#### 顺序写磁盘（相对于随机写性能提高百倍以上）

当broker接收到producer发送过来的消息时，需要根据消息的主题和分区信息，将该消息写入到该分区当前最后的segment文件中，文件的写入方式是追加写。

由于是对segment文件追加写，故实现了对磁盘文件的顺序写，避免磁盘随机写时的磁盘寻道的开销，同时由于是追加写，故写入速度与磁盘文件大小无关，具体如图

![image-20230106081938492](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_19_38_image-20230106081938492.png)

### 消费端重复消费生产原因

![image-20230106082016808](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_20_16_image-20230106082016808.png)

1 生产者如果网络抖动相关问题，可能重复发送消息

2 消费者宕机、重启或者被强行kill进程，导致消费者消费的offset没有提交

3 kafka消费端会每隔**5秒自动提交消费偏移量**(auto.commit.interval.ms)**如果网络问题没来及提交，其他消费者会重复消费消息**

4 kafka消费者会每隔10秒向服务端发送心跳（session.timeout.ms）表明还活着，否则服务端认为消费者离组会触发重平衡，重平衡rebalance也会造成消息重复消费

#### 重平衡Rebalance造成重复消费

kafka**不会像其他JMS队列**那样需要**得到消费者的确认**，消费者可以使用kafka来追踪消息在分区的位置（偏移量）

消费者会往一个叫做_consumer_offset的特殊主题发送消息，消息里包含了每个分区的偏移量。如果消费者发生崩溃或有新的消费者加入群组，就会触发重平衡

![image-20230106082125887](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_21_25_image-20230106082125887.png)

#### 消费者消息重复如何解决

![image-20230106082206161](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_22_6_image-20230106082206161.png)

### 高可用设计之吞吐优化

**1增加分区数量、2异步发送消息、3消息压缩支持、4、批量发送**

**默认情况下， 消息发送时不会被压缩。**

![image-20230106082251404](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_22_51_image-20230106082251404.png)

| **压缩算法** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| snappy       | 占用较少的  CPU，  却能提供较好的性能和相当可观的压缩比， 如果看重性能和网络带宽，建议采用 |
| lz4          | 占用较少的 CPU， 压缩和解压缩速度较快，压缩比也很客观        |
| gzip         | 占用较多的  CPU，但会提供更高的压缩比，网络带宽有限，可以使用这种算法 |

使用压缩可以降低网络传输开销和存储开销，而这往往是向 Kafka 发送消息的瓶颈所在。

**批量发送可以提高Kafka的吞吐**

![image-20230106082458725](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_24_58_image-20230106082458725.png)

| **参数**      | **说明**                                                     |
| ------------- | ------------------------------------------------------------ |
| batch-size    | Kafka的生产者发送数据到服务器，不是来一条就发一条，而是经过缓冲的，默认批量发送数据依次16K |
| buffer-memory | 缓存池大小，默认32M                                          |

## Kafka消息删除机制

- 对于传统的message queue而言，一般会删除已经被消费的消息，而Kafka集群会保留所有的消息，无论其被消费与否。当然，因为磁盘限制，不可能永久保留所有数据（实际上也没必要）。
- 两种策略删除旧数据。一是基于时间，二是基于Partition文件大小。
- 例如可以通过配置$KAFKA_HOME/config/server.properties，让Kafka删除一周前的数据，也可在Partition文件超过1GB时删除旧数据。比如如下默认删除策略：

![image-20230106082539567](https://gitlab.com/JWPeng/image/-/raw/main/pictures/2023/01/6_8_25_39_image-20230106082539567.png)
