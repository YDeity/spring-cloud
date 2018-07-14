## Turbine hystrix 监控

访问地址：http://ip:port/hystrix 

pom 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <artifactId>turbine-monitor</artifactId>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.ys</groupId>
        <artifactId>spring-cloud</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-turbine</artifactId>
        </dependency>
    </dependencies>

</project>
```



bootstrap 配置

```yaml
spring:
  application:
    name: turbine-monitor
    
eureka:
  instance:
    non-secure-port: ${server.port:8889}
  client:
    service-url:
      defaultZone: http://ys:ys1234@${eureka.host:localhost}:${eureka.port:8761}/eureka/
```



application 配置

```yaml
server:
  port: 8889
```



启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableHystrixDashboard
@EnableTurbine
public class MonitorApplication {
    public static void main(String[] args) {
        SpringApplication.run(MonitorApplication.class, args);
    }
}
```



Dockerfile

```dockerfile
FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/turbine-monitor-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/turbine-monitor-0.0.1-SNAPSHOT.jar"]

EXPOSE 8889
```

