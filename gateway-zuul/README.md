## Zuul 构建网关

Zuul是Netflix开源的微服务网关，他可以和Eureka,Ribbon,Hystrix等组件配合使用。Zuul组件的核心是一系列的过滤器，这些过滤器可以完成以下功能：

- 身份认证和安全: 识别每一个资源的验证要求，并拒绝那些不符的请求
- 审查与监控：
- 动态路由：动态将请求路由到不同后端集群
- 压力测试：逐渐增加指向集群的流量，以了解性能
- 负载分配：为每一种负载类型分配对应容量，并弃用超出限定值的请求
- 静态响应处理：边缘位置进行响应，避免转发到内部集群
- 多区域弹性：跨域AWS Region进行请求路由，旨在实现ELB(ElasticLoad Balancing)使用多样化



Spring Cloud对Zuul进行了整合和增强。目前，Zuul使用的默认是Apache的HTTP Client，也可以使用Rest Client，可以设置`ribbon.restclient.enabled=true`.



pom 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <artifactId>gateway-zuul</artifactId>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.ys</groupId>
        <artifactId>spring-cloud</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <dependencies>
        <!--zuul网关 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zuul</artifactId>
        </dependency>
        <!--zuul限流 -->
        <dependency>
            <groupId>com.marcosbarbero.cloud</groupId>
            <artifactId>spring-cloud-zuul-ratelimit</artifactId>
            <version>1.5.0.RELEASE</version>
        </dependency>
        <!--feign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-feign</artifactId>
        </dependency>
        <!--swagger ui-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>${swagger2.version}</version>
        </dependency>
        <!--zipkin-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
    </dependencies>

</project>
```



boostrap 配置文件

```yaml
spring:
  application:
    name: gateway-zuul

eureka:
  instance:
    prefer-ip-address: true
    lease-renewal-interval-in-seconds: 5
    lease-expiration-duration-in-seconds: 20
    non-secure-port: ${server.port:8089} # 非 SSL 端口,若环境变量中 server.port 有值 ,则使用环境变量的值,没有则使用 8080
    metadata-map:
      instanceId: ${spring.application.name}:${random.value}
  client:
    serviceUrl:
      defaultZone: http://ys:ys1234@${eureka.host:localhost}:${eureka.port:8761}/eureka/ # 配置要注册到 Eureka Server 的地址
    registry-fetch-interval-seconds: 10
```



application 配置文件

```yaml
server:
  port: 8889
```



启动类

```java
@SpringBootApplication
@EnableZuulProxy
@EnableEurekaClient
public class ZuulApplication {
    public static void main(String[] args){
        SpringApplication.run(ZuulApplication.class,args);
    }
}
```



Dockerfile

```dockerfile
FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/gateway-zuul-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/gateway-zuul-0.0.1-SNAPSHOT.jar"]

EXPOSE 8889
```

