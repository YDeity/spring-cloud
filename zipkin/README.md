## sleuth+zipkin 实现链路追踪服务

pom 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>
	<artifactId>zipkin</artifactId>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.ys</groupId>
		<artifactId>spring-cloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
		<relativePath>../pom.xml</relativePath>
	</parent>

	<dependencies>
		<dependency>
			<groupId>io.zipkin.java</groupId>
			<artifactId>zipkin-autoconfigure-ui</artifactId>
		</dependency>
		<dependency>
			<groupId>io.zipkin.java</groupId>
			<artifactId>zipkin-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
	</dependencies>

</project>
```



application 配置文件

```yaml
server:
  port: 8890

spring:
  application:
    name: zipkin-server

eureka:
  client:
    serviceUrl:
      defaultZone: http://ys:ys1234@${eureka.host:localhost}:${eureka.port:8761}/eureka/ # 配置要注册到 Eureka Server 的地址
  instance:
    prefer-ip-address: true
    instance-id: ${spring.application.name}:${spring.cloud.client.ipAddress}:${spring.application.instance_id:${server.port}}
```



启动类

```java
@SpringBootApplication
@EnableZipkinServer
@EnableEurekaClient
public class ZipkinApplication {
    public static void main(String[] args){
        SpringApplication.run(ZipkinApplication.class,args);
    }
}
```



Dockerfile 

```dockerfile
FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/zipkin-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/zipkin-0.0.1-SNAPSHOT.jar"]

EXPOSE 8890
```



### 在其他微服务中添加服务

添加依赖

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```



添加配置文件

```yaml
spring:
  zipkin:
    base-url: http://localhost:9411 # 这个地址是链路服务的地址
    # 这块zipkin的地址是硬编码的，目前还没发现怎么从服务注册中心eureka上动态获取，以后有解决方案
  sleuth:
    sampler:
      percentage: 1.0
```



访问链路服务地址就可以看见注册到链路服务中的微服务了。