FROM java:8-jre
MAINTAINER YDeity <meetliuyuan@163.com>

ADD ./jar/zipkin-0.0.1-SNAPSHOT.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/zipkin-0.0.1-SNAPSHOT.jar"]

EXPOSE 8890