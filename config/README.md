## Config Server 配置服务

配置服务器为本地服务器，默认为 git 

pom 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<artifactId>config</artifactId>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.ys</groupId>
		<artifactId>spring-cloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
	</dependencies>

</project>
```



bootstrap.yml 配置，bootstrap 的配置默认是比 application.yml 配置优先的

```yaml
spring:
  application:
    name: config
  profiles:
    active: native # 配置服务器为本地服务器,默认为 Git
    
eureka:
  instance:
    non-secure-port: ${server.port:8080} # 非 SSL 端口,若环境变量中 server.port 有值 ,则使用环境变量的值,没有则使用 8080
    prefer-ip-address: true
    metadata-map:
      instanceId: ${spring.application.name}:${random.value}
  client:
    serviceUrl:
      defaultZone: http://ys:ys1234@${eureka.host:localhost}:${eureka.port:8761}/eureka/ # 配置要注册到 Eureka Server 的地址
```



application.yml

```yaml
server:
  port: 8888

spring:
  cloud:
    config:
      server:
        native:
          search-locations: classpath:/config # 配置其他应用所需的配置文件位于类路径下的 config 目录下
```



启动类

```java
@SpringBootApplication
@EnableConfigServer
@EnableEurekaClient
public class ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class, args);
    }
}
```



Dockerfile

```dockerfile
FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/config-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/config-0.0.1-SNAPSHOT.jar"]

EXPOSE 8888
```

