<p align="center">
  <img src="https://img.shields.io/badge/Spring%20Boot-1.5.9-blue.svg" alt="Downloads">
</p>



<h2 align="center">Spring Cloud Demo</h2> 



> Spring Cloud Demo 涵盖各大组件使用方法，此 Demo 为探索式开发，不足之处望大虾指点。



> 一版本整合各大组件，每添加一个组件发布一个版本。内附 Dockerfile 创建 docker 镜像文件



> 此项目为 maven 聚合项目



pom.xml 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.ys</groupId>
	<artifactId>spring-cloud</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>pom</packaging>
	
	<modules>
		<module>eureka-discovery</module>
	</modules>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Edgware.RELEASE</spring-cloud.version>
	</properties>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
	</parent>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
			<dependency>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-test</artifactId>
				<scope>test</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

