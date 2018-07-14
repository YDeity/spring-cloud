## Eureka 服务治理

application.yml 配置文件

```yaml
server:
  port: 8761

spring:
  application:
    name: eureka-discovery

security:
  basic:
    enabled: true        # 开启基于 HTTP basic 的认证
  user:
    name: ys              # 配置登录的账号
    password: ys1234      # 配置登录的密码

eureka:
  client:
    fetch-registry: false # 表示是否从 Eureka Server 获取信息,默认为 true
    register-with-eureka: false  # 表示是否将自己注册到 Eureka Server,默认为 true
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ # 设置 Eureka Server 交互的地址
  instance:
    hostname: localhost
  server:  # 配置属性，但由于 Eureka 自我保护模式以及心跳周期长的原因，经常会遇到 Eureka Server 不剔除已关停的节点的问题
    enable-self-preservation: false # 关闭自我保护模式
    eviction-interval-timer-in-ms: 5000 # 设置心跳检测时间
```



pom.xml 配置文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<artifactId>eureka-discovery</artifactId>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.ys</groupId>
		<artifactId>spring-cloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
	</dependencies>
	
</project>
```



启动类

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```



Dockerfile 

```dockerfile
FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/eureka-discovery-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/eureka-discovery-0.0.1-SNAPSHOT.jar"]

EXPOSE 8761
```

